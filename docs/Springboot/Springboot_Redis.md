## 1 参考资料

[有道云_Redis笔记](https://note.youdao.com/ynoteshare/index.html?id=c357abb49568f83fcf2ce2e2972615bf&type=notebook&_time=1660427601940)

[狂神说Java_Redis](https://www.bilibili.com/video/BV1S54y1R7SB?share_source=copy_web&vd_source=0b00699b0417cbd0775b397ea799a5be)

[https://blog.51cto.com/zhangzhixi/3176540](https://blog.51cto.com/zhangzhixi/3176540)

[http://docs.tumo.tycoding.cn/#/docs/boot/spring-boot-redis](http://docs.tumo.tycoding.cn/#/docs/boot/spring-boot-redis)

## 2 项目简介

Springboot 有两种方式来整合Redis ，第一种采用官方推荐的工具Jedis，第二种采用RedisTemplate的方式连接Redis。
- Jedis 是Redis 官方推荐的Java连接工具，jedis 采用的是直接连接，多个线程操作时，是不安全的，如果想要安全，使用jedis pool 连接池，更像BIO 模式。
- RedisTemplate 个人比较推荐的连接方式，在SpringBoot2.x 之后，原来使用的redis 被替换成了 lettuce，lettuce 采用的是netty ，实例可以作多个线程中进行共享，不存在线程不安全的情况下，可以减少线程数据了，更像NIO 模式。

## 3 Springboot 整合 Jedis

### 3.1 创建 Springboot 项目
1. 创建一个 `springboot-redis`的项目
2. springboot的版本采用`2.1.17.RELEASE`

### 3.2 导入依赖
```java
    <!-- 👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇 jedis 👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇 -->
        <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.2.0</version>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.49</version>
        </dependency>
        <!-- 👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆 -->
```

### 3.3 连接数据库测试

