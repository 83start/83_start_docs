
     
## 1 参考网站

https://blog.csdn.net/weixin_34361881/article/details/93628663

https://oss.console.aliyun.com/bucket

https://help.aliyun.com/document_detail/32007.html

https://help.aliyun.com/document_detail/31837.htm?spm=a2c4g.11186623.0.0.4ec064017bTWHg#concept-zt4-cvy-5db

## 2 项目简介

阿里云对象存储OSS（Object Storage Service）是一款海量、安全、低成本、高可靠的云存储服务，可提供99.9999999999%（12个9）的数据持久性，99.995%的数据可用性。多种存储类型供选择，全面优化存储成本。

OSS具有与平台无关的RESTful API接口，您可以在任何应用、任何时间、任何地点存储和访问任意类型的数据。

您可以使用阿里云提供的API、SDK接口或者OSS迁移工具轻松地将海量数据移入或移出阿里云OSS。数据存储到阿里云OSS以后，您可以选择标准存储（Standard）作为移动应用、大型网站、图片分享或热点音视频的主要存储方式，也可以选择成本更低、存储期限更长的低频访问存储（Infrequent Access）、归档存储（Archive）、冷归档存储（Cold Archive）作为不经常访问数据的存储方式。

通过Springboot 接入阿里云OSS ,实现客户端的文件的集中管理，大大减轻服务器的压力。

## 3 创建阿里云Bucket

### 3.1 注册阿里云并认证

首先在阿里云官网注册阿里云账号，然后对账号进行认证，之后才可以在对象存储中新建存储空间。

阿里云官网：[https://www.aliyun.com/](https://www.aliyun.com/)

![image-20220817172029182](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171720449.png)

![image-20220817172109326](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171721496.png)

### 3.2 新建存储空间

1. 登陆阿里云控制台，点击左侧【对象存储OSS】

![image-20220811015618247](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208110156441.png)

2. 点击进入【Bucket列表】后，点击【创建Bucket】

![image-20220811015752309](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208110157419.png)

3. 自定义存储空间名称、选择存储区域、访问控制和读写权限，点击【确定】

![image-20220811015853992](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208110158107.png)



## 4 获取Bucket的相关信息

![image-20220817172315190](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171723312.png)

## 5 获取密钥

> 密钥是阿里云用户上传文件的唯一凭证
>
> 点击左上方的头像中的AccessKey管理

![image-20220811020033398](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208110200522.png)

## 6 Springboot 整合 Alicloud_OSS

### 6.1 创建项目

1. 创建一个Springboot - Maven 的项目
2. 项目名为`alicoud_oss`
3. Springboot 的版本为：`2.1.17.RELEASE`

### 6.2 引入依赖

```XML
<!-- 👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇引用常用的依赖👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>2.8.3</version>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.4</version>
    <scope>provided</scope>
</dependency>

<dependency>
    <groupId>joda-time</groupId>
    <artifactId>joda-time</artifactId>
    <version>2.9.9</version>
</dependency>

<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.8.1</version>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.47</version>
</dependency>
<!-- 👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆引用常用的依赖👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆 -->
```

### 6.3 编写配置文件

> 配置文件的地址：`src/main/resources/application.*`
>
> springboot 项目默认是 `application.properties`文件，使用`.yml` 相对简洁一些。
>
> 采用`.yml`文件

```YML
spring:
  application:
    # 定义项目名称
    name: alicloud-oss
  servlet:
    multipart:
      max-file-size: 100MB
      max-request-size: 1000MB

# 定义阿里云信息
aliyun:
  endpoint: xxxxxxxxxxxxxxxxxxxxxxxxx
  accessKeyId: xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  accessKeySecret: xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  bucketName: xxxxxxxxxxxxxxxx
  urlPrefix: http://${bucketName}.${endpoint}/
  
# 定义端口号
server:
  port: 8080
```

- accessKeyId： 密钥管理中的 AK
- accessKeySecret： 密钥管理中的 SK
- bucketName: bucket 的名字
- endpoint: 选择 bucket 所在的地区
- urlPrefix：访问域名，可以 bucketName + endpoint 拼接

### 6.3 编写配置类

> 配置类的地址：`src/main/java/${自定义项目路径}/config`

```java
package com.test.springboot.alicloudoss.config;

import com.aliyun.oss.OSS;
import com.aliyun.oss.OSSClient;
import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

@Configuration
@PropertySource(value = {"classpath:application.yml"})
@ConfigurationProperties(prefix = "aliyun")
@Data
public class AliCloudOssConfig {
    
    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;
    private String urlPrefix;

    @Bean
    public OSS getOssClient() {
        return new OSSClient(endpoint, accessKeyId, accessKeySecret);
    }
}
```

### 6.4 编写工具类

> 工具类地址：`src/main/java/${自定义项目路径}/utils`

```java
package com.test.springboot.alicloudoss.utils;

import org.apache.commons.lang3.RandomUtils;
import org.apache.commons.lang3.StringUtils;
import org.joda.time.DateTime;
import org.springframework.web.multipart.MultipartFile;

public class AlicloudOssUtil {

    /**
     * 获取文件的完整的上传路径
     *    设置文件新路径:
     *      - 如果指定了文件路径，则用文件用户指定的文件路径
     *      - 如果用户没有指定文件路径，则使用时间作为文件路径
     * @param file
     * @param filePath
     * @return
     */
    public static String getFilePath(MultipartFile file,String filePath){
        String fileName = file.getOriginalFilename();

        if (filePath.isEmpty() || filePath.equals(" ")){
            return getTimeFilePath(fileName);
        }else{
            // 判断路径的最后一个字符是否是 “/”
            if (! filePath.substring(filePath.length()-1).equals("/")){
                filePath += '/';
            }
            return filePath + fileName;
        }
    }

    /**
     * 根据时间生成 文件路径和文件名
     * @param sourceFileName
     * @return
     */
    public static String getTimeFilePath(String sourceFileName) {
        DateTime dateTime = new DateTime();
        return "File/Springboot/" + dateTime.toString("yyyy")
                + "/" + dateTime.toString("MM") + "/"
                + dateTime.toString("dd") + "/" + System.currentTimeMillis() +
                RandomUtils.nextInt(100, 9999) + "." +
                StringUtils.substringAfterLast(sourceFileName, ".");
    }
}
```

### 6.5 编写服务类

#### 6.5.1 编写服务接口

> 服务接口的地址 ： `src/main/java/${自定义项目路径}/service`

```java
package com.test.springboot.alicloudoss.service;

import com.aliyun.oss.model.OSSObjectSummary;
import com.test.springboot.alicloudoss.pojo.AlicloudOssPojo;
import org.springframework.web.multipart.MultipartFile;
import java.io.*;
import java.util.List;

/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/10 19:24
 * @package com.test.springboot.alicloudoss.service.impl
 * @description 文件说明
 */

public interface AlicloudOssService {
    /**
     * @desc 文件上传
     * 文档链接 https://help.aliyun.com/document_detail/84781.html?spm=a2c4g.11186623.6.749.11987a7dRYVSzn
     */
     AlicloudOssPojo upload(MultipartFile file, String filePath);

    /**
     * @desc 查看文件列表
     * 文档链接 https://help.aliyun.com/document_detail/84841.html?spm=a2c4g.11186623.2.13.3ad5b5ddqxWWRu#concept-84841-zh
     */
     List<OSSObjectSummary> list() ;

    /**
     * @desc 删除文件
     * 文档链接 https://help.aliyun.com/document_detail/84842.html?spm=a2c4g.11186623.6.770.4f9474b4UYlCtr
     */
     AlicloudOssPojo delete(String fileName) ;

    /**
     * @desc 下载文件
     * 文档链接 https://help.aliyun.com/document_detail/84823.html?spm=a2c4g.11186623.2.7.37836e84ZIuZaC#concept-84823-zh
     */
     void exportOssFile(OutputStream os, String fileName) throws IOException ;
}
```

#### 6.5.2 编写服务实现类

> 服务实现类的地址 ： `src/main/java/${自定义项目路径}/service/impl`

```java
package com.test.springboot.alicloudoss.service.impl;

import com.aliyun.oss.OSS;
import com.aliyun.oss.model.*;
import com.test.springboot.alicloudoss.config.AliCloudOssConfig;
import com.test.springboot.alicloudoss.pojo.AlicloudOssPojo;
import com.test.springboot.alicloudoss.service.AlicloudOssService;
import com.test.springboot.alicloudoss.utils.AlicloudOssUtil;
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;
import java.io.*;
import java.util.List;

@Service
public class AlicloudOssServiceImpl implements AlicloudOssService {
    // 允许上传的格式
    private static final String[] FILE_TYPE = new String[]{".bmp", ".jpg", ".jpeg", ".gif", ".png"};

    @Autowired
    private OSS ossClient;

    @Autowired
    private AliCloudOssConfig aliyunOssConfig;

    public AlicloudOssPojo upload(MultipartFile file,String filePath) {
        // 校验图片格式
        boolean isLegal = false;
        for (String type : FILE_TYPE) {
            if (StringUtils.endsWithIgnoreCase(file.getOriginalFilename(), type)) {
                isLegal = true;
                break;
            }
        }

        //封装Result对象，并且将文件的byte数组放置到result对象中
        AlicloudOssPojo alicloudOssPojo = new AlicloudOssPojo();
        if (!isLegal) {
            alicloudOssPojo.setStatus("error");
            return alicloudOssPojo;
        }

        // 生成文件上传的完整路径
        String allFilePath = AlicloudOssUtil.getFilePath(file, filePath);

        // 上传到阿里云
        try {
            ossClient.putObject(aliyunOssConfig.getBucketName(), allFilePath, new
                    ByteArrayInputStream(file.getBytes()));
        } catch (Exception e) {
            e.printStackTrace();
            //上传失败
            alicloudOssPojo.setStatus("error");
            return alicloudOssPojo;
        }

        // 设置实体pojo 的参数
        alicloudOssPojo.setStatus("done");
        alicloudOssPojo.setResponse("success");
        //this.aliyunConfig.getUrlPrefix() + filePath 文件路径需要保存到数据库
        alicloudOssPojo.setName(this.aliyunOssConfig.getUrlPrefix() + allFilePath);
        alicloudOssPojo.setUid(String.valueOf(System.currentTimeMillis()));
        return alicloudOssPojo;
    }

    public List<OSSObjectSummary> list() {
        // 设置最大个数。
        final int maxKeys = 200;
        // 列举文件。
        ObjectListing objectListing = ossClient.listObjects(new ListObjectsRequest(aliyunOssConfig.getBucketName()).withMaxKeys(maxKeys));
        List<OSSObjectSummary> sums = objectListing.getObjectSummaries();
        return sums;
    }

    public AlicloudOssPojo delete(String fileName) {
        // 根据BucketName,objectName删除文件
        ossClient.deleteObject(aliyunOssConfig.getBucketName(), fileName);
        AlicloudOssPojo alicloudOssPojo = new AlicloudOssPojo();
        alicloudOssPojo.setName(fileName);
        alicloudOssPojo.setStatus("removed");
        alicloudOssPojo.setResponse("success");
        return alicloudOssPojo;
    }

    public void exportOssFile(OutputStream os, String fileName) throws IOException {
        // ossObject包含文件所在的存储空间名称、文件名称、文件元信息以及一个输入流。
        OSSObject ossObject = ossClient.getObject(aliyunOssConfig.getBucketName(), fileName);
        // 读取文件内容。
        BufferedInputStream in = new BufferedInputStream(ossObject.getObjectContent());
        BufferedOutputStream out = new BufferedOutputStream(os);
        byte[] buffer = new byte[1024];
        int lenght = 0;
        while ((lenght = in.read(buffer)) != -1) {
            out.write(buffer, 0, lenght);
        }
        if (out != null) {
            out.flush();
            out.close();
        }
        if (in != null) {
            in.close();
        }
    }
}
```

### 6.6 编写控制类

> 服务控制类的地址 ： `src/main/java/${自定义项目路径}/controller`

```java
package com.test.springboot.alicloudoss.controller;

import com.alibaba.fastjson.JSON;
import com.aliyun.oss.model.OSSObjectSummary;
import com.test.springboot.alicloudoss.pojo.AlicloudOssPojo;
import com.test.springboot.alicloudoss.service.impl.AlicloudOssServiceImpl;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;

/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/10 18:52
 * @package com.test.springboot.alicloudoss.controller
 * @description 阿里云OSS 控制类
 */

@RestController
@Slf4j
public class AlicloudOssController {

    @Autowired
    private AlicloudOssServiceImpl alicloudOssService;

    /**
     * 文件上传到oss
     * 参考url路径：
     * @param file
     * @param filePath
     * @return
     * @throws Exception
     */
    @PostMapping("upload/path")
    public String upload(@RequestParam("file") MultipartFile file,
                         @RequestParam(value = "filePath",defaultValue = " ") String filePath)
            throws Exception {
        AlicloudOssPojo alicloudOssPojo = this.alicloudOssService.upload(file,filePath);

        log.info("日志文件：" + JSON.toJSONString(alicloudOssPojo));

        // 判断是否传输成功，如果传输成功，则返回访问地址。
        if (alicloudOssPojo.getResponse().equals("success")){
            return alicloudOssPojo.getName();
        }else {
            return "传输失败，请检查后重试";
        }
    }

    /**
     * 根据文件名删除oss上的文件
     * @param fileName
     * @return
     * @throws Exception
     */
    @DeleteMapping("delete")
    public AlicloudOssPojo delete(@RequestParam("fileName") String fileName) throws Exception {
        return this.alicloudOssService.delete(fileName);
    }

    /**
     * 查询oss上的所有文件
     * @return
     * @throws Exception
     */
    @GetMapping("list")
    public List<OSSObjectSummary> list() throws Exception {
        List<OSSObjectSummary> OssList = this.alicloudOssService.list();
        log.info(JSON.toJSONString(OssList));
        return OssList;
    }

    /**
     * 根据文件名下载oss上的文件
     * @param fileName
     * @param response
     * @throws IOException
     */
    @PostMapping("download")
    public void download(@RequestParam("fileName") String fileName, HttpServletResponse response) throws IOException {
        //通知浏览器以附件形式下载
        response.setHeader("Content-Disposition",
                "attachment;filename=" + new String(fileName.getBytes(), "ISO-8859-1"));
        this.alicloudOssService.exportOssFile(response.getOutputStream(),fileName);
    }
}
```

## 7 测试接口

> 运行项目，使用 ApiPost6 进行测试服务端上传
>

### 7.1 上传文件

请求方式：`POST`

请求地址：`http://localhost:8080/upload/path`

请求参数：

- filePath：`文件上传路径`
- file：`文件`

返回值：`图片的访问路径`

![image-20220817180127848](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171801972.png)

### 7.2 删除文件

请求方式：`DELETE`

请求地址：`http://127.0.0.1:8080/delete`

请求参数：fileName：`文件上传的路径/文件自定义名称`

返回值：`删除的信息`

![image-20220817180623298](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171806426.png)

### 7.3 列举文件

请求方式：`GET`

请求地址：`http://127.0.0.1:8080/list`

返回值：`文件列表信息`

![image-20220817180839499](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171808607.png)

### 7.4 下载文件(需要测试)

请求方式：`POST`

请求地址：`http://127.0.0.1:8080/download`

请求参数：fileName：`文件上传的路径/文件自定义名称`

返回值：`下载文件信息`

![image-20220817181145614](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208171811722.png)
