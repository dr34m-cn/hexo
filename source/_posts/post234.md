---
title: Java实现文件重命名，Java文件追加写入，java读取图片尺寸
date: 2017-12-15 17:47:38
tags: [编程,Java]
index_img: /assets/headImg/java.png
---
### 起由

因为规范要求，需要对一批文件重命名并获取尺寸，刚好上午的java课学了文件输入输出流，学以致用嘛，就利用java做了尝试。
### 功能
1.文件按照指定格式重命名；

2.文件夹内所有文件名与图片尺寸按格式写入一个TXT文件。
<!--more-->
### 效果
之前：

![234-1](post234/234-1.jpg)
之后：

![234-2](post234/234-2.jpg)

![234-3](post234/234-3.jpg)

### 代码
```java
import java.io.*;
import java.awt.image.BufferedImage;
import javax.imageio.*;
public class FileNameChange{
	public static void main(String[] args){
		String dir = "D:/filenamechangejava/";
		File f1 = new File(dir+"photo");
		File[] s = f1.listFiles();//为目录下所有文件构造对象到文件数组s中
		String[] k = new String[100];//定义一个字符串数组来存储文件名
		for(int i = 0;i < s.length;i++){
			k[i] = s[i].getName();
		}//存储所有文件名到k数组中
		for(int i = 0;i < s.length;i++){
			k[i] = k[i].substring(0,4)+k[i].substring(5,7)+k[i].substring(8,10)+k[i].substring(11,15)+".jpg";//更改文件名为想要的效果
			s[i].renameTo(new File(dir+"photo/"+k[i]));//重命名文件
		}
		System.out.println("Rename complete!");
		//以上为重命名部分，以下为写文件部分
		File[] s2 = f1.listFiles();
		try{
			for(int j = 0;j < s.length;j++){
				k[j] = "\""+k[j].substring(0,12)+"\",\n";
				BufferedImage pic = ImageIO.read(new FileInputStream(s2[j]));//读取图片
				String m = k[j] + "\"" + pic.getWidth()+ "x" + pic.getHeight()+"\",\n\n";//获取图片宽和高并和文件名一同存储到字符串m
				RandomAccessFile fos = new RandomAccessFile(dir+"name.txt","rw");//用读写方式打开文件
				long len = fos.length();//返回文件长度到len
				fos.seek(len);//定义指针从len开始写入
				fos.writeBytes(m);//写入文件
				fos.close();
			}
		}catch(IOException e){
			e.printStackTrace();
		}
		System.out.println("Write name and size in \""+dir+"name.txt\" complete!");
	}
}
```