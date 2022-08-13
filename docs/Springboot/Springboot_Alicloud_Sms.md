## 1 阿里云账号权限开通

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616642.png) 

### 1.1 了解子用户权限
**用户组：**
通过用户组对职责相同的 RAM 用户进行分类并授权，可以更加高效地管理用户及其权限。
1. 对一个用户组进行授权后，用户组内的所有用户会自动继承该用户组的权限。
2. 如果一个用户被加入到多个用户组，那么该用户将会继承多个用户组的权限。

**用户：**
RAM 用户是一个身份实体，它通常代表您的组织中需要访问云资源的人员或应用程序。
通常的操作步骤如下：
1. 创建用户，并为用户设置登录密码（用户登录控制台场景）或创建 AccessKey（应用程序调用 API 场景）。
2. 添加用户到用户组（需要先创建用户组并完成对用户组的授权）。

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616709.png)

### 1.2 设置子用户

1. 创建用户组

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616669.png)

2. 添加用户组的权限

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616604.png)
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616665.png)

3. 创建用户

**一定要开启 `Open API 访问`，这样才能通过编程来访问**

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616649.png)

4. 创建 授权码 `AccessKey`

**获取的授权码是访问的密钥，不能通过任何途径向外暴露信息**
> 生成的 `账号` `密码` 保存在本地，后期会用到。

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616578.png)

5. 用户组添加用户

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616506.png)

## 2 短信验证码获取

### 2.1 找到短信控制台面板

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616654.png)

### 2.2 帮助文档

https://help.aliyun.com/document_detail/59210.html

## 3 添加短信模板

### 3.1 添加短信模板

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616637.png)

### 3.2 等待审核

## 4 添加短信签名

### 4.1 添加短信模板

> 主要用于公司的名称

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616726.png)

### 4.2 等待审核

## 5 编写短信测试

### 5.1 创建 `Springboot` 项目
1. 选择Springboot 项目

> idea 默认使用 Spring Initializr 来初始化 url:`https://start.spring.io/` 失败率很高
> 用 aliCloud 不容易失败 url:`http://start.aliyun.com`

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616972.png)

2. 填写版本信息

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616288.png)

3. 选择依赖


![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616454.png)

4. 设置存储地址

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208111616537.png)

### 5.2 导入 pom 依赖
```xml
        <!-- sms -->
        <dependency>
            <groupId>com.aliyun</groupId>
            <artifactId>aliyun-java-sdk-core</artifactId>
            <version>4.0.3</version>
        </dependency>
        <!-- fastjson -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.62</version>
        </dependency>
        <!-- redis -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

### 5.3 编写测试代码
```java
package com.test.sms;
import com.alibaba.fastjson.JSONObject;
import com.aliyuncs.CommonRequest;
import com.aliyuncs.CommonResponse;
import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.IAcsClient;
import com.aliyuncs.exceptions.ClientException;
import com.aliyuncs.exceptions.ServerException;
import com.aliyuncs.http.MethodType;
import com.aliyuncs.profile.DefaultProfile;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import java.util.HashMap;

@RunWith(SpringRunner.class)
@SpringBootTest
public class SmsApplicationTests {

    @Test
    public void contextLoads() {
        // 连接阿里云
        DefaultProfile profile = DefaultProfile.getProfile("cn-hangzhou", "<accessKeyId>", "<accessSecret>");
        IAcsClient client = new DefaultAcsClient(profile);

        CommonRequest request = new CommonRequest();
        request.setMethod(MethodType.POST);
        request.setDomain("dysmsapi.aliyuncs.com");
        request.setVersion("2017-05-25");
        request.setAction("SendSms");

        // 自定义的参数（手机号，验证码，签名，模板）
//        request.putQueryParameter("RegionId","cn-hangzhou");
        // 手机号码
        request.putQueryParameter("PhoneNumbers","<接受人的手机号码>");
        // 签名
        request.putQueryParameter("SignName","<签名的名字>");
        // 模板
        request.putQueryParameter("TemplateCode","<TemplateCode>");

        // 构建短信验证码
        HashMap<String, Object> hashMap = new HashMap<>();
        hashMap.put("code",2233);

        // 设置模板内参数
        request.putQueryParameter("TemplateParam", JSONObject.toJSONString(hashMap));

        try {
            CommonResponse response = client.getCommonResponse(request);
            System.out.println(response.getData());
        } catch (ServerException e) {
            e.printStackTrace();
        } catch (ClientException e) {
            e.printStackTrace();
        }
    }
}
```

<iframe height=498 width=510 src="http://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/video/finish_1641710063818.mp4"/>

## 6 编写真实业务场景下的短信验证码微服务

### 6.1 服务接口

```java
package com.test.sms.service;
import java.util.Map;

/**
 * @FileName: sms.com.test.sms.service.ISendSms
 * @Author: <a href = "mailto:cao83jun@gmail.com"> 83_start <a/>
 * @Date: 2022-01-09 14:43
 * @Version: 1.0
 * @Description:
 */
public interface ISendSmsService {
    /**
     * 发送手机短信
     * @param phoneNumber 手机号
     * @param templateCode 模板号
     * @param codes 参数
     * @return 是否发送成功
     */
    public boolean sendSms(String phoneNumber, String templateCode, Map<String,Object> codes);
}
```

### 6.2 服务实现类

```java
package com.test.sms.service.impl;
import com.alibaba.fastjson.JSONObject;
import com.aliyuncs.CommonRequest;
import com.aliyuncs.CommonResponse;
import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.IAcsClient;
import com.aliyuncs.exceptions.ClientException;
import com.aliyuncs.exceptions.ServerException;
import com.aliyuncs.http.MethodType;
import com.aliyuncs.profile.DefaultProfile;
import com.test.sms.service.ISendSmsService;
import org.springframework.stereotype.Service;
import java.util.Map;

/**
 * @FileName: sms.com.test.sms.service.impl.SendSms
 * @Author: <a href = "mailto:cao83jun@gmail.com"> 83_start <a/>
 * @Date: 2022-01-09 14:45
 * @Version: 1.0
 * @Description:
 */
@Service
public class SendSmsService implements ISendSmsService {
    @Override
    public boolean sendSms(String phoneNumber, String templateCode, Map<String, Object> codes) {
        // 连接阿里云
        DefaultProfile profile = DefaultProfile.getProfile("cn-hangzhou", "<accessKeyId>", "<accessSecret>");
        IAcsClient client = new DefaultAcsClient(profile);

        CommonRequest request = new CommonRequest();
        request.setMethod(MethodType.POST);
        request.setDomain("dysmsapi.aliyuncs.com");
        request.setVersion("2017-05-25");
        request.setAction("SendSms");

        // 自定义的参数（手机号，验证码，签名，模板）
        // request.putQueryParameter("RegionId","cn-hangzhou");
        // 手机号码
        request.putQueryParameter("PhoneNumbers", phoneNumber);
        // 签名
        request.putQueryParameter("SignName", "<签名模块的名字>");
        // 模板
        request.putQueryParameter("TemplateCode", templateCode);

        // 设置模板内参数
        request.putQueryParameter("TemplateParam", JSONObject.toJSONString(codes));

        try {
            CommonResponse response = client.getCommonResponse(request);
            System.out.println(response.getData());
            return response.getHttpResponse().isSuccess();
        } catch (ServerException e) {
            e.printStackTrace();
        } catch (ClientException e) {
            e.printStackTrace();
        }
        return false;
    }
}
```
### 6.3 配置文件
```properties
# 应用名称
spring.application.name=sms

# 接口
server.port=9090

# redis
spring.redis.host=127.0.0.1
spring.redis.port=6379
```

### 6.4 控制类
```java
package com.test.sms.controller;
import com.aliyuncs.utils.StringUtils;
import com.test.sms.service.impl.SendSmsService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import java.util.HashMap;
import java.util.UUID;
import java.util.concurrent.TimeUnit;

/**
 * @FileName: sms.com.test.sms.controller.SendSmsController
 * @Author: <a href = "mailto:cao83jun@gmail.com"> 83_start <a/>
 * @Date: 2022-01-09 14:49
 * @Version: 1.0
 * @Description:
 */
@RestController
@CrossOrigin // 跨域支持
public class SendSmsController {

    @Autowired
    private SendSmsService smsService;

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    @GetMapping("/send/{phone}")
    public String code(@PathVariable("phone") String phone) {
        // 调用方法（模拟真实业务 redis）

        String code = redisTemplate.opsForValue().get(phone);
        if (!StringUtils.isEmpty(code)) {
            return phone + ":" + code + "尚未过期";
        }

        // 生成 验证码并存储到redis 中
        code = UUID.randomUUID().toString().substring(0, 4);
        HashMap<String, Object> hashMap = new HashMap<>();

        hashMap.put("code", code);

        boolean isSendSuccess = smsService.sendSms(phone, "<templateCode>", hashMap);

        if (isSendSuccess) {
            redisTemplate.opsForValue().set(phone, code, 5, TimeUnit.MINUTES);
            return phone + ":" + code + "发送成功";
        } else {
            return "发送失败";
        }
    }
}
```

### 6.5 运行测试

1. 打开本地的redis 
2. 运行 `Springboot` 应用程序
3. 输入url `localhost:9090/send/{phoneNumber}`
4. 查看手机验证码

<iframe height=498 width=510 src="http://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/video/finish2_1641712418112.mp4"/>
## 7 代码仓库



