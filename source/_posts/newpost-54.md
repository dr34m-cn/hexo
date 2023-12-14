---
title: 在Python中使用onvif管理摄像头，包括设备发现，获取RTSP地址，获取设备信息，截图，云台控制与缩放，设置时间
tags:
  - Python
  - Onvif
index_img: /assets/headImg/onvif&python.png
date: 2023-12-14 15:26:20
---

# 目标

使用Python对支持`ONVIF`的设备进行控制，包括设备发现，获取`RTSP地址`，获取设备信息，截图，云台控制与缩放，设置时间等。

以下均基于`Python3.11`，其他版本可能略有不同。

<!--more-->

参考文档：[wsdl文档](https://www.onvif.org/onvif/ver20/util/operationIndex.html)、[Python API](https://pypi.org/project/onvif/)

本文严重参考并感谢：[《ONVIF with python》](https://blog.csdn.net/u010881576/article/details/116885774)

本文在上边的基础上，支持了多画面多码流的摄像头，并对更多功能做了详细说明



# 实现

### 一、安装包

```shell
pip install onvif-zeep
# WSDiscovery 用于设备发现，不用则不再需要安装
pip install WSDiscovery
# requests 用于截图获取，不用则不需要安装
pip install requests
```

### 二、使用

基本类

```python
import base64
from itertools import groupby
import pytz
import datetime
import requests
from onvif import ONVIFCamera
from requests.auth import HTTPDigestAuth
from wsdiscovery.discovery import ThreadedWSDiscovery as WSDiscovery
from zeep.helpers import serialize_object


def checkPwdAndGetCam(ip, port, usr, pwd):
    try:
        cam = ONVIFCamera(ip, port, usr, pwd)
        media = cam.create_media_service()
        profiles = media.GetProfiles()
    except Exception as e:
        if 'timed out' in str(e):
            raise Exception("连接超时，请检查地址以及端口是否正确")
        elif 'HTTPConnectionPool' in str(e):
            raise Exception(
                "连接失败，请检查地址以及端口是否正确。"
                "<br/><br/><front style='color: #aaa;'>异常信息：%s</front>" % str(e))
        else:
            raise Exception(
                "请检查账号密码是否正确；<br/>如果您确认账号密码正确，摄像头时间与服务器时间差异过大可能导致验证失败。"
                "<br/><br/><front style='color: #aaa;'>异常信息：%s</front>" % str(e))
    return {
        'cam': cam,
        'media': media,
        'profiles': profiles
    }


class OnvifClient:
    def __init__(self, ip, port: int, usr, pwd, token=None, sourceToken=None):
        """
        初始化参数
        :param ip: 
        :param port: 
        :param usr: 
        :param pwd: 
        :param sourceToken: 每个画面会有一个独立的sourceToken，通常一个摄像头只有一个画面，有些红外、双摄之类的摄像头会有多个画面
        :param token: 每个码流都会有一个独立的token，通常一个画面会有2个或更多码流，例如主码流&辅码流
        """
        self.usr = usr
        self.pwd = pwd
        result = checkPwdAndGetCam(ip, port, usr, pwd)
        self.cam = result['cam']
        self.ptz = self.cam.create_ptz_service() if bool(self.cam.devicemgmt.GetCapabilities().PTZ) else None
        self.media = result['media']
        self.imaging = self.cam.create_imaging_service()
        self.profiles = result['profiles']
        # 如果没传token，默认使用第一个token
        self.token = token if token is not None else self.profiles[0].token
        # 如果没传sourceToken，默认使用第一个sourceToken
        self.sourceToken = sourceToken if sourceToken is not None else self.profiles[0].VideoSourceConfiguration.SourceToken

    def get_rtsp(self):
        """
        获取RTSP地址等，将会获取所有画面的每一个码流的地址、截图等信息
        参考文档：https://www.onvif.org/onvif/ver10/media/wsdl/media.wsdl#op.GetStreamUri
        """
        result = []
        StreamSetup = {'Stream': 'RTP-Unicast', 'Transport': {'Protocol': 'RTSP'}}
        for profile in self.profiles:
            obj = self.media.create_type('GetStreamUri')
            obj.StreamSetup = StreamSetup
            obj.ProfileToken = profile.token
            res_uri = self.media.GetStreamUri(obj)['Uri']
            if 'rtsp://' in res_uri and '@' not in res_uri:
                res_uri = res_uri.replace('rtsp://', 'rtsp://%s:%s@' % (self.usr, self.pwd))
            result.append({
                'source': profile.VideoSourceConfiguration.SourceToken,
                'uri': res_uri,
                'token': profile.token,
                'videoEncoding': profile.VideoEncoderConfiguration.Encoding,
                'Resolution': serialize_object(profile.VideoEncoderConfiguration.Resolution),
                'img': self.snip_image(profile.token)
            })
        sortedResult = sorted(result, key=lambda d: d['source'])
        groupData = groupby(sortedResult, key=lambda x: x['source'])
        return [{'source': key, 'data': [item for item in group]} for key, group in groupData]

    def snip_image(self, token=None):
        """
        截图，如果在浏览器上访问，可在img的src填入[data:image/jpeg;base64,%s]，%s处填写return值
        参考文档：https://www.onvif.org/onvif/ver10/media/wsdl/media.wsdl#op.GetSnapshotUri
        :param token:
        :return: base64转码之后的图片
        """
        token = token if token is not None else self.token
        res = self.media.GetSnapshotUri({'ProfileToken': token})
        auth = HTTPDigestAuth(self.usr, self.pwd)
        rsp = requests.get(res.Uri, auth=auth)
        return base64.b64encode(rsp.content).decode('utf-8')

    def get_deviceInfo(self):
        """
        获取设备信息
        参考文档：https://www.onvif.org/onvif/ver10/device/wsdl/devicemgmt.wsdl#op.GetDeviceInformation
        :return: 设备信息，包括名称-Model、厂家-Manufacturer、固件版本-FirmwareVersion、序列号-SerialNumber、硬件ID-HardwareId
        """
        return serialize_object(self.cam.devicemgmt.GetDeviceInformation())

    def ptz_move(self, Velocity=None, token=None):
        """
        PTZ控制移动
        参考文档：https://www.onvif.org/onvif/ver20/ptz/wsdl/ptz.wsdl#op.ContinuousMove
        :param token: 移动设备的token
        :param Velocity: 可选参数，不传表示停止移动
        """
        token = token if token is not None else self.token
        if self.ptz is None:
            if Velocity is not None:
                # 只在移动时展示不支持，以免频繁打扰
                raise Exception("该设备不支持PTZ控制")
        else:
            if Velocity is None:
                self.ptz.Stop({'ProfileToken': token})
            else:
                request = self.ptz.create_type('ContinuousMove')
                request.ProfileToken = token
                request.Velocity = Velocity
                self.ptz.ContinuousMove(request)

    def focus_move(self, speed=None, token=None):
        """
        聚焦
        参考文档：https://www.onvif.org/onvif/ver20/imaging/wsdl/imaging.wsdl#op.Move
        :param token: VideoSourceToken
        :param speed: 0.02：聚焦+，拉近；-0.02：聚焦-，拉远；None：停止聚焦
        """
        token = token if token is not None else self.sourceToken
        if speed is not None:
            request = self.imaging.create_type('Move')
            request.VideoSourceToken = token
            request.Focus = {'Continuous': {'Speed': speed}}
            try:
                self.imaging.Move(request)
            except Exception as e:
                raise Exception(
                    "该设备不支持该功能！"
                    "<br/><br/><front style='color: #aaa;'>异常信息：%s</front>" % str(e))
        else:
            self.imaging.Stop({'VideoSourceToken': token})

    def set_cam_time(self, timeStamp=None):
        """
        设置时间
        参考文档：https://www.onvif.org/onvif/ver10/device/wsdl/devicemgmt.wsdl#op.SetSystemDateAndTime
        :param timeStamp: 秒级时间戳，不传则使用当前时间
        """
        if timeStamp is None:
            timeNow = datetime.datetime.now()
        else:
            timeNow = datetime.datetime.fromtimestamp(int(timeStamp))
        utc_now = timeNow.astimezone(pytz.utc)
        self.cam.devicemgmt.SetSystemDateAndTime({
            'DateTimeType': 'Manual',
            'DaylightSavings': False,
            'UTCDateTime': {
                'Time': {
                    'Hour': utc_now.hour,
                    'Minute': utc_now.minute,
                    'Second': utc_now.second
                },
                'Date': {
                    'Year': utc_now.year,
                    'Month': utc_now.month,
                    'Day': utc_now.day
                }
            }
        })


def ws_discovery():
    """
    发现设备
    :return: 返回支持onvif协议的设备ip以及onvif端口号
    """
    result = []
    wsd = WSDiscovery()
    wsd.start()
    services = wsd.searchServices()
    for service in services:
        url = service.getXAddrs()[0]
        if 'onvif' in url and '//' in url:
            uri = url.split('//')[1]
            ipAddr = uri.split('/')[0] if '/' in uri else uri
            result.append({
                'ip': ipAddr.split(':')[0] if ':' in ipAddr else ipAddr,
                'port': int(ipAddr.split(':')[1]) if ':' in ipAddr else 80
            })
    wsd.stop()
    return result
```

调用方

```python
import time
import json


# 设备发现
print(ws_discovery())

client = OnvifClient('192.168.1.10', 80, 'admin', '123456')

# 获取所有画面所有码流的RTSP地址等信息，注意截图转为base64比较大，测试时可以注释掉
print(json.dumps(client.get_rtsp()))

# 获取设备信息
print(json.dumps(client.get_deviceInfo()))

# 设置时间
client.set_cam_time()

# 云台移动-正数和负数区分方向，数值决定速度，不需要移动的项设置为0
speedPan = '4'		# 平移
speedTilt = '-4'	# 俯仰
speedZoom = '1'		# 缩放
client.ptz_move({
    'PanTilt': {
        'x': speedPan,
        'y': speedTilt
    },
    'Zoom': speedZoom
})
# 3秒后停止
time.sleep(3)
client.ptz_move()

# 聚焦-通常用正负的0.02
client.focus_move('0.02')
# 3秒后停止
time.sleep(3)
client.focus_move()

```

