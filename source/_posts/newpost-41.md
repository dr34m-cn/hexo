---
title: SpringBoot单元测试注入空指针
date: 2022-04-27 16:10:52
tags: [Java]
index_img: /assets/headImg/java.png
---

## 现象

运行出现空指针异常，指向注入类的使用行

<!--more-->

类似下边的单元测试，空指针异常指向`fileCategoryDictService.deleteFileCategoryDictById(6);`所在行

```java
package xxx.test;

import com.ifnxs.amp.textanalyse.web.biz.configmgr.service.FileCategoryDictService;
import org.junit.Test;
import org.springframework.boot.test.context.SpringBootTest;

import javax.annotation.Resource;
import java.util.List;

@SpringBootTest
public class FileCategoryDictTest {

    @Resource
    private FileCategoryDictService fileCategoryDictService;


    /**
     * 删除字典
     */
    @Test
    public void delFileCategoryDict() {
        fileCategoryDictService.deleteFileCategoryDictById(6);
    }
}

```

## 解决

* 把`@Test`注解的引入由`import org.junit.Test;`改为`import org.junit.jupiter.api.Test;`

* 如果由于特殊原因一定从`import org.junit.Test;`引入的，可以在`FileCategoryDictTest`类上加上`@RunWith(SpringJUnit4ClassRunner.class)`注解
* 还没解决，可以尝试`@SpringBootTest`加上参数填入`SpringBoot`启动类如`@SpringBootTest(classes=xxApplication.class)`