# Springboot 整合 Mail

## 1 项目简介
Spring Boot也对JavaMail进行了封装简化。
以qq邮箱为例。

## 2 准备Mail

邮箱必须开启一个IMAP/POP3/SMTP 服务。

![mail_info](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140333544.png)


## 3 Springboot 整合 Mail
### 3.1 导入依赖

```xml
<!-- 👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>

<dependency>
    <groupId>net.sourceforge.nekohtml</groupId>
    <artifactId>nekohtml</artifactId>
    <version>1.9.22</version>
</dependency>
<!-- 👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆 -->
```

### 3.2 编写配置文件

> 使用 `application.yml` 文件

```yml
# 应用名称
spring:
  application:
    name: mail
  mail:
    #邮件服务器地址
    host: smtp.qq.com
    #用户名
    username: xxx@xx.xx
    #密码
    password: smtp_password
    default-encoding: UTF-8
    properties:
      mail:
        smtp.auth: true
    #发件人邮箱
    from: xxx@xx.xx

  thymeleaf:
    mode: LEGACYHTML5
    cache: false
    content-type: text/html
    prefix: classpath:/templates/

server:
  port: 8080
```

> 配置信息

1. `spring.mail.host`：

   - QQ邮箱：
     - SMTP服务器: smtp.qq.com

   - 163邮箱：

     - POP3服务器: pop.163.com 

     - SMTP服务器: smtp.163.com 

     - IMAP服务器: imap.163.com

2. `username`：邮箱登录名

3. `password`：

   - 客户端授权密码不是登录密码

   - 因为需要使用smtp服务，需要在`163/qq`邮箱设置中开启smtp服务（其他邮箱也是），而在`163/qq`邮箱中开启smtp服务需要设置客户端授权码，这个密码就是我们第三方API请求`163/qq`服务的验证密码

3. `mail.from`：发件人邮箱
   - 名字是自定义的。
   - 一般情况下，`spring.mail.username`和`spring.mail.from`是一样的

### 3.3 编写邮件服务

#### 3.3.1 文件目录

![image-20220814034823820](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140348870.png)

创建文件：

1. src/main/java/com/test/springboot/mail/service/MailService.class
2. src/main/resources/application.yml
3. src/main/resources/templates/emailTemplate.html
4. src/test/java/com/test/springboot/mail/service/MailServiceTest.class

#### 3.3.2 基础邮件

**service 类**

```java
package com.test.springboot.mail.service;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.FileSystemResource;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.stereotype.Component;
import org.thymeleaf.TemplateEngine;
import org.thymeleaf.context.Context;

import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import java.io.File;

/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/14 3:07
 * @package com.test.springboot.mail.service
 * @description 文件说明
 */

@Component
public class MailService {
    private Logger logger = LoggerFactory.getLogger(this.getClass());

    @Autowired
    private JavaMailSender mailSender;

    @Value("${spring.mail.from}")
    private String from;

    /**
     * 发送普通邮件
     *
     * @param to      收件人邮箱
     * @param subject 邮件主题
     * @param content 邮件内容
     */
    public void sendMail(String to, String subject, String content) {
        SimpleMailMessage mailMessage = new SimpleMailMessage();
        mailMessage.setFrom(from);
        mailMessage.setTo(to);
        mailMessage.setSubject(subject);
        mailMessage.setText(content);

        try {
            mailSender.send(mailMessage);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**test 类**

```java
package com.test.springboot.mail.service;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

/**
 * @author thinkpad - 83start
 * @version v1.0
 * @create 2022/8/14 3:08
 * @package com.test.springboot.mail.service
 * @description 文件说明
 */

@SpringBootTest
@RunWith(SpringRunner.class)
public class MailServiceTest {

    @Autowired
    private MailService mailService;

    @Test
    public void sendMail() {
        mailService.sendMail("xxx@xx.com", "title", "内容");
    }
}
```

#### 3.3.3 HTML格式邮件

**service 类**

```java
/**
     * 发送HTML格式的邮件
     *
     * @param to      收件人邮箱
     * @param subject 邮件主题
     * @param content 邮件内容
     */
    public void sendHtmlMail(String to, String subject, String content) {
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        try {
            MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true);
            helper.setFrom(from);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(content, true);
            mailSender.send(mimeMessage);
        } catch ( MessagingException e) {
            e.printStackTrace();
        }
    }

```

**test 类**

```java
@Test
    public void sendHtmlMail() {
        String html = "<html>\n" +
                "<body>\n" +
                "<h2>this a html email</h2>\n" +
                "</body>\n" +
                "</html>";
        mailService.sendHtmlMail("xxx@xx.xx", "测试HTML邮件", html);
    }
```

#### 3.3.4 携带附件的邮件

**service 类**

```java
/**
     * 发送携带附件的邮件
     *
     * @param to       收件人邮箱
     * @param subject  邮件主题
     * @param content  邮件内容
     * @param filePath 文件地址
     */
    public void sendAttachmentsMail(String to, String subject, String content, String filePath) {
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        try {
            MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true);
            helper.setFrom(from);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(content, true);

            FileSystemResource file = new FileSystemResource(new File(filePath));
            String fileName = filePath.substring(filePath.lastIndexOf(File.separator));
            helper.addAttachment(fileName, file);

            mailSender.send(mimeMessage);
        } catch (MessagingException e) {
            e.printStackTrace();
        }
    }
```

**test 类**

```java
 @Test
    public void sendAttachmentsMail() {
        String filePath = "/tycoding/learn-plan.md";
        mailService.sendAttachmentsMail("xxx@xx.xx", "测试携带附件的邮件", "这是携带附件的邮件，请注意查收", filePath);
    }
```

#### 3.3.5 HTML内联资源邮件

**service 类**

```java
/**
     * 发送HTML标签内携带资源的邮件
     *
     * @param to      收件人邮箱
     * @param subject 邮件主题
     * @param content 邮件内容
     * @param resPath 资源路径
     * @param resId   资源ID
     */
    public void sendInlineResourceMail(String to, String subject, String content, String resPath, String resId) {
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        try {
            MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true);
            helper.setFrom(from);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(content, true);
            FileSystemResource resource = new FileSystemResource(new File(resPath));
            helper.addInline(resId, resource);
            mailSender.send(mimeMessage);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

**test 类**

```java
@Test
    public void sendInlineResourceMail() {
        String to = "xxx@xx.xx";
        String subject = "这是携带内联资源的邮件";
        String resId = "001";
        String resPath = "/tycoding/totoro.png";
        String content = "<html>\n" +
                "<body>\n" +
                "这是带图片的邮件<img src=\'resId:" + resId + "\'>" +
                "</body>\n" +
                "</html>";
        mailService.sendInlineResourceMail(to, subject, content, resPath, resId);
    }
```

#### 3.3.6 模板邮件

比如一些网站常用发送发送验证码到邮箱，通常这是用一个模板的，比如这样：

在`resources/`下创建模板`emailTemplate.html`

**html 文件**

```html
<!DOCTYPE html>
<html lang="zh" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>邮件模板</title>
</head>
<body>

您好，欢迎注册本系统，您的验证码是： <b th:text="${code}"></b>

-- 83start

</body>
</html>
```

**service 类**

```java
 @Autowired
    private TemplateEngine templateEngine;

    /**
     * 发送模板邮件
     *
     * @param to      收件人邮箱
     * @param subject 邮件主题
     * @param code    邮件验证码
     */
    public void sendTemplateMail(String to, String subject, String code) {
        Context context = new Context();
        context.setVariable("code", code);
        String emailContent = templateEngine.process("emailTemplate", context);
        sendHtmlMail(to, subject, emailContent);
    }
```

**test 类**

```java
    @Test
    public void sendTemplateMail() {
        int code = (int) (Math.random() * 9 + 1) * 100000;
        mailService.sendTemplateMail("xxx@xx.xx", "这是模板邮件", String.valueOf(code));
    }
```

## 4 项目地址

gitee：https://gitee.com/salute83/springboot-mail.git