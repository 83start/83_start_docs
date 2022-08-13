# 1 参考网站

http://t.csdn.cn/cc0xz

https://developer.qiniu.com/kodo/1239/java

https://portal.qiniu.com/kodo/bucket

# 2 项目简介

七牛云对象存储Kodo 是否七牛云提供的高可靠，强安全，低成本，可扩展的存储服务，可以通过控制台，API,SDK等方式简单快速的接入七牛云存储服务，实现海量数据的存储和管理。通过Kodo 可以进行对文件进行上传，下载和管理。

通过Springboot 接入七牛云Kodo ,实现客户端的文件的集中管理，大大减轻服务器的压力。

# 3 创建七牛云

## 3.1 注册七牛云并认证

首先在七牛云官网注册七牛云账号，然后对账号进行认证，之后才可以在对象存储中新建存储空间。

## 3.2 新建存储空间

1. 登陆七牛云开发者平台，点击左侧【对象存储】

![](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101220803.png)

2. 点击进入【空间管理】后，点击【新建空间】

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101220235.png)

3. 自定义存储空间名称、选择存储区域和访问控制，点击【确定】

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101220012.png)

# 4 绑定域名

> 七牛云的新建Bucket会有一个免费的CDN测试域名，但是测试域名只可以用于测试，只有30天的有效期，不适合在生产环境中应用

可以在域名管理中绑定已经备案过的域名。

![4](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101224758.png)

# 5 获取密钥

> 密钥是七牛云用户上传文件的唯一凭证
>
> 点击左上方的头像中的密钥管理

![5](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101226830.png)

# 6 Springboot 管理七牛云

## 6.1 创建项目

1. 创建一个Springboot - Maven 的项目
2. 项目名为`qiniu-kodo`
3. Springboot 的版本为：`2.1.17.RELEASE`

## 6.2 引入依赖

```XML
<!-- 👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇引用常用的依赖👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>com.qiniu</groupId>
            <artifactId>qiniu-java-sdk</artifactId>
            <version>7.7.0</version>
        </dependency>

        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.8.5</version>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.47</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
        </dependency>
        <!-- 👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆引用常用的依赖👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆 -->
```

## 6.3 编写配置文件

> 配置文件的地址：`src/main/resources/application.*`
>
> springboot 项目默认是 `application.properties`文件，使用`.yml` 相对简洁一些。
>
> 采用`.yml`文件

```YML
# 应用名称
spring:
  application:
    name: qiniu-kodo
    
# 设置端口
server:
  port: 8080

# 配置七牛云Kodo 的信息
qiniu:
  kodo:
    accessKey: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    secretKey: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    bucket: XXXXXXXXXXXX
    region: XXXXXXXXXXX
    domain: XX.XX.XX.XX
```

- accessKey ： 密钥管理中的 AK
- secretKey ： 密钥管理中的 SK
- bucket : bucket 的名字，空间管理
- region : 选择 bucket 所在的地区
  - 华南 :  huanan
  - 华东：huadong
  - 华北：huabei
  - 北美：beimei
  - 新加坡 ： xinjiapo
  - ……
- domain：绑定的域名（如果没有绑定域名，可以暂时使用测试域名）

## 6.3 编写配置类

> 配置类的地址：`src/main/java/${自定义项目路径}/config`

```java
package com.test.springboot.qiniukodo.config;

import com.qiniu.cdn.CdnManager;
import com.qiniu.storage.BucketManager;
import com.qiniu.storage.Region;
import com.qiniu.storage.UploadManager;
import com.qiniu.storage.persistent.FileRecorder;
import com.qiniu.util.Auth;
import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.MultipartConfigFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.util.unit.DataSize;
import javax.servlet.MultipartConfigElement;
import java.io.IOException;
import java.nio.file.Paths;

/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/10 10:13
 * @package com.springboot.qiniukode.config
 * @description 七牛云对象存储配置类
 */
@ConfigurationProperties(prefix = "qiniu.kodo")
@Configuration
@Data
public class QiniuKodoConfig {
    // 配置静态变量

    private String accessKey;
    private String secretKey;
    private String bucket;
    private String region;
    private String domain;

    /**
     * 带有指定Region对象的配置实例
     *
     * @return com.qiniu.storage.Configuration
     */
    @Bean
    public com.qiniu.storage.Configuration config() {
        if ("huadong".equals(region)) {
            return new com.qiniu.storage.Configuration(Region.huadong());
        }
        if ("huabei".equals(region)) {
            return new com.qiniu.storage.Configuration(Region.huabei());
        }
        if ("huanan".equals(region)) {
            return new com.qiniu.storage.Configuration(Region.huanan());
        }
        if ("beimei".equals(region)) {
            return new com.qiniu.storage.Configuration(Region.beimei());
        }
        if ("xinjiapo".equals(region)) {
            return new com.qiniu.storage.Configuration(Region.xinjiapo());
        }
        return new com.qiniu.storage.Configuration();
    }

    /**
     * 七牛云上传管理器实例
     *
     * @return com.qiniu.storage.UploadManager
     */
    @Bean
    public UploadManager uploadManager() {
        return new UploadManager(config());
    }

    /**
     * 断点续传的七牛云上传管理器实例
     *
     * @return com.qiniu.storage.UploadManager
     * @throws IOException IOException
     */
    @Bean
    public UploadManager resumableUploadManager() throws IOException {
        com.qiniu.storage.Configuration config = config();
        // 指定分片上传版本
        config.resumableUploadAPIVersion = com.qiniu.storage.Configuration.ResumableUploadAPIVersion.V2;
        // 设置分片上传并发，1：采用同步上传；大于1：采用并发上传
        config.resumableUploadMaxConcurrentTaskCount = 2;
        String localTempDir = Paths.get(System.getenv("java.io.tmpdir"), bucket).toString();
        // 设置断点续传文件进度保存目录
        FileRecorder fileRecorder = new FileRecorder(localTempDir);
        return new UploadManager(config, fileRecorder);
    }

    /**
     * 认证信息实例
     *
     * @return com.qiniu.util.Auth
     */
    @Bean
    public Auth auth() {
        return Auth.create(accessKey, secretKey);
    }

    /**
     * 空间资源管理器实例
     *
     * @return com.qiniu.storage.BucketManager
     */
    @Bean
    public BucketManager bucketManager() {
        return new BucketManager(auth(), config());
    }

    /**
     * CDN管理器实例
     *
     * @return com.qiniu.cdn.CdnManager
     */
    @Bean
    public CdnManager cdnManager() {
        return new CdnManager(auth());
    }

    /**
     * 文件上传配置
     *
     * @return javax.servlet.MultipartConfigElement
     */
    @Bean
    public MultipartConfigElement multipartConfigElement() {
        MultipartConfigFactory multipartConfigFactory = new MultipartConfigFactory();
        // 设置multipart/form-data请求允许的最大数据大小，这里设置为1Gb
        multipartConfigFactory.setMaxRequestSize(DataSize.ofGigabytes(1));
        // 设置上传文件允许的最大大小，这里设置为1Gb
        multipartConfigFactory.setMaxFileSize(DataSize.ofGigabytes(1));
        return multipartConfigFactory.createMultipartConfig();
    }
}
```

## 6.4 编写工具类

> 工具类地址：`src/main/java/${自定义项目路径}/utils`

### 6.4.1 Bean 工具类

```java
package com.test.springboot.qiniukodo.utils;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/10 10:43
 * @package com.springboot.qiniukode.utils
 * @description 获取Bean的工具类
 */

@Component
public class SpringUtil implements ApplicationContextAware {
    private static ApplicationContext applicationContext;

    /**
     * 设置Application方法
     *
     * @param applicationContext
     * @throws BeansException
     */
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        SpringUtil.applicationContext = applicationContext;
    }

    /**
     * 获取application实例
     *
     * @return
     */
    public static ApplicationContext getApplicationContext(){
        return applicationContext;
    }

    /**
     * 通过名称获取bean
     *
     * @param name
     * @return
     */
    public static Object getBean(String name){
        return getApplicationContext().getBean(name);
    }

    /**
     * 通过class获取Bean
     *
     * @param requiredType class对象
     * @param <T>          class所代表的类型
     * @return Bean
     */
    public static <T> T getBean(Class<T> requiredType) {
        return getApplicationContext().getBean(requiredType);
    }

    /**
     * 通过name和class获取Bean
     *
     * @param name         name
     * @param requiredType class
     * @param <T>          class所代表的类型
     * @return Bean
     */
    public static <T> T getBean(String name, Class<T> requiredType) {
        return getApplicationContext().getBean(name, requiredType);
    }
}
```

### 6.4.2 QiniuKodo工具类

```java
package com.test.springboot.qiniukodo.utils;

import com.alibaba.fastjson.JSON;
import com.google.gson.Gson;
import com.qiniu.cdn.CdnManager;
import com.qiniu.cdn.CdnResult;
import com.qiniu.common.QiniuException;
import com.qiniu.http.Response;
import com.qiniu.storage.BucketManager;
import com.qiniu.storage.UploadManager;
import com.qiniu.storage.model.DefaultPutRet;
import com.qiniu.util.Auth;
import com.qiniu.util.StringMap;
import com.test.springboot.qiniukodo.config.QiniuKodoConfig;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.InputStream;
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;


/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/10 10:49
 * @package com.springboot.qiniukode.utils
 * @description 七牛云Kodo工具类
 */

public class QiniuKodoUtil {
    private static final Logger LOGGER = LoggerFactory.getLogger(QiniuKodoUtil.class);
    private static final String BUCKET;
    private static final String DOMAIN;
    private static final UploadManager UPLOAD_MANAGER;
    private static final UploadManager RESUMABLE_UPLOAD_MANAGER;
    private static final BucketManager BUCKET_MANAGER;
    private static final Auth AUTH;
    private static final CdnManager CDN_MANAGER;

    static {
        QiniuKodoConfig qiniuKodoConfig = SpringUtil.getBean(QiniuKodoConfig.class);
        BUCKET = qiniuKodoConfig.getBucket();
        DOMAIN = qiniuKodoConfig.getDomain();
        UPLOAD_MANAGER = SpringUtil.getBean("uploadManager", UploadManager.class);
        RESUMABLE_UPLOAD_MANAGER = SpringUtil.getBean("resumableUploadManager", UploadManager.class);
        BUCKET_MANAGER = SpringUtil.getBean(BucketManager.class);
        AUTH = SpringUtil.getBean(Auth.class);
        CDN_MANAGER = SpringUtil.getBean(CdnManager.class);
    }

    /**
     * 简单上传的凭证
     *
     * @return 上传凭证
     */
    public static String getUpToken() {
        return AUTH.uploadToken(BUCKET);
    }

    /**
     * 覆盖上传的凭证
     *
     * @param key 要想进行覆盖的文件名称，必须与上传文件名一致
     * @return 上传凭证
     */
    public static String getUpToken(String key) {
        return AUTH.uploadToken(BUCKET, key);
    }

    /**
     * 自定义上传回复的凭证
     *
     * @return 上传凭证
     */
    public static String getCustomUpToken() {
        StringMap putPolicy = new StringMap();
        // 通过设置returnBody参数来实现返回的JSON格式的内容
        putPolicy.put("returnBody",
                "{\"key\":\"$(key)\",\"hash\":\"$(etag)\",\"bucket\":\"$(bucket)\",\"fsize\":$(fsize)}");
        long expireSeconds = 3600;
        return AUTH.uploadToken(BUCKET, null, expireSeconds, putPolicy);
    }

    /**
     * 带回调业务服务器的凭证
     *
     * @return 上传凭证
     */
    public static String getUpTokenWithCallback() {
        StringMap putPolicy = new StringMap();
        // 回调地址，该地址需要允许公网访问
        putPolicy.put("callbackUrl", "http://83527e8a63ff.ngrok.io/qiniu/upload/callback");
        putPolicy.put("callbackBody",
                "{\"key\":\"$(key)\",\"hash\":\"$(etag)\",\"bucket\":\"$(bucket)\",\"fsize\":$(fsize)}");
        putPolicy.put("callbackBodyType", "application/json");
        long expireSeconds = 3600;
        return AUTH.uploadToken(BUCKET, null, expireSeconds, putPolicy);
    }

    /**
     * 本地文件上传
     *
     * @param localFilePath 本地路径
     * @param fileName      文件名
     * @param override      是否覆盖上传凭证
     * @return 文件链接
     */
    public static String uploadFile(String localFilePath, String fileName, boolean override) {
        String upToken;
        if (override) {
            // 覆盖上传凭证
            upToken = getUpToken(fileName);
        } else {
            upToken = getUpToken();
        }
        try {
            Response response = UPLOAD_MANAGER.put(localFilePath, fileName, upToken);
            String fileUrl = fileUrl(response, fileName);
            String[] urls = {fileUrl};
            refreshFiles(urls);
            return fileUrl;
        } catch (QiniuException ex) {
            qiniuException(ex);
            throw new RuntimeException(ex.getMessage());
        }
    }

    /**
     * 字节数组上传
     *
     * @param bytes    字节数组
     * @param fileName 文件名
     * @return 文件链接
     */
    public static String uploadFile(byte[] bytes, String fileName) {
        try {
            Response response = UPLOAD_MANAGER.put(bytes, fileName, getUpToken());
            return fileUrl(response, fileName);
        } catch (QiniuException ex) {
            qiniuException(ex);
            throw new RuntimeException(ex.getMessage());
        }
    }

    /**
     * 数据流上传
     *
     * @param inputStream 输入流
     * @param fileName    文件名
     * @return 文件链接
     */
    public static String uploadFile(InputStream inputStream, String fileName) {
        try {
            Response response = UPLOAD_MANAGER.put(inputStream, fileName, getUpToken(), null, null);
            return fileUrl(response, fileName);
        } catch (QiniuException ex) {
            qiniuException(ex);
            throw new RuntimeException(ex.getMessage());
        }
    }

    /**
     * 断点续传
     *
     * @param inputStream 输入流
     * @param fileName    文件名
     * @return 文件链接
     */
    public static String resumableUploadFile(InputStream inputStream, String fileName) {
        try {
            Response response = RESUMABLE_UPLOAD_MANAGER.put(inputStream, fileName, getUpToken(), null, null);
            return fileUrl(response, fileName);
        } catch (QiniuException ex) {
            qiniuException(ex);
            throw new RuntimeException(ex.getMessage());
        }
    }

    /**
     * 手动拼接方式获取公开空间文件链接
     *
     * @param fileName 文件名
     * @return 文件链接
     * @throws UnsupportedEncodingException
     */
    public static String getFileUrl(String fileName) throws UnsupportedEncodingException {
        // 对文件名进行urlencode以兼容不同的字符
        String encoderFileName = URLEncoder.encode(fileName, "UTF-8").replace("+", "20%");
        // 拼接文件链接
        String finalUrl = String.format("%s/%s", "http://" + DOMAIN, encoderFileName);
        LOGGER.info(finalUrl);
        return finalUrl;
    }

    /**
     * 手动拼接方式获取私有空间文件链接
     *
     * @param fileName 文件名
     * @return 文件链接
     * @throws UnsupportedEncodingException
     */
    public static String getPrivateFileUrl(String fileName) throws UnsupportedEncodingException {
        // 构建对应的公开空间访问链接
        String encoderFileName = URLEncoder.encode(fileName, "UTF-8").replace("+", "20%");
        String publicUrl = String.format("%s/%s", "http://" + DOMAIN, encoderFileName);
        // 1小时，可以自定义链接过期时间
        long expireInSeconds = 3600;
        // 对该链接进行私有授权签名
        String finalUrl = AUTH.privateDownloadUrl(publicUrl, expireInSeconds);
        LOGGER.info(finalUrl);
        return finalUrl;
    }

    /**
     * 删除空间中的文件
     *
     * @param fileName 文件名
     * @return
     */
    public static String delete(String fileName) {
        try {
            Response response = BUCKET_MANAGER.delete(BUCKET, fileName);
            return response.statusCode == 200 ? "删除成功" : "删除失败";
        } catch (QiniuException ex) {
            LOGGER.error(ex.code() + "");
            LOGGER.error(ex.response.toString());
            throw new RuntimeException(ex.getMessage());
        }
    }

    /**
     * 文件刷新
     *
     * @param urls 待刷新的文件链接数组
     */
    public static void refreshFiles(String[] urls) {
        try {
            CdnResult.RefreshResult refreshResult = CDN_MANAGER.refreshUrls(urls);
            LOGGER.info(refreshResult.code + "");
        } catch (QiniuException e) {
            LOGGER.error(e.response.toString());
        }
    }

    /**
     * 返回文件链接
     *
     * @param response com.qiniu.http.Response
     * @param fileName 文件名
     * @return 文件链接
     * @throws QiniuException QiniuException
     */
    private static String fileUrl(Response response, String fileName) throws QiniuException {
        // 解析上传成功的结果
        DefaultPutRet putRet = new Gson().fromJson(response.bodyString(), DefaultPutRet.class);
        LOGGER.info(putRet.key);
        LOGGER.info(putRet.hash);
        LOGGER.info("上传文件成功 {}", JSON.toJSONString(putRet));
        return "http://" + DOMAIN + "/" + fileName;
    }

    /**
     * QiniuException异常处理
     *
     * @param ex QiniuException
     */
    private static void qiniuException(QiniuException ex) {
        Response response = ex.response;
        LOGGER.error(response.toString());
        LOGGER.error("上传文件失败 {}", ex);
        try {
            LOGGER.error(response.bodyString());
        } catch (QiniuException e) {
            e.printStackTrace();
        }
    }

}
```

## 6.5 编写服务类

### 6.5.1 编写服务接口

> 服务接口的地址 ： `src/main/java/${自定义项目路径}/service`

```java
package com.test.springboot.qiniukodo.service;

import org.springframework.web.multipart.MultipartFile;

import java.io.UnsupportedEncodingException;

/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/10 10:53
 * @package com.springboot.qiniukode.service
 * @description 七牛云Kodo实现接口
 */



public interface QiniuKodoService {

    /**
     * 本地文件上传
     *
     * @param localFilePath 本地文件路径
     * @param fileName      上传路径和名字/文件名
     * @param override      是否覆盖上传凭证
     * @return 文件链接
     */
    String uploadPath(String localFilePath, String fileName, boolean override);

    /**
     * 字节数组上传
     *
     * @param file MultipartFile对象
     * @return 文件链接
     */
    String uploadBytes(MultipartFile file);

    /**
     * 数据流上传
     *
     * @param file MultipartFile对象
     * @return 文件链接
     */
    String uploadStream(MultipartFile file);

    /**
     * 断点续传
     *
     * @param file MultipartFile对象
     * @return 文件链接
     */
    String resumableUpload(MultipartFile file);

    /**
     * 公开空间文件下载
     *
     * @param fileName 文件名
     * @return 文件链接
     * @throws UnsupportedEncodingException
     */
    String download(String fileName) throws UnsupportedEncodingException;

    /**
     * 私有空间文件下载
     *
     * @param fileName 文件名
     * @return 文件链接
     * @throws UnsupportedEncodingException
     */
    String privateDownload(String fileName) throws UnsupportedEncodingException;

    /**
     * 删除空间中的文件
     *
     * @param fileName 文件名
     * @return 删除的文件名
     */
    String delete(String fileName);

    /**
     * 获取简单上传的配置
     *
     * @return 上传凭证
     */
    String getUpToken();

    /**
     * 获取自定义上传回复的凭证
     *
     * @return 上传凭证
     */
    String getCustomUpToken();

    /**
     * 获取带回调业务服务器的凭证
     *
     * @return 上传凭证
     */
    String getUpTokenWithCallback();
}
```

### 6.5.2 编写服务实现类

> 服务实现类的地址 ： `src/main/java/${自定义项目路径}/service/impl`

```java
package com.test.springboot.qiniukodo.service.impl;

import com.qiniu.util.StringUtils;
import com.test.springboot.qiniukodo.service.QiniuKodoService;
import com.test.springboot.qiniukodo.utils.QiniuKodoUtil;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;

import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.UUID;


/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/10 10:54
 * @package com.springboot.qiniukode.service
 * @description 七牛云Kodo服务实现类
 */


@Service
public class QiniukodoServiceImpl implements QiniuKodoService {

    @Override
    public String uploadPath(String localFilePath, String fileName, boolean override) {
        // 判断本地文件是否为空
        if (StringUtils.isNullOrEmpty(localFilePath)) {
            throw new RuntimeException("文件路径为空");
        }

        return QiniuKodoUtil.uploadFile(localFilePath, fileName, override);
    }

    @Override
    public String uploadBytes(MultipartFile file) {
        String originalFilename = file.getOriginalFilename();
        String fileName = UUID.randomUUID() + "-" + originalFilename;
        if (file.isEmpty()) {
            throw new RuntimeException("文件为空");
        }
        try {
            byte[] bytes = file.getBytes();
            return QiniuKodoUtil.uploadFile(bytes, fileName);
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException(e.getMessage());
        }
    }

    @Override
    public String uploadStream(MultipartFile file) {
        String originalFilename = file.getOriginalFilename();
        String fileName = UUID.randomUUID() + "-" + originalFilename;
        if (file.isEmpty()) {
            throw new RuntimeException("文件为空");
        }
        try {
            ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(file.getBytes());
            return QiniuKodoUtil.uploadFile(byteArrayInputStream, fileName);
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException(e.getMessage());
        }
    }

    @Override
    public String resumableUpload(MultipartFile file) {
        String originalFilename = file.getOriginalFilename();
        String fileName = UUID.randomUUID() + "-" + originalFilename;
        if (file.isEmpty()) {
            throw new RuntimeException("文件为空");
        }
        try {
            ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(file.getBytes());
            return QiniuKodoUtil.resumableUploadFile(byteArrayInputStream, fileName);
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException(e.getMessage());
        }
    }

    @Override
    public String download(String fileName) throws UnsupportedEncodingException {
        if (StringUtils.isNullOrEmpty(fileName)) {
            throw new RuntimeException("文件名为空");
        }
        return QiniuKodoUtil.getFileUrl(fileName);
    }

    @Override
    public String privateDownload(String fileName) throws UnsupportedEncodingException {
        if (StringUtils.isNullOrEmpty(fileName)) {
            throw new RuntimeException("文件名为空");
        }
        return QiniuKodoUtil.getPrivateFileUrl(fileName);
    }

    @Override
    public String delete(String fileName) {
        if (StringUtils.isNullOrEmpty(fileName)) {
            throw new RuntimeException("文件名为空");
        }
        return QiniuKodoUtil.delete(fileName);
    }

    @Override
    public String getUpToken() {
        return QiniuKodoUtil.getUpToken();
    }

    @Override
    public String getCustomUpToken() {
        return QiniuKodoUtil.getCustomUpToken();
    }

    @Override
    public String getUpTokenWithCallback() {
        return QiniuKodoUtil.getUpTokenWithCallback();
    }
}
```

## 6.6 编写控制类

> 服务控制类的地址 ： `src/main/java/${自定义项目路径}/controller`

```java
package com.test.springboot.qiniukodo.controller;

import com.test.springboot.qiniukodo.service.QiniuKodoService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import javax.annotation.Resource;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/10 10:52
 * @package com.springboot.qiniukode.controller
 * @description 七牛云Kodo控制类
 */

@RestController
public class QiniuKodoController {

    @Resource
    private QiniuKodoService qiniuKodoService;

    /**
     * 本地文件上传
     *
     * @param localFilePath 本地文件的路径地址
     * @param filename 自定义的文件路径/文件名，如果没有设置filename的值则一文件内容的hash值作为文件名
     * @param override 是否开启上传凭证，如果不开启，则相同文件名会报错
     * @return
     */
    @PostMapping("/upload/path")
    public String uploadPath(@RequestParam(value = "localFilePath") String localFilePath,
                             @RequestParam(value = "fileName") String filename,
                             @RequestParam(value = "override") boolean override) {
        return qiniuKodoService.uploadPath(localFilePath, filename, override);
    }

    @PostMapping("/upload/bytes")
    public String uploadBytes(@RequestPart(value = "file") MultipartFile file) {
        return qiniuKodoService.uploadBytes(file);
    }

    @PostMapping("/upload/stream")
    public String uploadStream(@RequestPart(value = "file") MultipartFile file) {
        return qiniuKodoService.uploadStream(file);
    }

    @PostMapping("/upload/resumable")
    public String resumableUpload(@RequestPart(value = "file") MultipartFile file) {
        return qiniuKodoService.resumableUpload(file);
    }

    @GetMapping("/download/{fileName}")
    public void download(@PathVariable(value = "fileName") String fileName, HttpServletResponse response) {
        try {
            String fileUrl = qiniuKodoService.download(fileName);
            response.sendRedirect(fileUrl);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @GetMapping("/download/private/{fileName}")
    public void privateDownload(@PathVariable(value = "fileName") String fileName, HttpServletResponse response) {
        try {
            String fileUrl = qiniuKodoService.privateDownload(fileName);
            response.sendRedirect(fileUrl);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @DeleteMapping("/delete/{fileName}")
    public String delete(@PathVariable(value = "fileName") String fileName) {
        return qiniuKodoService.delete(fileName);
    }

    @GetMapping("/token")
    public String getUpToken() {
        return qiniuKodoService.getUpToken();
    }

    @GetMapping("/token/custom")
    public String getCustomUpToken() {
        return qiniuKodoService.getCustomUpToken();
    }

    @GetMapping("/token/callback")
    public String getUpTokenWithCallback() {
        return qiniuKodoService.getUpTokenWithCallback();
    }

    @PostMapping("/qiniu/upload/callback")
    public void qiNiuCallback(HttpServletRequest request) {
        try {
            String line = "";
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(request.getInputStream()));
            StringBuilder stringBuilder = new StringBuilder();
            while ((line = bufferedReader.readLine()) != null) {
                stringBuilder.append(line);
            }
            Logger logger = LoggerFactory.getLogger(QiniuKodoController.class);
            logger.info("callbackBody:" + stringBuilder);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

# 7 测试接口

> 运行项目，使用 ApiPost6 进行测试
>
> 文件上传分为 `客户端上传` 和 `服务端上传`
>
> - 客户端：
>   - 需要从客户自己的业务服务器获取上传凭证，而这些上传凭证是通过服务端的SDK来生成的
>   - 然后通过客户自己的业务API分发给客户端使用。
>   - 根据上传的业务需求不同，七牛云Java SDK支持丰富的上传凭证生成方式。
> - 服务端：
>   - 客户使用七牛SDK直接从服务端直接把文件上传到Kodo
>   - 服务端可以生成凭证，并利用SDK进行文件上传，最后从七牛云获取文件访问的路径。
>   - 服务端上传，很少用到上传回调功能，直接用自定义的`returnBody`来获取自定义的回复内容。

## 7.1 服务端直传测试

### 7.1.1 上传本地文件（不覆盖上传凭证）

请求方式：`POST`

请求地址：`http://localhost:8080/upload/path`

请求参数：

- localFilePath：`文件上传路径`
- fileName：`文件上传的路径/文件自定义名称，如果没有指定fileName，以文件hash值作为文件名。`
- override：`是否开启覆盖`

返回值：`图片的访问路径`

![image-20220810131600777](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101316948.png)

> 如果没有开启覆盖，则上次上传相同名称，不同文件的时候会报错。
>
> 原因：图片的凭证是文件的Hash值，如果文件名相同，但是Hash值不同，就会报错。
>
> ![image-20220810132532985](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101325126.png)

### 7.1.2 上传本地文件（覆盖上传凭证）

请求方式：`POST`

请求地址：`http://localhost:8080/upload/path`

请求参数：

- localFilePath：`文件上传路径`
- fileName：`文件上传的路径/文件自定义名称，如果没有指定fileName，以文件hash值作为文件名。`
- override：`是否开启覆盖`

返回值：`图片的访问路径`

![image-20220810132815378](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101328524.png)

### 7.1.3 字节数组上传

> 可以将文件按照字节数组上传到空间中

请求方式：`POST`

请求地址：`http://localhost:8080/upload/bytes`

请求参数：

- file：`文件`

返回值：`图片的访问路径`

![image-20220810135819519](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101358659.png)

### 7.1.4 数据流上传

> 通过`InputStream`进行上传，适用于所有的`InputStream`子类。

请求方式：`POST`

请求地址：`http://localhost:8080/upload/stream`

请求参数：

- file：`文件`

返回值：`图片的访问路径`

![image-20220810140225728](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101402880.png)

### 7.1.5 断点续传

> 断点续传实在分片上传的基础上实现的。
>
> SDK内置`表单上传` 和 `分片上传`两种上传方式。
>
> - 表单上传适合小文件上传：前四种都属于表单上传
> - 分片上传适合大文件上传：
>   - 七牛文件上传管理器`UploadManager`上传文件时，会自动根据根据定义的`Condfiguration.putThreshold`来判断采用哪种上传方式。
>   - 超过了定义的`Configuration.putThreshold`就会使用分片上传方式，可以在构造该类对象的时候，通过`Configuration类`来自定义这个值。
>   - 分片上传分为`V1` 和 `V2`两个方式，默认是`V1`版。

请求方式：`POST`

请求地址：`http://localhost:8080/upload/resumable`

请求参数：

- file：`文件`

返回值：`图片的访问路径`

![image-20220810141219386](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101412537.png)

## 7.2 客户端直传测试

### 7.2.1 获取简单上传凭证

请求方式：`GET`

请求地址：`http://localhost:8080/token`

返回值：`token值`

![image-20220810150511018](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101505153.png)

### 7.2.2 通过Token 上传文件

请求方式：`POST`

请求地址：`http://upload-z2.qiniup.com`

请求参数：

- token：`请求的token值`
- file：`上传文件`

返回值：

```json
{
	"hash": "xxxxxxxxxxxxxxxxxxxxxxxxxx",
	"key": "xxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

> 将返回到的key值通过 7.3.2 私有空间即可访问
>
> 区域不同，存储空间不同，访问https://developer.qiniu.com/kodo/1671/region-endpoint-fq查看

### 7.2.3 获取自定义上传回复凭证

> 默认情况下，文件上传到七牛之后，在没有设置`returnBody`或者`callback`相关的参数情况下，七牛返回给上传端的回复格式为`hash`和`key`。之前的简单上传凭证就是默认的回复格式。通过设置`returnBody`参数可以实现自定义返回的JSON格式的内容。

请求方式：`GET`

请求地址：`http://localhost:8080/token/custom`

返回值：`token值`

![image-20220810151820665](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101518816.png)

### 7.2.4 通过自定义凭证上传文件

请求方式：`POST`

请求地址：`http://upload-z2.qiniup.com`

请求参数：

- token：`请求的自定义token值`
- file：`上传文件`

返回值：	

```json
{
	"key": "xxxxxxxxxxxxxxxxxxxxxxxxxx",
	"hash": "xxxxxxxxxxxxxxxxxxxxxxxx",
	"bucket": "xxxxxxxxxxxxxx",
	"fsize": 00000
}
```

![image-20220810153834942](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101538095.png)

> 将返回的key拼接到`http://localhost:8080/download/private/`访问即可重定向到文件链接地址

### 7.2.5 获取回调业务服务器的凭证

> 回调通知是指客户端在上传时指定服务端在处理完上传请求后，应该通知某个特定服务器，在该服务器确认接收了该回调后才将所有结果返回给客户端。
>
> 开发者可以要求七牛云存储在某文件上传完成后向特定的URL发起一个回调请求。七牛云存储会将该回调的响应内容作为文件上传响应的一部分一并返回给客户端。回调的流程如下：
> ------------------------------------------------
> ![39](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101529786.png)
>
> 要启用回调功能，业务服务器在签发上传凭证时需要设置上传策略（PutPolicy）中的callbackUrl和callbackBody字段，callbackUrl需要允许公网访问。在上传策略里面设置了上传回调相关参数的时候，七牛在文件上传到服务器之后，会主动地向callbackUrl发送POST请求的回调，回调的内容为callbackBody模版所定义的内容。业务服务器在收到来自七牛的回调请求的时候，可以根据请求头部的Authorization字段来进行验证，查看该请求是否是来自七牛的未经篡改的请求。
>
> 自定义上传回复的凭证适用于上传端（无论是客户端还是服务端）和七牛服务器之间进行直接交互的情况下。在客户端上传的场景之下，有时候客户端需要在文件上传到七牛之后，从业务服务器获取相关的信息，这个时候就要用到七牛的上传回调及相关回调参数的设置。

请求方式：`GET`

请求地址：`http://localhost:8080/token/callback`

返回值：`token值`

![image-20220810153323543](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101533706.png)

### 7.2.6 通过回调业务服务器的凭证上传文件（有问题，需要内网穿透）

请求方式：`POST`

请求地址：`http://upload-z2.qiniup.com`

请求参数：

- token：`请求的自定义token值`
- file：`上传文件`

返回值：	

> 将返回的key拼接到`http://localhost:8080/download/private/`访问即可重定向到文件链接地址

## 7.3 文件下载测试

> 文件的链接地址可以通过 `手动拼接方式`和`sdk自动生成方式`获取。在这里采用手动拼接的方式获取文件的连接地址。
>
> 文件的下载分为公开空间的文件下载和私有空间的文件下载。
>
> - 公开空间下载：其访问的链接主要是将空间绑定的域名拼接上空间里面的文件名即可访问
> - 私有空间下载：
>   1. 需要按照公开空间的文件访问方式构建对应的公开空间访问链接
>   2. 再对这个链接进行私有授权签名

### 7.3.1 公开空间下载

请求方式：`GET`

请求地址：`http://localhost:8080/download/${filePath/fileName}`

返回值：`图片`

![image-20220810145034283](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101450473.png)

### 7.3.2 私有空间下载

请求方式：`GET`

请求地址：`http://localhost:8080/download/private/${filePath/fileName}`

返回值：`图片`

![image-20220810145356015](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101453187.png)

## 7.4 删除文件测试

请求方式：`DELETE`

请求地址：`http://localhost:8080/delete/${filePath/fileName}`

返回值：`是否删除成功`

![image-20220810150021004](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208101500158.png)
