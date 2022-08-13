# 1 工程简介

Kaptcha是Google提供的一个图形验证码插件，可以从官网 https://code.google.com/p/kaptcha/ 了解更多的详情。它的源代码是公开的，在上面的官网提供的download项，可以下载一个zip包，里面包含了jar包和源代码。

Kaptcha 是一个扩展自simplecaptcha的验证码库，默认情况下，Kaptcha非常易于设置和使用，并且默认输出会产生一个很难验证的验证码。默认情况下，它生成的验证码看起来与上面的非常相似。如果您想更改输出的外观，则有几个配置选项，并且该框架是模块化的，因此您可以编写自己的变形代码。
# 2 Kaptcha详细配置表

> 主要用于 `application.yml` 文件中配置文件。

## 2.1 Kaptcha 常用属性

| 序号 | 属性名         | 描述       | 默认值 |
| ---- | -------------- | ---------- | ------ |
| 1    | kaptcha.width  | 验证码宽度 | 200    |
| 2    | kaptcha.height | 验证码高度 | 50     |

## 2.2 Kaptcha border（边框）属性

| 序号 | 属性名                   | 描述         | 默认值 |
| ---- | ------------------------ | ------------ | ------ |
| 1    | kaptcha.border.enabled   | 是否显示边框 | true   |
| 2    | kaptcha.border.color     | 边框颜色     | black  |
| 3    | kaptcha.border.thickness | 边框厚度     | 2      |

## 2.3 Kaptcha content（文本）属性

| 序号 | 属性名                 | 描述           | 默认值                |
| ---- | ---------------------- | -------------- | --------------------- |
| 1    | kaptcha.content.length | 验证码文本长度 | 4                     |
| 2    | kaptcha.content.source | 文本源         | abcde2345678gfynmnpwx |
| 3    | kaptcha.content.space  | 文本间隔       | 2                     |

## 2.4 Kaptcha font（字体）属性

| 序号 | 属性名             | 描述     | 默认值 |
| ---- | ------------------ | -------- | ------ |
| 1    | kaptcha.font.name  | 字体名称 | Arial  |
| 2    | kaptcha.font.size  | 字体大小 | 40     |
| 3    | kaptcha.font.color | 字体颜色 | black  |

## 2.5 Kaptcha background-color（背景颜色）属性
| 序号 | 属性名                        | 描述                   | 默认值    |
| :--- | ----------------------------- | ---------------------- | --------- |
| 1    | kaptcha.background-color.from | 背景颜色（开始渐变色） | lightGray |
| 2    | kaptcha.background-color.to   | 背景颜色（结束渐变色） | white     |

# 3 配置文件

`application.yml`

```yml
# kaptcha 配置
kaptcha:
  # 验证码宽度
  width: 200
  # 验证码高度
  height: 50
  # 验证码内容配置
  content:
    # 验证码文本长度
    length: 4
    # 验证码文本源
    source: ABCDEFGHIJKLMNOPQRSTUVWXYZqwertyuioplkjhgfdsazxcvbnm12345678923456789
    # 验证码文本间隔
    space: 2
  # 字体设置
  font:
    # 字体名称
    name: Arial
    # 字体大小
    size: 40
    # 字体颜色
    color: black
  # 背景颜色设置
  background-color:
    # 背景颜色（开始渐变色）
    from: lightGray
    # 背景颜色（结束渐变色）
    to: white
  # 边框设置
  border:
    # 是否启用边框
    enabled: true
    # 边框颜色
    color: black
    # 边框宽度
    thickness: 1
```

`application.properties`

```properties
#验证码宽度
kaptcha.width=200
#验证码高度
kaptcha.height=50
#验证码内容长度
kaptcha.content.length=4
#验证码内容源
kaptcha.content.source=ABCDEFGHIJKLMNOPQRSTUVWXYZ2345678923456789
#验证码内容间隔
kaptcha.content.space=2
#验证码字体名称
kaptcha.font.name=Arial
#验证码字体大小
kaptcha.font.size=40
#验证码字体颜色
kaptcha.font.color=black
#验证码背景颜色（开始颜色）
kaptcha.background-color.from=lightGray
#验证码背景颜色（结束颜色）
kaptcha.background-color.to=white
#验证码是否显示边框
kaptcha.border.enabled=true
#验证码边框颜色
kaptcha.border.color=black
#验证码边框厚度
kaptcha.border.thickness=1
```

# 4 Springboot 集成 Kaptcha

## 4.1 创建项目

1. 创建项目 `springboot - kaptcha`
2. 选择spring boot的版本为 `2.1.17.RELEASE`

## 4.2 导入依赖

```xml
        <!-- 验证码 -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>kaptcha-spring-boot-starter</artifactId>
            <version>1.1.0</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
```

## 4.3 编写配置文件

> 编写配置文件 `application.yml` 

```yml
# 应用名称
spring:
  application:
    name: kaptcha

server:
  port: 8080

# kaptcha 配置
kaptcha:
  # 验证码宽度
  width: 200
  # 验证码高度
  height: 50
  # 验证码内容配置
  content:
    # 验证码文本长度
    length: 4
    # 验证码文本源
    source: ABCDEFGHIJKLMNOPQRSTUVWXYZqwertyuioplkjhgfdsazxcvbnm12345678923456789
    # 验证码文本间隔
    space: 2
  # 字体设置
  font:
    # 字体名称
    name: Arial
    # 字体大小
    size: 40
    # 字体颜色
    color: black
  # 背景颜色设置
  background-color:
    # 背景颜色（开始渐变色）
    from: lightGray
    # 背景颜色（结束渐变色）
    to: white
  # 边框设置
  border:
    # 是否启用边框
    enabled: true
    # 边框颜色
    color: black
    # 边框宽度
    thickness: 1
```

## 4.4 编写控制类

> 创建控制文件 `KaptchaController.java`

```java
package com.test.springboot.kaptcha.controller;

import com.baomidou.kaptcha.Kaptcha;
import com.baomidou.kaptcha.exception.KaptchaIncorrectException;
import com.baomidou.kaptcha.exception.KaptchaNotFoundException;
import com.baomidou.kaptcha.exception.KaptchaTimeoutException;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/12 23:28
 * @package com.test.springboot.kaptcha.controller
 * @description 文件说明
 */
@Slf4j
@RestController
@RequestMapping(value = "/kaptcha")
public class KaptchaController {

    @Autowired
    private Kaptcha kaptcha;

    /**
     * 获取Kaptcha验证码
     */
    @GetMapping("/getCode")
    public void getKaptchaImg() {
        //默认900秒
        String render = kaptcha.render();
        log.info(render);
    }

    /**
     * 验证验证码
     *
     * @param code 验证码
     * @return
     */
    @PostMapping("/validCode")
    public String validCode(@RequestParam(value = "code") String code) {
        try {
            kaptcha.validate(code);
        } catch (Exception e) {
            if (e instanceof KaptchaIncorrectException) {
                return "验证码不正确";
            } else if (e instanceof KaptchaNotFoundException) {
                return "验证码未找到";
            } else if (e instanceof KaptchaTimeoutException) {
                return "验证码过期";
            } else {
                return "验证码渲染失败";
            }
        }
        return "验证通过";
    }
}
```

# 5 测试接口

## 5.1获取验证码

请求方式：`GET`

请求地址：`http://127.0.0.1:8080/kaptcha/getCode`

返回值：`验证码图片`

![image-20220813001217358](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208130012587.png)

## 5.2 验证验证码

请求方式：`POST`

请求地址：`http://127.0.0.1:8080/kaptcha/validCode`

请求参数：`code : 验证码`

返回值：`验证成功是否`

![image-20220813001443311](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208130014440.png)