# 1 NoSQL 概述

## 1.1 数据库的发展

> 单机MySQL时代

![在这里插入图片描述](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705643.png)

90年代,一个网站的访问量一般不会太大，单个数据库完全够用。随着用户增多，网站出现以下问题：

- 数据量增加到一定程度，单机数据库就放不下了

- 数据的索引（B+ Tree）,一个机器内存也存放不下

- 访问量变大后（读写混合），一台服务器承受不住。

> [Memcached](https://so.csdn.net/so/search?q=Memcached&spm=1001.2101.3001.7020)(缓存) + Mysql + 垂直拆分（读写分离）

网站80%的情况都是在读，每次都要去查询数据库的话就十分的麻烦！所以说我们希望减轻数据库的压力，我们可以使用缓存来保证效率！

![在这里插入图片描述](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705908.png)

优化过程经历了以下几个过程：

- 优化数据库的数据结构和索引(难度大)

- 文件缓存，通过IO流获取比每次都访问数据库效率略高，但是流量爆炸式增长时候，IO流也承受不了

- MemCache,当时最热门的技术，通过在数据库和数据库访问层之间加上一层缓存，第一次访问时查询数据库，将结果保存到缓存，后续的查询先检查缓存，若有直接拿去使用，效率显著提升。

> 分库分表 + 水平拆分 + Mysql集群

![在这里插入图片描述](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705809.png)

> 如今最近的年代

如今信息量井喷式增长，各种各样的数据出现（用户定位数据，图片数据等），大数据的背景下关系型数据库（RDBMS）无法满足大量数据要求。Nosql数据库就能轻松解决这些问题。

用户的个人信息，社交网络，地理位置。用户自己产生的数据，用户日志等等爆发式增长！
这时候我们就需要使用NoSQL数据库的，Nosql可以很好的处理以上的情况！

![在这里插入图片描述](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705870.png)



## 1.2 什么是NoSQL

`NoSQL = Not Only SQL（不仅仅是SQL）`Not Only Structured Query Language

关系型数据库：列+行，同一个表下数据的结构是一样的。

非关系型数据库：数据存储没有固定的格式，并且可以进行横向扩展。

NoSQL泛指非关系型数据库，随着web2.0互联网的诞生，传统的关系型数据库很难对付web2.0时代！尤其是超大规模的高并发的社区，暴露出来很多难以克服的问题，NoSQL在当今大数据环境下发展的十分迅速，Redis是发展最快的。

## 1.3 NoSQL 特点

1. 方便扩展（数据之间没有关系，很好扩展！）
2. 大数据量高性能（Redis一秒可以写8万次，读11万次，NoSQL的缓存记录级，是一种细粒度的缓存，性能会比较高！）
3. 数据类型是多样型的！（不需要事先设计数据库，随取随用）
4. 传统的 RDBMS 和 NoSQL比较：

| 传统的 RDBMS(关系型数据库)         | **Nosql**                                            |
| ---------------------------------- | ---------------------------------------------------- |
| 结构化组织                         | 不仅仅是数据                                         |
| SQL                                | 没有固定的查询语言                                   |
| 数据和关系都存在单独的表中 row col | 键值对存储，列存储，文档存储，图形数据库（社交关系） |
| 操作，数据定义语言                 | 最终一致性                                           |
| 严格的一致性                       | CAP定理和BASE                                        |
| 基础的事务                         | 高性能，高可用，高扩展                               |

## 1.4 3V + 3高

大数据时代的3V ：主要是**描述问题**的

1. 海量Velume
2. 多样Variety
3. 实时Velocity

大数据时代的3高 ： 主要是**对程序的要求**

1. 高并发
2. 高可扩
3. 高性能

真正在公司中的实践：NoSQL + RDBMS 一起使用才是最强的。

## 1.5 NoSQL四大分类

> KV键值对

- 新浪：**Redis**
- 美团：Redis + Tair
- 阿里、百度：Redis + Memcache

> 文档型数据库（bson数据格式）

- **MongoDB**(掌握)
  - 基于分布式文件存储的数据库。C++编写，用于处理大量文档。
  - MongoDB是RDBMS和NoSQL的中间产品。MongoDB是非关系型数据库中功能最丰富的，NoSQL中最像关系型数据库的数据库。
- ConthDB

> 列存储数据库

- **HBase**(大数据必学)
- 分布式文件系统

> 图关系数据库

用于广告推荐，社交网络

- **Neo4j**
- InfoGrid

| 分类                    | 举例                                               | **典型应用场景**                                             | **数据模型**                                    | **优点**                                                     | 缺点                                                         |
| ----------------------- | -------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **键值对（key-value）** | Tokyo Cabinet/Tyrant, Redis, Voldemort, Oracle BDB | 内容缓存，主要用于处理大量数据的高访问负载，也用于一些日志系统等等。 | Key 指向 Value 的键值对，通常用hash table来实现 | 查找速度快                                                   | 数据无结构化，通常只被当作字符串或者二进制数据               |
| **列存储数据库**        | Cassandra, HBase, Riak                             | 分布式的文件系统                                             | 以列簇式存储，将同一列数据存在一起              | 查找速度快，可扩展性强，更容易进行分布式扩展                 | 功能相对局限                                                 |
| **文档型数据库**        | CouchDB, MongoDB                                   | Web应用（与Key-Value类似，Value是结构化的，不同的是数据库能够了解Value的内容） | Key-Value对应的键值对，Value为结构化数据        | 数据结构要求不严格，表结构可变，不需要像关系型数据库一样需要预先定义表结构 | 查询性能不高，而且缺乏统一的查询语法。                       |
| **图形(Graph)数据库**   | Neo4J, InfoGrid, Infinite Graph                    | 社交网络，推荐系统等。专注于构建关系图谱                     | 图结构                                          | 利用图结构相关算法。比如最短路径寻址，N度关系查找等          | 很多时候需要对整个图做计算才能得出需要的信息，而且这种结构不太好做分布式的集群 |



# 2 Redis 入门

## 2.1 什么是Redis

Redis（Remote Dictionary Server )，即远程字典服务。

是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

## 2.2 Redis能该干什么

1. 缓存

   String类型

   例如：热点数据缓存（例如报表、明星出轨），对象缓存、全页缓存、可以提升热点数据的访问数据。

2. 数据共享分布式

   String 类型，因为 Redis 是分布式的独立服务，可以在多个应用之间共享

   例如：分布式Session

	```
    <dependency> 
     <groupId>org.springframework.session</groupId> 
     <artifactId>spring-session-data-redis</artifactId> 
    </dependency>
	```

3. 分布式锁

   String 类型setnx方法，只有不存在时才能添加成功，返回true

    ```
    public static boolean getLock(String key) {
        Long flag = jedis.setnx(key, "1");
        if (flag == 1) {
            jedis.expire(key, 10);
        }
        return flag == 1;
    }

    public static void releaseLock(String key) {
        jedis.del(key);
    }
    ```

4. 全局ID

    int类型，incrby，利用原子性

    incrby userid 1000

    分库分表的场景，一次性拿一段

5. 计数器

    int类型，incr方法

    例如：文章的阅读量、微博点赞数、允许一定的延迟，先写入Redis再定时同步到数据库

6. 限流

    int类型，incr方法

    以访问者的ip和其他信息作为key，访问一次增加一次计数，超过次数则返回false

7. 位统计

    String类型的bitcount（1.6.6的bitmap数据结构介绍）

    字符是以8位二进制存储的

    ```
    set k1 a
    setbit k1 6 1
    setbit k1 7 0
    get k1 
    /* 6 7 代表的a的二进制位的修改
    a 对应的ASCII码是97，转换为二进制数据是01100001
    b 对应的ASCII码是98，转换为二进制数据是01100010

    因为bit非常节省空间（1 MB=8388608 bit），可以用来做大数据量的统计。
    */
    ```

    例如：在线用户统计，留存用户统计

    ```
    setbit onlineusers 01 
    setbit onlineusers 11 
    setbit onlineusers 20
    ```

    支持按位与、按位或等等操作

    ```
    BITOPANDdestkeykey[key...] ，对一个或多个 key 求逻辑并，并将结果保存到 destkey 。       
    BITOPORdestkeykey[key...] ，对一个或多个 key 求逻辑或，并将结果保存到 destkey 。 
    BITOPXORdestkeykey[key...] ，对一个或多个 key 求逻辑异或，并将结果保存到 destkey 。 
    BITOPNOTdestkeykey ，对给定 key 求逻辑非，并将结果保存到 destkey 。
    ```

    计算出7天都在线的用户

    ```
    BITOP "AND" "7_days_both_online_users" "day_1_online_users" "day_2_online_users" ...  "day_7_online_users"
    ```

8. 购物车

    String 或hash。所有String可以做的hash都可以做

    ![image-20220820012733042](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208200127123.png)

    - key：用户id；field：商品id；value：商品数量。
    - +1：hincr。-1：hdecr。删除：hdel。全选：hgetall。商品数：hlen。

9. 用户消息时间线timeline

    list，双向链表，直接作为timeline就好了。插入有序

10. 消息队列

    List提供了两个阻塞的弹出操作：blpop/brpop，可以设置超时时间

    - blpop：blpop key1 timeout 移除并获取列表的第一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
    - brpop：brpop key1 timeout 移除并获取列表的最后一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。

    上面的操作。其实就是java的阻塞队列。学习的东西越多。学习成本越低

    - 队列：先进先除：rpush blpop，左头右尾，右边进入队列，左边出队列
    - 栈：先进后出：rpush brpop

11. 抽奖

    自带一个随机获得值

    ```
    spop myset
    ```

12. 点赞、签到、打卡

    ![image-20220820012554501](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208200125593.png)

    假如上面的微博ID是t1001，用户ID是u3001

    用 like:t1001 来维护 t1001 这条微博的所有点赞用户

    - 点赞了这条微博：sadd like:t1001 u3001
    - 取消点赞：srem like:t1001 u3001
    - 是否点赞：sismember like:t1001 u3001
    - 点赞的所有用户：smembers like:t1001
    - 点赞数：scard like:t1001

    是不是比数据库简单多了。

13. 商品标签

    ![image-20220820012802757](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208200128814.png)

    老规矩，用 tags:i5001 来维护商品所有的标签。

    - sadd tags:i5001 画面清晰细腻
    - sadd tags:i5001 真彩清晰显示屏
    - sadd tags:i5001 流程至极

14. 商品筛选

    ```
    // 获取差集
    sdiff set1 set2
    // 获取交集（intersection ）
    sinter set1 set2
    // 获取并集
    sunion set1 set2
    ```

    ![image-20220820012712617](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208200127701.png)

    假如：iPhone11 上市了

    ```
    sadd brand:apple iPhone11

    sadd brand:ios iPhone11

    sad screensize:6.0-6.24 iPhone11

    sad screentype:lcd iPhone 11
    ```

    赛选商品，苹果的、ios的、屏幕在6.0-6.24之间的，屏幕材质是LCD屏幕

    ```
    sinter brand:apple brand:ios screensize:6.0-6.24 screentype:lcd
    ```

15. 用户关注、推荐模型

    follow 关注 fans 粉丝

    相互关注：

    - sadd 1:follow 2
    - sadd 2:fans 1
    - sadd 1:fans 2
    - sadd 2:follow 1

    我关注的人也关注了他(取交集)：

    - sinter 1:follow 2:fans

    可能认识的人：

    - 用户1可能认识的人(差集)：sdiff 2:follow 1:follow
    - 用户2可能认识的人：sdiff 1:follow 2:follow

16. 排行榜

    id 为6001 的新闻点击数加1：`zincrby hotNews:20190926 1 n6001`

    获取今天点击最多的15条：`zrevrange hotNews:20190926 0 15 withscores`

    ## 2.3 Redis 特性

    1. 多样的数据类型
    2. 持久化
    3. 集群
    4. 事务
    5. ……

## 2.4 Redis官网

https://redis.io/

## 2.5 Redis 安装

参考文档

# 3 Redis 性能测试

## 3.1 参数
| 序号 | 选项                          | 描述                                       | 默认值    |
| ---- | ----------------------------- | ------------------------------------------ | --------- |
| 1    | **-h**                        | 指定服务器主机名                           | 127.0.0.1 |
| 2    | **-p**                        | 指定服务器端口                             | 6379      |
| 3    | **-s**                        | 指定服务器 socket                          |           |
| 4    | **-c**                        | 指定并发连接数                             | 50        |
| 5    | **-n**                        | 指定请求数                                 | 10000     |
| 6    | **-d**                        | 以字节的形式指定 SET/GET 值的数据大小      | 2         |
| 7    | **-k**                        | 1=keep alive 0=reconnect                   | 1         |
| 8    | **-r**                        | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| 9    | **-P**                        | 通过管道传输 <numreq> 请求                 | 1         |
| 10   | **-q**                        | 强制退出 redis。仅显示 query/sec 值        |           |
| 11   | **--csv**                     | 以 CSV 格式输出                            |           |
| 12   | ***-l*** **（L 的小写字母）** | 生成循环，永久执行测试                     |           |
| 13   | **-t**                        | 仅运行以逗号分隔的测试命令列表。           |           |
| 14   | ***-I*** **（i 的大写字母）** | Idle 模式。仅打开 N 个 idle 连接并等待。   |           |

## 3.2 测试步骤
1. 设置请求

`100 个连接 10000个请求`

```bash
redis-benchmark -c 100 -n 10000
```

2. 测试分析

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705047.png)

# 4 Redis 的基础知识

## 4.1 数据库
redis 默认有16个数据库

默认使用第0个数据库

## 4.2 基本命令
### 4.2.1 切换数据库
```bash
& select [index]
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705654.png)

### 4.2.2 查看数据库的大小
```bash
& DBSIZE
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705779.png)

### 4.2.3 查看数据库所有的键
```bash
& keys *
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705808.png)

### 4.2.4 清空当前数据库
```bash
& flushdb
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705884.png)

### 4.2.5 清空所有数据库
```bash
& flushall
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705878.png)

### 4.2.6 判断键是否存在
```bash
& EXISTS [key]
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705379.png)
> 如果存在返回1 ，如果不存在则返回0

### 4.2.7 移除单个键
```bash
& MOVE [key] 1

# 1 表示当前数据库
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705617.png)
> 如果成功返回1，如果键不存在则返回0

### 4.2.8 设置值的过期时间
```bash
& EXPIRE [key] [second]
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705544.png)
> 如果成功返回1，如果键不存在则返回0

### 4.2.9 查看键的剩余时间
```bash
& ttl [key]
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705651.png)

> 正数则是剩余时间，负数则是键过期

### 4.2.10 查看值的类型
```bash
& type [key]
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705629.png)

# 5 Redis 的基本数据类型
## 5.1 String 字符串
### 5.1.1 命令
1. 设置单个值


```bash
# 普通设置值(键已经存在则覆盖)
set [key] [value]

# 设置时间参数 ex:秒级 px:毫秒级
# nx:键不存在才能设置，xx:键存在时才能设置（更新）
set [key] [value] [ex seconds] [px milliseconds] [nx|xx]  

# 设置值并设置过期时间
setex [key] [second] [value]

# 值不存在时设置(常用于分布式锁)
setnx [key] [value]

# 返回OK 表示成功
```

2. 获取单个值

```bash
get [key]

# 如果值不存在，则返回 nil
```

3. 设置多个值


```bash
 mset k1 v1 k2 v2
```
> 原子性：要么都成功，要么都失败

4. 获取多个值

```bash
mget k1 k2
```
> 当值不存在时，返回nil

5. 先获取再设置值


```bash
getset k1 v1
```
> 如果不存在，则返回 nil,并先建一个值
> 如果存在值，获取原来的值，并设置新的值

6. 追加值

```bash
append [key] [newValue]

# 如果键存在，则在值的后面追加值，返回值的长度
# 如果键不存在，则新建一个值，返回值的长度
```

7. 截取字符串

```bash
getrange [key] [start] [end] 

# 截取字符串中下标为 [0,1]的子字符串
getrange key 0 1 
# 截取全部的字符串(相当于 get [key])
getrange key 0 -1
```

8. 替换字符串

```bash
setrange [key] [offset] [value]

# 原始值为 javajava
# 替换掉位置 4后的值为 coo
# 在字符串中间替换，替换子字符串长度 = 替换字符串长度
setrange key 4 coo

# 原始值为 javajava
# 当替换字符串长度 > 替换位置到最后的字符串的长度,在字符串末尾添加值
setrange key 7 coo
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705957.png)
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705156.png)

9. 自增

```bash
incr [key]

# 返回自增后的值
```

10. 自减

```bash
decr [key]

# 返回自减后的值
```

11. 自增指定长度

```bash
incrby [key] [increment]

# 返回自增后的值
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705261.png)

12. 自减指定长度

```bash
decrby [key] [increment]

# 返回自减后的值
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705580.png)

13. 获取值的长度

```bash
strlen [key]

# 如果键不存在，则返回 0
```

14. 判断键是否存在

```bash
exist [key]

# 如果键不存在则报错
```

### 5.1.2 保存对象的方式
1. 方式一

```bash
usr:{id} { json 序列化 }
```

2. 方式二

```bash
user:{id}:{field1} {value1}
user:{id}:{field2} {value2}
```

### 5.1.3 使用场景
**String 的阅读场景：不仅可以设置字符串，也可以是数字**
1. 计数器
	1. 统计多单位的数量
	2. 统计粉丝数等
2. 对象缓存存储


## 5.2 List 列表
### 5.2.1 特点
- list 是有序且可重复的
- list可以用作消息对列，栈和阻塞队列使用

### 5.2.2 命令
1. 增加元素
```Bash
# 从列表的左侧添加元素
LPUSH list v1 v2

# 向列表的右侧添加元素
RPUSH list r1 r2

# 返回结果为添加后列表中元素的个数
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705362.png)

2. 删除元素
```Bash
# 从左边弹出一个元素
LPOP list

# 从右边弹出一个元素
RPOP list

# 返回结果为弹出的元素的值
```
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705348.png)!

3. 删除指定元素
```Bash
# 从左到右删除指定数量的指定元素
LREM {list} {count} {value}
LREM list 1 v1

# 返回结果为删除元素的数量
```

4. 将列表中的最后一个元素移动新的列表中
```Bash
# 将列表中的尾部的元素移除，从新的列表中的左侧添加
RPOPLPUSH {list} {newList}

RPOPLPUSH list list1
```

5. 遍历列表
```Bash
# 遍历整个列表
LRANGE list 0 -1

# 遍历区间列表
LRANGE list 0 5 # [0,5]
```

6. 获取指定下标的值
```Bash
LINDEX [list] [index]
LINDEX list 2
```

> 可以实现阻塞队列：当 lindex list 0  没有值时，队列阻塞，一有值就消费


7. 获取列表元素个数
```Bash
 LLEN list
```

8. 插入值
```Bash
LINSERT list [after|after] value newValue
LINSERT list after value newValue

# 返回结果为列表中的元素数量
```

9. 判断列表是否存在
```Bash
EXIST key
```

10. 更新指定下标的元素
```Bash
# 更新前要先检查列表是否存在，不存在更新会报错
EXISTS list

LSET list 0 item
```

11. 截取列表元素
```Bash
LTRIM {list} {start} {end}

# 保留区间的元素
LTRIM list 0 3 #[0,3]

# 保留全部的元素
LTRIM list 0 -1 
```

### 5.2.3 使用场景
1. 消息排队
2. 左进右出：消息队列
3. 左进左出：栈

### 5.2.4 小结
1. list 是一个链表，before Node after ,left ,right 都可以插入值
2. 如果key 不存在，则创建新的链表
3. 如果key 存在，则新增内容
4. 如果移除了所有的值，也代表不存在
5. 在两边插入值，效率最高！中间元素，相对来说效率较低！

## 5.3 Set 集合
### 5.3.1 特点
无序且不重复
所有的命令都是 S 开头

### 5.3.2 命令
1. 增加元素
```Bash
SADD set v1 v2

# 成功则返回添加到set 中的元素数量
# 如果添加重复的值，则不允许添加
```

2. 查看所有的值
```Bash
SMEMBERS set
```

3. 获取集合的长度
```Bash
SCARD set
```

4. 删除指定元素
```Bash
SREM set value1 value2 ……
```

5. 删除随机元素
```Bash
SPOP {set} {count}
 
SPOP set 1
```

6. 抽取随机元素
```Bash
SRANDMEMBER {set} {count}
SRANDMEMBER set 2
```

7. 查看是否存在指定元素
```Bash
SISMEMBER set value
```

8. 将指定的元素移动到另外的set中
```Bash
SMOVE set  newset value
```

9. 差集
```Bash
# 准备工作
127.0.0.1:6379> sadd set1 a b c d e f g
(integer) 7
127.0.0.1:6379> sadd set2 c d e f g h i j k
(integer) 9

# 求差集
127.0.0.1:6379> SDIFF set1 set2

# 结果
1) "a"
2) "b"
```

10. 交集
```Bash
# 准备工作
127.0.0.1:6379> sadd set1 a b c d e f g
(integer) 7
127.0.0.1:6379> sadd set2 c d e f g h i j k
(integer) 9

11. 差集
```bash
127.0.0.1:6379> SINTER set1 set2

# 结果
1) "c"
2) "f"
3) "d"
4) "g"
5) "e"
```

12. 并集
```Bash
# 准备工作
127.0.0.1:6379> sadd set1 a b c d e f g
(integer) 7
127.0.0.1:6379> sadd set2 c d e f g h i j k
(integer) 9

# 求并集
127.0.0.1:6379> SUNION set1 set2

# 结果
 1) "c"
 2) "h"
 3) "i"
 4) "g"
 5) "e"
 6) "f"
 7) "k"
 8) "j"
 9) "b"
10) "d"
11) "a"
```

### 5.3.3 应用场景
- 将所有的关注的人都放在set 集合中
- 将所有的粉丝都放在set 集合中
- 可以完成 共同关注，共同爱好，二度好友（推荐好友） 

## 5.4 HashMap 集合
### 5.4.1 命令
1. 设置单个元素
```Bash
HSET hash field value
```

2. 集合不存在时创建
```Bash
HSETNX hashnx field value
```

3. 设置多个元素
```Bash
HMSET hash field1 value1 field2 value2
```

4. 获取单个元素
```Bash
HGET hash field
```

5. 获取多个元素
```Bash
HMGET hash field1 field2
```

6. 获取所有的键
```Bash
HKEYS hash
```

7. 获取所有的值
```Bash
HVALS hash
```

8. 获取所有元素
```Bash
HGETALL hash
```

9. 删除指定元素
```Bash
# 删除一个元素
HDEL hash field

# 删除多个元素
HDEL hash field1 field2 
```

10. 获取元素个数
```Bash
HLEN hash
```

11. 判断元素是否存在
```Bash
HEXISTS hash field
```

12. 自增步长
```Bash
HINCRBY  {key} {filed} {increment}

HINCRBY hash num 1

# 返回值是自增后的数
```

### 5.4.2 应用场景
- 对象存储（用户信息）
- 经常变动的信息

## 5.5 Zset 有序集合
### 5.5.1 特点
在set 的基础上增加一个值（成绩）

### 5.5.2 命令
1. 添加元素
```Bash
ZADD key [NX|XX] [CH] [INCR] score member [score member ...]

# 向集合中添加一个值
ZADD zset 1 value1

# 向集合中添加多个值
ZADD zset 1 value1  2 value2

# nx 集合不存在时添加值
# xx 集合存在时添加值,用户更新
ZADD zset [nx|xx] 1 value1

# ch:返回操作后，元素发生变化的的个数
zadd zset ch 1 value1 2 value2 3 value3

# incr:对socre 做增加，相当于zincrby
zadd zset incr 4 value4
```

2. 获取元素
```Bash
# 获取区间元素 [1,3]
ZRANGE zset 1 3

# 获取全部元素
ZRANGE zset 0 -1

# 显示元素 和 成绩
ZRANGE zset 0 -1 WITHSCORES
```

3. 按照成绩排序
```Bash
# ===== 升序排序 =====
# 成绩区间获取元素
ZRANGEBYSCORE zset 0 3

# 显示全部元素
ZRANGEBYSCORE zset -inf +inf

# 显示元素 和 成绩 
ZRANGEBYSCORE zset -inf +inf WITHSCORES

# ===== 降序排序 =====
# 成绩区间获取元素
ZREVRANGE zset 0 3

# 显示全部元素
ZREVRANGE zset -inf +inf

# 显示元素 和 成绩 
ZREVRANGE zset -inf +inf WITHSCORES
```

> 区间：-Inf 无穷小 +inf 无穷大


4. 移除指定元素
```Bash
ZREM zset value1 value2
```

5. 查看元素个数
```Bash
ZCARD zset

```

ZCARD zset


6. 查看成绩区间中元素个数
```Bash
# 查看全部的元素个数
zcount zset -inf +inf

# 查看指定成绩区间的元素数量
zcount zset 3 4 #[3,4]
```

### 5.5.3 应用场景
- 存储班级成绩，存储工资表
- 带权重的判断 1：普通用户 2：vip 用户 
- 排行榜应用，取TopN 测试

# 6 Redis的特殊数据类型
## 6.1 Geospatial 地理位置
### 6.1.1 命令
1. 添加位置
```Bash
# 添加位置（1）
GEOADD key longitude latitude member [longitude latitude member ...]

# 添加一个地理信息
GEOADD geo 118.80 32.06 nanjing

# 添加多个地理信息
GEOADD geo 118.4 34.23 beijing 114.4 34.23 shanghai
```

> p1 : 经度 维度 名称

2. 获取地理信息
```Bash
GEOPOS geo beijing nanjing
```

3. 获取两个地理位置的距离
```Bash
GEODIST geo beijing nanjing km
```

> m 米
> km 千米
> mi 英里
> ft 尺

4. 获取指定范围内地理信息合集
```Bash
 GEORADIUS geo 118.8 34 1000 km
```

> 附近的人的实现方式

5. 获取元素指定范围内地理信息合集
```Bash
# 显示到中间距离1000km 的城市信息
GEORADIUSBYMEMBER geo beijing 1000 km

# 显示到中间距离的位置
GEORADIUS geo 118 32 400 km withdist

# 显示其他城市的地理位置
GEORADIUS geo 118 32 400 km withcoord

# 筛选结果
GEORADIUS geo 118 32 400 km count 1
```

6. 获得geoHash(少用)
```Bash
GEOHASH geo beijing
```

7. 查询所有的地理位置
```Bash
# 查询所有的地理位置
ZRANGE geo 0 -1
```

8. 删除指定的元素
```Bash
ZREM geo beijing
```

### 6.1.2 应用场景
1. 定位
2. 附近的人
3. 打车距离的计算

## 6.2 Hyperloglog 基数统计
### 6.2.1 特点
1. 用于基数统计
2. 占用内存极小
3. 有一定的误差：0.81%
	1. 如果可以出错，就使用Hyperloglog 
	2. 如果不允许出错，就使用set 或其他集合

> **基数** ： 数据集中不重复的数字的个数

### 6.2.2 命令
1. 添加元素
```Bash
PFADD hyper a b v d h f d s
```

2. 统计基数
```Bash
PFCOUNT hyper hyper2
```

3. 合并集合到新的集合中
```Bash
PFMERGE newhyper hyper hyper2
```

### 6.2.3 应用场景
- 统计网站的访问量

## 6.3 Bitmap 位存储
### 6.3.1 特点
都是操作二进制进行计算，就只有1 和0  两种状态

### 6.3.2 命令
1. 设置状态
```Bash
# 设置值
SETBIT bit:1 1 1
SETBIT bit:1 2 1
SETBIT bit:1 3 1
```

2. 获取状态
```Bash
GETBIT bit:1 2
```

3. 统计状态为1 的个数
```Bash
BITCOUNT bit
```

### 6.3.3 应用场景
**表示两个状态**
- 是否打卡
- 用户是否活跃
- 用户是否登陆

# 7 Redis的事务
## 7.1 事务
### 7.1.1 特点
1. Redis 事务的本质是：一组命令的集合！一个事务中的所有的命令都会被序列化，在事务执行的过程中，会按照顺序执行！
2. 一致性，顺序性，排他性
3. Redis 事务中没有隔离级别的概念
4. 所有的命令都保存在事务中，并没有直接执行
5. Redis 的单条命令是保持原子型的，但是事务是不保证原子性！
6. redis 事务的过程
	- 开启事务
	- 命令入队
	- 执行事务

### 7.1.2 命令
1. 开启事务
```Bash
MULTI
```

2. 执行事务
```Bash
EXEC
```

3. 放弃事务
```Bash
DISCARD
```

### 7.1.3 异常处理
1. 编译型异常

> 代码有问题！命令报错！，事务中的所有的命令都不会被执行。

```Bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v1
QUEUED
127.0.0.1:6379> set k3 v1
QUEUED
127.0.0.1:6379> GETSET k3 # 代码报错
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379> exec
(error) EXECABORT Transaction discarded because of previous errors.
```

2. 运行型异常

> 如果事务队列中存在语法性，执行命令的时候，其他命令可以执行，错误命令会抛出异常。

```Bash
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> incr k1 # 错误
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> EXEC
1) OK
2) (error) ERR value is not an integer or out of range
3) OK
```

## 7.2 乐观锁
### 7.2.1  悲观锁和乐观锁
1. 悲观锁
认为任何时候都会出现问题，无论做什么都会加锁

2. 乐观锁
认为任何时候都不会出现问题，所以不会上锁，更新数据的时候会先判断是否有线程修过过这个数据。

### 7.2.2 Redis 实现乐观锁
> 可以使用watch 当做redis 乐观锁的操作！

1. 正常执行
```Bash
# 设置值
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> set money 100
OK

# 监视值
127.0.0.1:6379> watch money
OK

# 开启事务
127.0.0.1:6379> multi
OK
127.0.0.1:6379> DECRBY money 20
QUEUED
127.0.0.1:6379> INCRBY out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
```

2. 多线程执行
```Bash
# 线程一：
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money out
OK

# 线程二：
127.0.0.1:6379> keys *
1) "out"
2) "money"
127.0.0.1:6379> incr money
(integer) 101
127.0.0.1:6379> decr out
(integer) -1
127.0.0.1:6379>

# 线程三：
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> incr money
QUEUED
127.0.0.1:6379> DECR out
QUEUED
127.0.0.1:6379> EXEC
```

3. 解锁
```Bash
UNWATCH
```
> 如果发现事务执行失败，就先解锁。


# 8 Springboot 集成 Redis
## 8.1 官方推荐工具 Jedis
### 8.1.1 jedis 简介
Jedis 是Redis 官方推荐的Java连接工具！

### 8.1.2 Jedis 操作 Redis
1. 创建Maven 项目

2. 导入依赖
```XML
<!-- 导入jedis -->
<!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.2.0</version>
</dependency>

<!-- 导入 fastjson -->
<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.76</version>
</dependency>
```

3. 连接Redis
```Java
public class ConnectionTest {
    public void connectRedis(){
        // 连接数据库
        Jedis jedis = new Jedis("127.0.0.1",6379);

        // 测试连接
        String ping = jedis.ping();
        System.out.println(ping);
    }
}
```

4. Jedis 对key操作
操作：
| 方法                               | 描述                              |
| ---------------------------------- | --------------------------------- |
| jedis.flush                        |                                   |
| jedis.flushDB                      | 清空数据                          |
| boolean jedis.exists(String key)   | 判断某个键是否存在                |
| jedis.set(String key,String value) | 新增键值对（key,value）           |
| `Set<String> jedis.keys(*)`        | 获取所有key                       |
| jedis.del(String key)              | 删除键为key的数据项               |
| jedis.expire(String key,int i)     | 设置键为key的过期时间为i秒        |
| int jedis.ttl(String key)          | 获取建委key数据项的剩余时间（秒） |
| jedis.persist(String key)          | 移除键为key属性项的生存时间限制   |
| jedis.type(String key)             | 查看键为key所对应value的数据类型  |

```Java
Jedis jedis = new Jedis("127.0.0.1", 6379);
// 判断某个值是否存在
Boolean user = jedis.exists("user");
System.out.println("判断某个值是否存在：" + user);

// 匹配数据库中的键
Set<String> keys = jedis.keys("*");
System.out.println("匹配数据库中的键：" + keys);

// 查看值的类型
String type = jedis.type("key");
System.out.println("查看值的类型：" + type);

// 随机返回key空间的一个
String randomKey = jedis.randomKey();
System.out.println("随机返回key空间的一个：" + randomKey);

// 重命名key
String rename = jedis.rename("oldName", "newName");
System.out.println("重命名key：" + rename);

// 按索引查询
String select = jedis.select(7);
System.out.println("按索引查询：" + select);

// 查询数据中的key 的数量
Long aLong = jedis.dbSize();
System.out.println("查询数据中的key 的数量" + aLong);

// 清空当前数据库
String flushDB = jedis.flushDB();
System.out.println("清空当前数据库：" + flushDB);

// 清空所有的数据库
String flushAll = jedis.flushAll();
System.out.println("清空所有数据库：" + flushAll);

```
2. Jedis 对 String 操作
操作：
| 语法                                                  | 描述                                           |
| ----------------------------------------------------- | ---------------------------------------------- |
| jedis.set(String key,String value)                    | 增加（或覆盖）数据项                           |
| jedis.setnx(String key,String value)                  | 不覆盖增加数据项（重复的不插入）               |
| jedis.setex(String ,int t,String value)               | 增加数据项并设置有效时间                       |
| jedis.del(String key)                                 | 删除键为key的数据项                            |
| jedis.get(String key)                                 | 获取键为key对应的value                         |
| jedis.append(String key, String s)                    | 在key对应value 后边扩展字符串 s                |
| jedis.mset(String k1,String V1,String K2,String V2,…) | 增加多个键值对                                 |
| String[] jedis.mget(String K1,String K2,…)            | 获取多个key对应的value                         |
| `jedis.del(new String[](String K1,String K2,.... ))`  | 删除多个key对应的数据项                        |
| String jedis.getSet(String key,String value)          | 获取key对应value并更新value                    |
| String jedis.getrang(String key , int i, int j)       | 获取key对应value第i到j字符 ，从0开始，包头包尾 |

```Java
Jedis jedis = new Jedis("127.0.0.1", 6379);

// 添加单个值
String set = jedis.set("key1", "value1");
System.out.println("设置单个值：" + set);

// 键不存在时设置多个值
Long setnx = jedis.setnx("key1", "value1");
System.out.println("键不存在时设置多个值：" + setnx);

// 设置值并设置有效时间
String setex = jedis.setex("keytime", 6, "value");
System.out.println("设置值并设置有效时间：" + setex);

try {
    TimeUnit.SECONDS.sleep(3);
} catch (InterruptedException e) {
    e.printStackTrace();
}

// 修改单个值
String newSet = jedis.set("key1", "v1");
System.out.println("修改单个值：" + newSet);

// 追加值
Long append = jedis.append("key1", "append_value");
System.out.println("追加值：" + append);

// 获取单个值
String key1 = jedis.get("key1");
System.out.println("获取单个值：" + key1);

// 删除单个键
Long del = jedis.del("key1");
System.out.println("删除单个值：" + del);

// 添加多个值
String mset = jedis.mset("k1", "v1", "k2", "v2", "k3", "v3", "k4", "v4");
System.out.println("设置多个值：" + mset);

// 获取多个值
List<String> mget = jedis.mget("k1", "k2");
System.out.println("获取多个值：" + mget);

// 删除多个值
Long delList = jedis.del("k1", "k2");
System.out.println("删除多个值：" + delList);

// 获取原址，更新为新值
String newGetSet = jedis.getSet("k1", "newValue");
System.out.println("获取原址，更新为新值：" + newGetSet);

// 截取字符串
String subK1 = jedis.getrange("k1", 2, 4);
System.out.println("截取字符串："+ subK1);
```
3. Jedis对List操作
操作：
| 语法                                                 | 描述                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| `jedis.lpush(String key, String v1, String v2,....)` | 添加一个List , 注意：如果已经有该List对应的key, 则按顺序在左边追加 一个或多个 |
| jedis.rpush(String key , String vn)                  | key对应list右边插入元素                                      |
| jedis.lrange(String key,int i,int j)                 | 获取key对应list区间[i,j]的元素，注：从左边0开始，包头包尾    |
| jedis.lrem(String key,int n , String val)            | 删除list中 n个元素val                                        |
| jedis.ltrim(String key,int i,int j)                  | 删除list区间[i,j] 之外的元素                                 |
| jedis.lpop(String key)                               | key对应list ,左弹出栈一个元素                                |
| jedis.rpop(String key)                               | key对应list ,右弹出栈一个元素                                |
| jedis.lset(String key,int index,String val)          | 修改key对应的list指定下标index的元素                         |
| jedis.llen(String key)                               | 获取key对应list的长度                                        |
| jedis.lindex(String key,int index)                   | 获取key对应list下标为index的元素                             |
| jedis.sort(String key)                               | 把key对应list里边的元素从小到大排序                          |

```Java
Jedis jedis = new Jedis("127.0.0.1", 6379);

// 向 List 中添加元素(左)
Long list0 = jedis.lpush("list", "java");
System.out.println("(左)添加一个元素：" + list0);
Long list1 = jedis.lpush("list", "python", "c++");
System.out.println("(左)添加多个元素：" + list1);

// 向 List 中添加元素(右)
Long list2 = jedis.rpush("list", "java");
System.out.println("(右)添加一个元素：" + list2);
Long list3 = jedis.rpush("list", "python", "c++");
System.out.println("(右)添加多个元素：" + list3);

// 获得区间的列表
List<String> lrange0 = jedis.lrange("list", 0, 3);
System.out.println("获取[0,3]的区间列表：" + lrange0);
//-1代表倒数第一个元素，-2代表倒数第二个元素,end为-1表示查询全部
List<String> lrange1 = jedis.lrange("list", 0, -1);
System.out.println("获取全部的区间列表：" + lrange1);

// 左出栈
String lpop = jedis.lpop("list");
System.out.println("左出栈：" + lpop);

// 右出栈
String rpop = jedis.rpop("list");
System.out.println("右出栈：" + rpop);

// 删除列表中的指定值
// 第二个参数为删除的个数（有重复时），后add进去的值先被删，类似于出栈
Long lrem = jedis.lrem("list", 1, "java");
System.out.println("删除列表中的指定值：" + lrem);

// 截取列表的中的值
String list = jedis.ltrim("list", 0, 2);
System.out.println("截取列表的中的值：" + list);

// 设置指定下标的内容
String lset = jedis.lset("list", 1, "newValue");
System.out.println("设置指定下标的内容：" + lset);

// 获取列表中元素个数
Long llen = jedis.llen("list");
System.out.println("获取列表中元素个数：" + llen);

// 获取指定下标的内容
String lindex = jedis.lindex("list", 0);
System.out.println("获取指定下标的内容：" + lindex);

// 排序并返回一个新的集合
jedis.lpush("sortedList", "3", "6", "2", "0", "7", "4");
System.out.println("sortedList排序前：" + jedis.lrange("sortedList", 0, -1));
List<String> sortedList = jedis.sort("sortedList");
System.out.println("sortedList排序前：" + sortedList);
```
5. Jedis 对Set操作
操作：
| 语法                                              | 操作                            |
| ------------------------------------------------- | ------------------------------- |
| jedis.sadd(String key,String v1,String v2,…)      | 添加一个set                     |
| jedis.smenbers(String key)                        | 获取key对应set的所有元素        |
| jedis.srem(String key,String val)                 | 删除集合key中值为val的元素      |
| jedis.srem(String key, Sting v1, String v2,…)     | 删除值为v1, v2 , …的元素        |
| jedis.spop(String key)                            | 随机弹出栈set里的一个元素       |
| jedis.scared(String key)                          | 获取set元素个数                 |
| jedis.smove(String key1, String key2, String val) | 将元素val从集合key1中移到key2中 |
| jedis.sinter(String key1, String key2)            | 获取集合key1和集合key2的交集    |
| jedis.sunion(String key1, String key2)            | 获取集合key1和集合key2的并集    |
| jedis.sdiff(String key1, String key2)             | 获取集合key1和集合key2的差集    |

```Java
Jedis jedis = new Jedis("127.0.0.1", 6379);

// 添加值
Long sadd = jedis.sadd("set", "v1", "v2");
System.out.println("添加值：" + sadd);

// 查询所有的值
Set<String> set = jedis.smembers("set");
System.out.println("查询所有的值：" + set);

// 随机的移除集合中的一个元素
String spopset = jedis.spop("set");
System.out.println("随机的移除集合中的一个元素：" + spopset);

// 查询元素个数
Long scard = jedis.scard("set");
System.out.println("查询元素个数：" + scard);

// 判断元素是否存在
Boolean sismember = jedis.sismember("set", "v1");
System.out.println("判断元素是否存在：" + sismember);

// 将set中删除v1并存入set1中：
Long smove = jedis.smove("set", "set1", "v1");
System.out.println("将set中删除v1并存入set1中：" + smove);

// ===================== 集合运算 =====================
jedis.sadd("eleSet1", "e1", "e2", "e4", "e3", "e0", "e8", "e7", "e5");
jedis.sadd("eleSet2", "e1", "e2", "e4", "e3", "e0", "e8");

// 交集
Set<String> sinter = jedis.sinter("eleSet1", "eleSet2");
System.out.println("交集：" + sinter);
// 并集
Set<String> sunion = jedis.sunion("eleSet1", "eleSet2");
System.out.println("并集：" + sunion);
// 差集
Set<String> sdiff = jedis.sdiff("eleSet1", "eleSet2");
System.out.println("差集：" + sdiff);
// 求交集并将交集保存到 dstkey 的集合
Long sinterstore = jedis.sinterstore("eleSet", "eleSet1", "eleSet2");
System.out.println("求交集并将交集保存到 dstkey 的集合：" + sinterstore + " " + jedis.smembers("eleSet") );
```

6. Jedis对Hash操作
操作：
| 语法                                              | 描述                           |
| ------------------------------------------------- | ------------------------------ |
| jedis.hmset(String key,Map map)                   | 添加一个Hash                   |
| jedis.hset(String key , String key, String value) | 向Hash中插入一个元素（K-V）    |
| jedis.hgetAll(String key)                         | 获取Hash的所有（K-V） 元素     |
| jedis.hkeys（String key）                         | 获取Hash所有元素的key          |
| jedis.hvals(String key)                           | 获取Hash所有元素 的value       |
| jedis.hincrBy(String key , String k, int i)       | 把Hash中对应的k元素的值 val+=i |
| jedis.hdecrBy(String key,String k, int i)         | 把Hash中对应的k元素的值 val-=i |
| jedis.hdel(String key , String k1, String k2,…)   | 从Hash中删除一个或多个元素     |
| jedis.hlen(String key)                            | 获取Hash中元素的个数           |
| jedis.hexists(String key,String K1)               | 判断Hash中是否存在K1对应的元素 |
| jedis.hmget(String key,String K1,String K2)       | 获取Hash中一个或多个元素value  |

```Java
Jedis jedis = new Jedis("127.0.0.1", 6379);

// 添加多个元素
HashMap<String, String> stringHashMap = new HashMap<String, String>();
stringHashMap.put("k1", "v1");
stringHashMap.put("k2", "v1");
stringHashMap.put("k3", "v1");
stringHashMap.put("k4", "v1");
stringHashMap.put("k5", "v1");
String hash = jedis.hmset("hash", stringHashMap);
System.out.println("添加多个元素：" + hash);

// 获取多个值
List<String> hmget = jedis.hmget("hash", "k1", "k3");
System.out.println("获取多个值：" + hmget);

// 添加单个元素
Long hset = jedis.hset("hash", "k6", "v6");
System.out.println("添加单个元素：" + hset);

// 获取单个值
String hget = jedis.hget("hash", "k1");
System.out.println("获取单个值：" + hget);

// 获取所有的键值对
Map<String, String> hgetAll = jedis.hgetAll("hash");
System.out.println("获取所有的键值对：" + hgetAll);

// 获取所有的键
Set<String> hkeys = jedis.hkeys("hash");
System.out.println("获取所有的键：" + hkeys);

// 获取所有的值
List<String> hvals = jedis.hvals("hash");
System.out.println("获取所有的值：" + hvals);

// 将key6保存的值加上一个整数，如果key6不存在则添加key6
Long k7 = jedis.hincrBy("hash", "k7", 6);
System.out.println("自增初始化：" + k7);
Long k7_1 = jedis.hincrBy("hash", "k7", 6);
System.out.println("自增：" + k7_1);

// 删除键值对
Long hdel = jedis.hdel("hash", "k3", "k4");
System.out.println("删除键值对：" + hdel);

// 获取键值对的个数
Long hlen = jedis.hlen("hash");
System.out.println("获取键值对的个数：" + hlen);

// 判断值是否存在
Boolean hexists = jedis.hexists("hash", "k1");
System.out.println("判断值是否存在：" + hexists);
```

7. Jedis对ZSet操作
操作：
| 语法                                             | 描述                                            |
| ------------------------------------------------ | ----------------------------------------------- |
| jedis.zadd(String key,Map map)                   | 添加一个ZSet                                    |
| jedis.hset(String key,int score , int val)       | 往 ZSet插入一个元素（Score-Val）                |
| jedis.zrange(String key, int i , int j)          | 获取ZSet 里下表[i,j] 区间元素Val                |
| jedis. zrangeWithScore(String key,int i , int j) | 获取ZSet 里下表[i,j] 区间元素Score - Val        |
| jedis.zrangeByScore(String , int i , int j)      | 获取ZSet里score[i,j]分数区间的元素（Score-Val） |
| jeids.zscore(String key,String value)            | 获取ZSet里value元素的Score                      |
| jedis.zrank(String key,String value)             | 获取ZSet里value元素的score的排名                |
| jedis.zrem(String key,String value)              | 删除ZSet里的value元素                           |
| jedis.zcard(String key)                          | 获取ZSet的元素个数                              |
| jedis.zcount(String key , int i ,int j)          | 获取ZSet总score在[i,j]区间的元素个数            |
| jedis.zincrby(String key,int n , String value)   | 把ZSet中value元素的score+=n                     |

```Java
Jedis jedis = new Jedis("127.0.0.1", 6379);

jedis.flushDB();

// 添加多个元素
HashMap<String, Double> zaddMap = new HashMap<String, Double>();
zaddMap.put("caoJun",Double.valueOf("2"));
zaddMap.put("chenLong",Double.valueOf("4"));
zaddMap.put("hanXin",Double.valueOf("5"));
zaddMap.put("huangQiong",Double.valueOf("7"));
Long zadd = jedis.zadd("zadd", zaddMap);
System.out.println("添加多个元素："  + zadd);

// 获取区间内的元素
// 获取全部元素
Set<String> zrange0 = jedis.zrange("zadd", 0, -1);
System.out.println("获取全部元素：" + zrange0);
// 获取区间元素
Set<String> zrange1 = jedis.zrange("zadd", 0, 1);
System.out.println("获取区间元素：" + zrange1);
// 获取元素带成绩 Score - Val
Set<Tuple> zrangeWithScores = jedis.zrangeWithScores("zadd", 0, -1);
System.out.println("获取元素带成绩 Score - Val：" + zrangeWithScores);

// 获取score[i,j]分数区间的元素 Score-Val
Set<String> zrangeByScore = jedis.zrangeByScore("zadd", 0, 9);
System.out.println("获取score[i,j]分数区间的元素 Score-Val：" + zrangeByScore);

// 获取ZSet里value元素的Score
Double zscore = jedis.zscore("zadd", "caoJun");
System.out.println("获取ZSet里value元素的Score：" + zscore);

// 获取ZSet里value元素的score的排名
Long zrank = jedis.zrank("zadd", "caoJun");
System.out.println("获取ZSet里value元素的score的排名：" + zrank);

// 删除元素
Long zrem = jedis.zrem("zadd", "caoJun");
System.out.println("删除元素：" + zrem);

// 获取元素的个数
Long zcard = jedis.zcard("zadd");
System.out.println("获取元素的个数：" + zcard);

// 获取score在[i,j]区间的元素个数
Long zcount = jedis.zcount("zadd", 0, 1);
System.out.println("获取score在[i,j]区间的元素个数：" + zcount);

// 成绩自增
Double zincrby = jedis.zincrby("zadd", 4, "caoJun");
System.out.println("成绩自增：" + zincrby);
```
9. Jedis 排序操作
操作：
| 语法                                          | 描述                 |
| --------------------------------------------- | -------------------- |
| jedis.sort(String key,sortingParams.alpha())  | 队列按首字母z-a 排序 |
| jedis.sort(String key, sortingParams.asc() )  | 队列按数字升序排列   |
| jedis.sort(String key , sortingParams.desc()) | 队列按数字降序排列   |

```Java
Jedis jedis = new Jedis("127.0.0.1", 6379);

jedis.flushDB();

SortingParams sortingParams = new SortingParams();

jedis.lpush("numList", "4", "5", "2", "1", "-92", "98", "7");
// 队列按数字升序排列
List<String> numListUp = jedis.sort("numList", sortingParams.asc());
System.out.println("队列按数字升序排列：" + numListUp);
// 队列按数字升序降列
List<String> numListDown = jedis.sort("numList", sortingParams.desc());
System.out.println("队列按数字降序排列：" + numListDown);

jedis.lpush("nameList", "caoJun", "xieWei", "chenHe", "huangQiong", "hanXin", "liBai", "zhenZiDan");
// 首字母排序
List<String> nameList = jedis.sort("nameList", sortingParams.alpha());
System.out.println("首字母排序：" + nameList);
```

## 8.2 推荐使用工具 RedisTemplate
### 8.2.1 RedisTemplate 简介
SpringBoot 操作数据库：spring-data jpa jdbc mongodb redis

在SpringBoot2.x 之后，原来使用的redis 被替换成了 lettuce
- jedis 采用的是直接连接，多个线程操作时，是不安全的，如果想要安全，使用jedis pool 连接池，更像BIO 模式
- lettuce 采用的是netty ，实例可以作多个线程中进行共享，不存在线程不安全的情况下，可以减少线程数据了，更像NIO 模式
### 8.2.2 RedisTemplate 操作 Redis
1. 创建SpringBoot 项目

2. 导入依赖
```XML
<!--集成redis-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.49</version>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
</dependency>
```

3. 配置文件
```YAML
spring:
  redis:                    # redis 
    host: 127.0.0.1         # ip
    port: 6379              # 端口
    password:               # 密码
    database: 0             # 默认数据库（默认为 0）
    timeout: 1000           # 连接的超时时间
    lettuce:
      shutdown-timeout: 200 #关闭超时时间；单位毫秒
      pool:
        max-active: 8       # Redis 连接池最大连接数，默认为 8。如果为负则表示没有限制
        max-wait: -1        # Redis 连接池最大阻塞等待时间，默认为 -1。如果为负则没有限制
        max-idle: 8         # Redis 连接池中最大空闲连接，默认为 8
        min-idle: 0         # Redis 连接池中最小空闲连接，默认为 0
```

4. 重写redisTemplate模板

默认的 redisTemplate模板：
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705076.png)

> 默认redisTemplate 模板的序列化使用Java 默认的序列化，在存入字符串时，需要转义。所以要重写redisTemplate 模板。

```Java
@Configuration
public class RedisConfig {

    @Bean
    @SuppressWarnings("all")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
        template.setConnectionFactory(factory);
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        // key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        // hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        // value序列化方式采用jackson
        template.setValueSerializer(jackson2JsonRedisSerializer);
        // hash的value序列化方式采用jackson
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();

        return template;
    }
}
```
5. 封装redisTemplate Utils

```JAVA
package com.test.springboot.redistemplate.utils;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;
import org.springframework.util.CollectionUtils;

import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.TimeUnit;

@Component
public final class RedisUtil {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    // =============================common============================
    /**
     * 指定缓存失效时间
     * @param key  键
     * @param time 时间(秒)
     */
    public boolean expire(String key, long time) {
        try {
            if (time > 0) {
                redisTemplate.expire(key, time, TimeUnit.SECONDS);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据key 获取过期时间
     * @param key 键 不能为null
     * @return 时间(秒) 返回0代表为永久有效
     */
    public long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }


    /**
     * 判断key是否存在
     * @param key 键
     * @return true 存在 false不存在
     */
    public boolean hasKey(String key) {
        try {
            return redisTemplate.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除缓存
     * @param key 可以传一个值 或多个
     */
    @SuppressWarnings("unchecked")
    public void del(String... key) {
        if (key != null && key.length > 0) {
            if (key.length == 1) {
                redisTemplate.delete(key[0]);
            } else {
                redisTemplate.delete(CollectionUtils.arrayToList(key));
            }
        }
    }


    // ============================String=============================

    /**
     * 普通缓存获取
     * @param key 键
     * @return 值
     */
    public Object get(String key) {
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }

    /**
     * 普通缓存放入
     * @param key   键
     * @param value 值
     * @return true成功 false失败
     */

    public boolean set(String key, Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 普通缓存放入并设置时间
     * @param key   键
     * @param value 值
     * @param time  时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */

    public boolean set(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 递增
     * @param key   键
     * @param delta 要增加几(大于0)
     */
    public long incr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }


    /**
     * 递减
     * @param key   键
     * @param delta 要减少几(小于0)
     */
    public long decr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }


    // ================================Map=================================

    /**
     * HashGet
     * @param key  键 不能为null
     * @param item 项 不能为null
     */
    public Object hget(String key, String item) {
        return redisTemplate.opsForHash().get(key, item);
    }

    /**
     * 获取hashKey对应的所有键值
     * @param key 键
     * @return 对应的多个键值
     */
    public Map<Object, Object> hmget(String key) {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * HashSet
     * @param key 键
     * @param map 对应多个键值
     */
    public boolean hmset(String key, Map<String, Object> map) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * HashSet 并设置时间
     * @param key  键
     * @param map  对应多个键值
     * @param time 时间(秒)
     * @return true成功 false失败
     */
    public boolean hmset(String key, Map<String, Object> map, long time) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @param time  时间(秒) 注意:如果已存在的hash表有时间,这里将会替换原有的时间
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value, long time) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除hash表中的值
     *
     * @param key  键 不能为null
     * @param item 项 可以使多个 不能为null
     */
    public void hdel(String key, Object... item) {
        redisTemplate.opsForHash().delete(key, item);
    }


    /**
     * 判断hash表中是否有该项的值
     *
     * @param key  键 不能为null
     * @param item 项 不能为null
     * @return true 存在 false不存在
     */
    public boolean hHasKey(String key, String item) {
        return redisTemplate.opsForHash().hasKey(key, item);
    }


    /**
     * hash递增 如果不存在,就会创建一个 并把新增后的值返回
     *
     * @param key  键
     * @param item 项
     * @param by   要增加几(大于0)
     */
    public double hincr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, by);
    }


    /**
     * hash递减
     *
     * @param key  键
     * @param item 项
     * @param by   要减少记(小于0)
     */
    public double hdecr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, -by);
    }


    // ============================set=============================

    /**
     * 根据key获取Set中的所有值
     * @param key 键
     */
    public Set<Object> sGet(String key) {
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 根据value从一个set中查询,是否存在
     *
     * @param key   键
     * @param value 值
     * @return true 存在 false不存在
     */
    public boolean sHasKey(String key, Object value) {
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将数据放入set缓存
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSet(String key, Object... values) {
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 将set数据放入缓存
     *
     * @param key    键
     * @param time   时间(秒)
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSetAndTime(String key, long time, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().add(key, values);
            if (time > 0)
                expire(key, time);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 获取set缓存的长度
     *
     * @param key 键
     */
    public long sGetSetSize(String key) {
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 移除值为value的
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 移除的个数
     */

    public long setRemove(String key, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().remove(key, values);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    // ===============================list=================================

    /**
     * 获取list缓存的内容
     *
     * @param key   键
     * @param start 开始
     * @param end   结束 0 到 -1代表所有值
     */
    public List<Object> lGet(String key, long start, long end) {
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 获取list缓存的长度
     *
     * @param key 键
     */
    public long lGetListSize(String key) {
        try {
            return redisTemplate.opsForList().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 通过索引 获取list中的值
     *
     * @param key   键
     * @param index 索引 index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
     */
    public Object lGetIndex(String key, long index) {
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     */
    public boolean lSet(String key, Object value) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将list放入缓存
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     */
    public boolean lSet(String key, Object value, long time) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            if (time > 0)
                expire(key, time);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, List<Object> value) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     * @return
     */
    public boolean lSet(String key, List<Object> value, long time) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            if (time > 0)
                expire(key, time);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 根据索引修改list中的某条数据
     *
     * @param key   键
     * @param index 索引
     * @param value 值
     * @return
     */

    public boolean lUpdateIndex(String key, long index, Object value) {
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 移除N个值为value
     *
     * @param key   键
     * @param count 移除多少个
     * @param value 值
     * @return 移除的个数
     */

    public long lRemove(String key, long count, Object value) {
        try {
            Long remove = redisTemplate.opsForList().remove(key, count, value);
            return remove;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }

    }

}
```



### 8.2.3 序列化和反序列化案例

1. 编写User类

   ```java
   @Component
   @AllArgsConstructor
   @NoArgsConstructor
   @Data
   public class User {
       private String name;
       private int age;
   }
   ```

   

2. 测试连接

   ```java
   @Test
       void test() throws JsonProcessingException {
           // 真实开发都是使用 json 来传递对象
           User user = new User("张三", 2);
           String jsonUser = new ObjectMapper().writeValueAsString(user);
           redisTemplate.opsForValue().set("user", user);  //set("user", jsonUser)
           System.out.println(redisTemplate.opsForValue().get("user"));
       }
   ```

# 9 配置文件
```bash
# Redis configuration file example.
#
# Note that in order to read the configuration file, Redis must be
# started with the file path as first argument:
#
# ./redis-server /path/to/redis.conf

# Note on units: when memory size is needed, it is possible to specify
# it in the usual form of 1k 5GB 4M and so forth:


# ===== 单位 =====
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes


# ===== 对大小写不敏感 =====
# units are case insensitive so 1GB 1Gb 1gB are all the same.

################################## INCLUDES ###################################

# Include one or more other config files here.  This is useful if you
# have a standard template that goes to all Redis servers but also need
# to customize a few per-server settings.  Include files can include
# other files, so use this wisely.
#
# Notice option "include" won't be rewritten by command "CONFIG REWRITE"
# from admin or Redis Sentinel. Since Redis always uses the last processed
# line as value of a configuration directive, you'd better put includes
# at the beginning of this file to avoid overwriting config change at runtime.
#
# If instead you are interested in using includes to override configuration
# options, it is better to use include as the last line.
#

# ===== 包含其他配置文件 =====
# include /path/to/local.conf
# include /path/to/other.conf

################################## MODULES #####################################

# Load modules at startup. If the server is not able to load modules
# it will abort. It is possible to use multiple loadmodule directives.
#
# loadmodule /path/to/my_module.so
# loadmodule /path/to/other_module.so

################################## NETWORK 网络 #####################################

# By default, if no "bind" configuration directive is specified, Redis listens
# for connections from all the network interfaces available on the server.
# It is possible to listen to just one or multiple selected interfaces using
# the "bind" configuration directive, followed by one or more IP addresses.
#
# Examples:
#
# bind 192.168.1.100 10.0.0.1
# bind 127.0.0.1 ::1
#
# ~~~ WARNING ~~~ If the computer running Redis is directly exposed to the
# internet, binding to all the interfaces is dangerous and will expose the
# instance to everybody on the internet. So by default we uncomment the
# following bind directive, that will force Redis to listen only into
# the IPv4 loopback interface address (this means Redis will be able to
# accept connections only from clients running into the same computer it
# is running).
#
# IF YOU ARE SURE YOU WANT YOUR INSTANCE TO LISTEN TO ALL THE INTERFACES
# JUST COMMENT THE FOLLOWING LINE.
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# bind 127.0.0.1

# ===== 绑定的IP =====
bind *
# Protected mode is a layer of security protection, in order to avoid that
# Redis instances left open on the internet are accessed and exploited.
#
# When protected mode is on and if:
#
# 1) The server is not binding explicitly to a set of addresses using the
#    "bind" directive.
# 2) No password is configured.
#
# The server only accepts connections from clients connecting from the
# IPv4 and IPv6 loopback addresses 127.0.0.1 and ::1, and from Unix domain
# sockets.
#
# By default protected mode is enabled. You should disable it only if
# you are sure you want clients from other hosts to connect to Redis
# even if no authentication is configured, nor a specific set of interfaces
# are explicitly listed using the "bind" directive.

# ===== 保护设置 =====
protected-mode no

# Accept connections on the specified port, default is 6379 (IANA #815344).
# If port 0 is specified Redis will not listen on a TCP socket.

# ===== 绑定的端口 =====
port 6379

# TCP listen() backlog.
#
# In high requests-per-second environments you need an high backlog in order
# to avoid slow clients connections issues. Note that the Linux kernel
# will silently truncate it to the value of /proc/sys/net/core/somaxconn so
# make sure to raise both the value of somaxconn and tcp_max_syn_backlog
# in order to get the desired effect.
tcp-backlog 511

# Unix socket.
#
# Specify the path for the Unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
#
# unixsocket /tmp/redis.sock
# unixsocketperm 700

# Close the connection after a client is idle for N seconds (0 to disable)
timeout 0

# TCP keepalive.
#
# If non-zero, use SO_KEEPALIVE to send TCP ACKs to clients in absence
# of communication. This is useful for two reasons:
#
# 1) Detect dead peers.
# 2) Take the connection alive from the point of view of network
#    equipment in the middle.
#
# On Linux, the specified value (in seconds) is the period used to send ACKs.
# Note that to close the connection the double of the time is needed.
# On other kernels the period depends on the kernel configuration.
#
# A reasonable value for this option is 300 seconds, which is the new
# Redis default starting with Redis 3.2.1.
tcp-keepalive 300

################################# GENERAL 通用 #####################################

# By default Redis does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
# ===== 以守护后台进程进行 =====
daemonize yes

# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
#   supervised no      - no supervision interaction
#   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
#   supervised systemd - signal systemd by writing READY=1 to $NOTIFY_SOCKET
#   supervised auto    - detect upstart or systemd method based on
#                        UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
#       They do not enable continuous liveness pings back to your supervisor.
supervised no

# If a pid file is specified, Redis writes it where specified at startup
# and removes it at exit.
#
# When the server runs non daemonized, no pid file is created if none is
# specified in the configuration. When the server is daemonized, the pid file
# is used even if not specified, defaulting to "/var/run/redis.pid".
#
# Creating a pid file is best effort: if Redis is not able to create it
# nothing bad happens, the server will start and run normally.

# ===== 如果以后天方式运行，就需要指定一个pid 文件 =====
pidfile /var/run/redis_6379.pid

# ===== 日志 =====
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged) # 生产环境
loglevel notice

# Specify the log file name. Also the empty string can be used to force
# Redis to log on the standard output. Note that if you use standard
# output for logging but daemonize, logs will be sent to /dev/null

# ===== 日志的文件位置名 =====
logfile ""

# To enable logging to the system logger, just set 'syslog-enabled' to yes,
# and optionally update the other syslog parameters to suit your needs.
# syslog-enabled no

# Specify the syslog identity.
# syslog-ident redis

# Specify the syslog facility. Must be USER or between LOCAL0-LOCAL7.
# syslog-facility local0

# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1

# ===== 默认有16个数据库 =====
databases 16

# By default Redis shows an ASCII art logo only when started to log to the
# standard output and if the standard output is a TTY. Basically this means
# that normally a logo is displayed only in interactive sessions.
#
# However it is possible to force the pre-4.0 behavior and always show a
# ASCII art logo in startup logs by setting the following option to yes.

# ===== 是否使用logo =====
always-show-logo yes

################################ SNAPSHOTTING 快照 ################################
#
# Save the DB on disk:
#
#   save <seconds> <changes>
#
#   Will save the DB if both the given number of seconds and the given
#   number of write operations against the DB occurred.
#
#   In the example below the behaviour will be to save:
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
#
#   Note: you can disable saving completely by commenting out all "save" lines.
#
#   It is also possible to remove all the previously configured save
#   points by adding a save directive with a single empty string argument
#   like in the following example:
#
#   save ""

# ===== 快照，持久化，在规定的时间内，执行了多少次操作，就会持久化到文件 .rdb.aof =====
save 900 1 # 如果900内，如果至少 1 个key进行了修改，我们就执行持久化操作 
save 300 10 # 如果300内，如果至少 10 个key进行了修改，我们就执行持久化操作
save 60 10000 # 如果60内，如果至少10000个key进行了修改，我们就执行持久化操作

# By default Redis will stop accepting writes if RDB snapshots are enabled
# (at least one save point) and the latest background save failed.
# This will make the user aware (in a hard way) that data is not persisting
# on disk properly, otherwise chances are that no one will notice and some
# disaster will happen.
#
# If the background saving process will start working again Redis will
# automatically allow writes again.
#
# However if you have setup your proper monitoring of the Redis server
# and persistence, you may want to disable this feature so that Redis will
# continue to work as usual even if there are problems with disk,
# permissions, and so forth.

# ===== 持久化如果出错，是否继续工作 =====
stop-writes-on-bgsave-error yes

# Compress string objects using LZF when dump .rdb databases?
# For default that's set to 'yes' as it's almost always a win.
# If you want to save some CPU in the saving child set it to 'no' but
# the dataset will likely be bigger if you have compressible values or keys.

# ===== 是否压缩rdb 文件 =====
rdbcompression yes

# Since version 5 of RDB a CRC64 checksum is placed at the end of the file.
# This makes the format more resistant to corruption but there is a performance
# hit to pay (around 10%) when saving and loading RDB files, so you can disable it
# for maximum performances.
#
# RDB files created with checksum disabled have a checksum of zero that will
# tell the loading code to skip the check.

# ===== 保存rdb文件，进行错误的检查校验 =====
rdbchecksum yes

# The filename where to dump the DB
dbfilename dump.rdb

# The working directory.
#
# The DB will be written inside this directory, with the filename specified
# above using the 'dbfilename' configuration directive.
#
# The Append Only File will also be created inside this directory.
#
# Note that you must specify a directory here, not a file name.

# ===== 文件的保存目录 =====
dir ./

################################# REPLICATION 主从复制 #################################

# Master-Replica replication. Use replicaof to make a Redis instance a copy of
# another Redis server. A few things to understand ASAP about Redis replication.
#
#   +------------------+      +---------------+
#   |      Master      | ---> |    Replica    |
#   | (receive writes) |      |  (exact copy) |
#   +------------------+      +---------------+
#
# 1) Redis replication is asynchronous, but you can configure a master to
#    stop accepting writes if it appears to be not connected with at least
#    a given number of replicas.
# 2) Redis replicas are able to perform a partial resynchronization with the
#    master if the replication link is lost for a relatively small amount of
#    time. You may want to configure the replication backlog size (see the next
#    sections of this file) with a sensible value depending on your needs.
# 3) Replication is automatic and does not need user intervention. After a
#    network partition replicas automatically try to reconnect to masters
#    and resynchronize with them.
#
# replicaof <masterip> <masterport>

# If the master is password protected (using the "requirepass" configuration
# directive below) it is possible to tell the replica to authenticate before
# starting the replication synchronization process, otherwise the master will
# refuse the replica request.
#
# masterauth <master-password>

# When a replica loses its connection with the master, or when the replication
# is still in progress, the replica can act in two different ways:
#
# 1) if replica-serve-stale-data is set to 'yes' (the default) the replica will
#    still reply to client requests, possibly with out of date data, or the
#    data set may just be empty if this is the first synchronization.
#
# 2) if replica-serve-stale-data is set to 'no' the replica will reply with
#    an error "SYNC with master in progress" to all the kind of commands
#    but to INFO, replicaOF, AUTH, PING, SHUTDOWN, REPLCONF, ROLE, CONFIG,
#    SUBSCRIBE, UNSUBSCRIBE, PSUBSCRIBE, PUNSUBSCRIBE, PUBLISH, PUBSUB,
#    COMMAND, POST, HOST: and LATENCY.
#
replica-serve-stale-data yes

# You can configure a replica instance to accept writes or not. Writing against
# a replica instance may be useful to store some ephemeral data (because data
# written on a replica will be easily deleted after resync with the master) but
# may also cause problems if clients are writing to it because of a
# misconfiguration.
#
# Since Redis 2.6 by default replicas are read-only.
#
# Note: read only replicas are not designed to be exposed to untrusted clients
# on the internet. It's just a protection layer against misuse of the instance.
# Still a read only replica exports by default all the administrative commands
# such as CONFIG, DEBUG, and so forth. To a limited extent you can improve
# security of read only replicas using 'rename-command' to shadow all the
# administrative / dangerous commands.
replica-read-only yes

# Replication SYNC strategy: disk or socket.
#
# -------------------------------------------------------
# WARNING: DISKLESS REPLICATION IS EXPERIMENTAL CURRENTLY
# -------------------------------------------------------
#
# New replicas and reconnecting replicas that are not able to continue the replication
# process just receiving differences, need to do what is called a "full
# synchronization". An RDB file is transmitted from the master to the replicas.
# The transmission can happen in two different ways:
#
# 1) Disk-backed: The Redis master creates a new process that writes the RDB
#                 file on disk. Later the file is transferred by the parent
#                 process to the replicas incrementally.
# 2) Diskless: The Redis master creates a new process that directly writes the
#              RDB file to replica sockets, without touching the disk at all.
#
# With disk-backed replication, while the RDB file is generated, more replicas
# can be queued and served with the RDB file as soon as the current child producing
# the RDB file finishes its work. With diskless replication instead once
# the transfer starts, new replicas arriving will be queued and a new transfer
# will start when the current one terminates.
#
# When diskless replication is used, the master waits a configurable amount of
# time (in seconds) before starting the transfer in the hope that multiple replicas
# will arrive and the transfer can be parallelized.
#
# With slow disks and fast (large bandwidth) networks, diskless replication
# works better.
repl-diskless-sync no

# When diskless replication is enabled, it is possible to configure the delay
# the server waits in order to spawn the child that transfers the RDB via socket
# to the replicas.
#
# This is important since once the transfer starts, it is not possible to serve
# new replicas arriving, that will be queued for the next RDB transfer, so the server
# waits a delay in order to let more replicas arrive.
#
# The delay is specified in seconds, and by default is 5 seconds. To disable
# it entirely just set it to 0 seconds and the transfer will start ASAP.
repl-diskless-sync-delay 5

# Replicas send PINGs to server in a predefined interval. It's possible to change
# this interval with the repl_ping_replica_period option. The default value is 10
# seconds.
#
# repl-ping-replica-period 10

# The following option sets the replication timeout for:
#
# 1) Bulk transfer I/O during SYNC, from the point of view of replica.
# 2) Master timeout from the point of view of replicas (data, pings).
# 3) Replica timeout from the point of view of masters (REPLCONF ACK pings).
#
# It is important to make sure that this value is greater than the value
# specified for repl-ping-replica-period otherwise a timeout will be detected
# every time there is low traffic between the master and the replica.
#
# repl-timeout 60

# Disable TCP_NODELAY on the replica socket after SYNC?
#
# If you select "yes" Redis will use a smaller number of TCP packets and
# less bandwidth to send data to replicas. But this can add a delay for
# the data to appear on the replica side, up to 40 milliseconds with
# Linux kernels using a default configuration.
#
# If you select "no" the delay for data to appear on the replica side will
# be reduced but more bandwidth will be used for replication.
#
# By default we optimize for low latency, but in very high traffic conditions
# or when the master and replicas are many hops away, turning this to "yes" may
# be a good idea.
repl-disable-tcp-nodelay no

# Set the replication backlog size. The backlog is a buffer that accumulates
# replica data when replicas are disconnected for some time, so that when a replica
# wants to reconnect again, often a full resync is not needed, but a partial
# resync is enough, just passing the portion of data the replica missed while
# disconnected.
#
# The bigger the replication backlog, the longer the time the replica can be
# disconnected and later be able to perform a partial resynchronization.
#
# The backlog is only allocated once there is at least a replica connected.
#
# repl-backlog-size 1mb

# After a master has no longer connected replicas for some time, the backlog
# will be freed. The following option configures the amount of seconds that
# need to elapse, starting from the time the last replica disconnected, for
# the backlog buffer to be freed.
#
# Note that replicas never free the backlog for timeout, since they may be
# promoted to masters later, and should be able to correctly "partially
# resynchronize" with the replicas: hence they should always accumulate backlog.
#
# A value of 0 means to never release the backlog.
#
# repl-backlog-ttl 3600

# The replica priority is an integer number published by Redis in the INFO output.
# It is used by Redis Sentinel in order to select a replica to promote into a
# master if the master is no longer working correctly.
#
# A replica with a low priority number is considered better for promotion, so
# for instance if there are three replicas with priority 10, 100, 25 Sentinel will
# pick the one with priority 10, that is the lowest.
#
# However a special priority of 0 marks the replica as not able to perform the
# role of master, so a replica with priority of 0 will never be selected by
# Redis Sentinel for promotion.
#
# By default the priority is 100.
replica-priority 100

# It is possible for a master to stop accepting writes if there are less than
# N replicas connected, having a lag less or equal than M seconds.
#
# The N replicas need to be in "online" state.
#
# The lag in seconds, that must be <= the specified value, is calculated from
# the last ping received from the replica, that is usually sent every second.
#
# This option does not GUARANTEE that N replicas will accept the write, but
# will limit the window of exposure for lost writes in case not enough replicas
# are available, to the specified number of seconds.
#
# For example to require at least 3 replicas with a lag <= 10 seconds use:
#
# min-replicas-to-write 3
# min-replicas-max-lag 10
#
# Setting one or the other to 0 disables the feature.
#
# By default min-replicas-to-write is set to 0 (feature disabled) and
# min-replicas-max-lag is set to 10.

# A Redis master is able to list the address and port of the attached
# replicas in different ways. For example the "INFO replication" section
# offers this information, which is used, among other tools, by
# Redis Sentinel in order to discover replica instances.
# Another place where this info is available is in the output of the
# "ROLE" command of a master.
#
# The listed IP and address normally reported by a replica is obtained
# in the following way:
#
#   IP: The address is auto detected by checking the peer address
#   of the socket used by the replica to connect with the master.
#
#   Port: The port is communicated by the replica during the replication
#   handshake, and is normally the port that the replica is using to
#   listen for connections.
#
# However when port forwarding or Network Address Translation (NAT) is
# used, the replica may be actually reachable via different IP and port
# pairs. The following two options can be used by a replica in order to
# report to its master a specific set of IP and port, so that both INFO
# and ROLE will report those values.
#
# There is no need to use both the options if you need to override just
# the port or the IP address.
#
# replica-announce-ip 5.5.5.5
# replica-announce-port 1234

################################## SECURITY 安全 ###################################

# Require clients to issue AUTH <PASSWORD> before processing any other
# commands.  This might be useful in environments in which you do not trust
# others with access to the host running redis-server.
#
# This should stay commented out for backward compatibility and because most
# people do not need auth (e.g. they run their own servers).
#
# Warning: since Redis is pretty fast an outside user can try up to
# 150k passwords per second against a good box. This means that you should
# use a very strong password otherwise it will be very easy to break.
#

# ===== 设置登陆密码，默认没有密码 =====
# requirepass foobared

# Command renaming.
#
# It is possible to change the name of dangerous commands in a shared
# environment. For instance the CONFIG command may be renamed into something
# hard to guess so that it will still be available for internal-use tools
# but not available for general clients.
#
# Example:
#
# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52
#
# It is also possible to completely kill a command by renaming it into
# an empty string:
#
# rename-command CONFIG ""
#
# Please note that changing the name of commands that are logged into the
# AOF file or transmitted to replicas may cause problems.

################################### CLIENTS 客户端限制 ####################################

# Set the max number of connected clients at the same time. By default
# this limit is set to 10000 clients, however if the Redis server is not
# able to configure the process file limit to allow for the specified limit
# the max number of allowed clients is set to the current file limit
# minus 32 (as Redis reserves a few file descriptors for internal uses).
#
# Once the limit is reached Redis will close all the new connections sending
# an error 'max number of clients reached'.
#

# ===== 最大客户端的数量 =====
# maxclients 10000

############################## MEMORY MANAGEMENT ################################

# Set a memory usage limit to the specified amount of bytes.
# When the memory limit is reached Redis will try to remove keys
# according to the eviction policy selected (see maxmemory-policy).
#
# If Redis can't remove keys according to the policy, or if the policy is
# set to 'noeviction', Redis will start to reply with errors to commands
# that would use more memory, like SET, LPUSH, and so on, and will continue
# to reply to read-only commands like GET.
#
# This option is usually useful when using Redis as an LRU or LFU cache, or to
# set a hard memory limit for an instance (using the 'noeviction' policy).
#
# WARNING: If you have replicas attached to an instance with maxmemory on,
# the size of the output buffers needed to feed the replicas are subtracted
# from the used memory count, so that network problems / resyncs will
# not trigger a loop where keys are evicted, and in turn the output
# buffer of replicas is full with DELs of keys evicted triggering the deletion
# of more keys, and so forth until the database is completely emptied.
#
# In short... if you have replicas attached it is suggested that you set a lower
# limit for maxmemory so that there is some free RAM on the system for replica
# output buffers (but this is not needed if the policy is 'noeviction').
#

# ===== 最大的内存容量 =====
# maxmemory <bytes>

# MAXMEMORY POLICY: how Redis will select what to remove when maxmemory
# is reached. You can select among five behaviors:
#
# volatile-lru -> Evict using approximated LRU among the keys with an expire set.
# allkeys-lru -> Evict any key using approximated LRU.
# volatile-lfu -> Evict using approximated LFU among the keys with an expire set.
# allkeys-lfu -> Evict any key using approximated LFU.
# volatile-random -> Remove a random key among the ones with an expire set.
# allkeys-random -> Remove a random key, any key.
# volatile-ttl -> Remove the key with the nearest expire time (minor TTL)
# noeviction -> Don't evict anything, just return an error on write operations.
#
# LRU means Least Recently Used
# LFU means Least Frequently Used
#
# Both LRU, LFU and volatile-ttl are implemented using approximated
# randomized algorithms.
#
# Note: with any of the above policies, Redis will return an error on write
#       operations, when there are no suitable keys for eviction.
#
#       At the date of writing these commands are: set setnx setex append
#       incr decr rpush lpush rpushx lpushx linsert lset rpoplpush sadd
#       sinter sinterstore sunion sunionstore sdiff sdiffstore zadd zincrby
#       zunionstore zinterstore hset hsetnx hmset hincrby incrby decrby
#       getset mset msetnx exec sort
#
# The default is:
#
# ===== 内存到达上限的处理策略 =====
# maxmemory-policy noeviction

# LRU, LFU and minimal TTL algorithms are not precise algorithms but approximated
# algorithms (in order to save memory), so you can tune it for speed or
# accuracy. For default Redis will check five keys and pick the one that was
# used less recently, you can change the sample size using the following
# configuration directive.
#
# The default of 5 produces good enough results. 10 Approximates very closely
# true LRU but costs more CPU. 3 is faster but not very accurate.
#
# maxmemory-samples 5

# Starting from Redis 5, by default a replica will ignore its maxmemory setting
# (unless it is promoted to master after a failover or manually). It means
# that the eviction of keys will be just handled by the master, sending the
# DEL commands to the replica as keys evict in the master side.
#
# This behavior ensures that masters and replicas stay consistent, and is usually
# what you want, however if your replica is writable, or you want the replica to have
# a different memory setting, and you are sure all the writes performed to the
# replica are idempotent, then you may change this default (but be sure to understand
# what you are doing).
#
# Note that since the replica by default does not evict, it may end using more
# memory than the one set via maxmemory (there are certain buffers that may
# be larger on the replica, or data structures may sometimes take more memory and so
# forth). So make sure you monitor your replicas and make sure they have enough
# memory to never hit a real out-of-memory condition before the master hits
# the configured maxmemory setting.
#
# replica-ignore-maxmemory yes

############################# LAZY FREEING ####################################

# Redis has two primitives to delete keys. One is called DEL and is a blocking
# deletion of the object. It means that the server stops processing new commands
# in order to reclaim all the memory associated with an object in a synchronous
# way. If the key deleted is associated with a small object, the time needed
# in order to execute the DEL command is very small and comparable to most other
# O(1) or O(log_N) commands in Redis. However if the key is associated with an
# aggregated value containing millions of elements, the server can block for
# a long time (even seconds) in order to complete the operation.
#
# For the above reasons Redis also offers non blocking deletion primitives
# such as UNLINK (non blocking DEL) and the ASYNC option of FLUSHALL and
# FLUSHDB commands, in order to reclaim memory in background. Those commands
# are executed in constant time. Another thread will incrementally free the
# object in the background as fast as possible.
#
# DEL, UNLINK and ASYNC option of FLUSHALL and FLUSHDB are user-controlled.
# It's up to the design of the application to understand when it is a good
# idea to use one or the other. However the Redis server sometimes has to
# delete keys or flush the whole database as a side effect of other operations.
# Specifically Redis deletes objects independently of a user call in the
# following scenarios:
#
# 1) On eviction, because of the maxmemory and maxmemory policy configurations,
#    in order to make room for new data, without going over the specified
#    memory limit.
# 2) Because of expire: when a key with an associated time to live (see the
#    EXPIRE command) must be deleted from memory.
# 3) Because of a side effect of a command that stores data on a key that may
#    already exist. For example the RENAME command may delete the old key
#    content when it is replaced with another one. Similarly SUNIONSTORE
#    or SORT with STORE option may delete existing keys. The SET command
#    itself removes any old content of the specified key in order to replace
#    it with the specified string.
# 4) During replication, when a replica performs a full resynchronization with
#    its master, the content of the whole database is removed in order to
#    load the RDB file just transferred.
#
# In all the above cases the default is to delete objects in a blocking way,
# like if DEL was called. However you can configure each case specifically
# in order to instead release memory in a non-blocking way like if UNLINK
# was called, using the following configuration directives:

lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no

############################## APPEND ONLY MODE 持久化aof配置 ###############################

# By default Redis asynchronously dumps the dataset on disk. This mode is
# good enough in many applications, but an issue with the Redis process or
# a power outage may result into a few minutes of writes lost (depending on
# the configured save points).
#
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Please check http://redis.io/topics/persistence for more information.

# ===== 默认不开启rof =====
appendonly no

# The name of the append only file (default: "appendonly.aof")

# ===== 持久化文件的名字 =====
appendfilename "appendonly.aof"

# The fsync() call tells the Operating System to actually write data on disk
# instead of waiting for more data in the output buffer. Some OS will really flush
# data on disk, some other OS will just try to do it ASAP.
#
# Redis supports three different modes:
#
# no: don't fsync, just let the OS flush the data when it wants. Faster.
# always: fsync after every write to the append only log. Slow, Safest.
# everysec: fsync only one time every second. Compromise.
#
# The default is "everysec", as that's usually the right compromise between
# speed and data safety. It's up to you to understand if you can relax this to
# "no" that will let the operating system flush the output buffer when
# it wants, for better performances (but if you can live with the idea of
# some data loss consider the default persistence mode that's snapshotting),
# or on the contrary, use "always" that's very slow but a bit safer than
# everysec.
#
# More details please check the following article:
# http://antirez.com/post/redis-persistence-demystified.html
#
# If unsure, use "everysec".

# ===== 执行sync =====
# appendfsync always # 每次修改都会sync 消耗性能
appendfsync everysec # 每秒执行一次sync,可能会丢失1s的数据
# appendfsync no # 不执行

# When the AOF fsync policy is set to always or everysec, and a background
# saving process (a background save or AOF log background rewriting) is
# performing a lot of I/O against the disk, in some Linux configurations
# Redis may block too long on the fsync() call. Note that there is no fix for
# this currently, as even performing fsync in a different thread will block
# our synchronous write(2) call.
#
# In order to mitigate this problem it's possible to use the following option
# that will prevent fsync() from being called in the main process while a
# BGSAVE or BGREWRITEAOF is in progress.
#
# This means that while another child is saving, the durability of Redis is
# the same as "appendfsync none". In practical terms, this means that it is
# possible to lose up to 30 seconds of log in the worst scenario (with the
# default Linux settings).
#
# If you have latency problems turn this to "yes". Otherwise leave it as
# "no" that is the safest pick from the point of view of durability.

no-appendfsync-on-rewrite no

# Automatic rewrite of the append only file.
# Redis is able to automatically rewrite the log file implicitly calling
# BGREWRITEAOF when the AOF log size grows by the specified percentage.
#
# This is how it works: Redis remembers the size of the AOF file after the
# latest rewrite (if no rewrite has happened since the restart, the size of
# the AOF at startup is used).
#
# This base size is compared to the current size. If the current size is
# bigger than the specified percentage, the rewrite is triggered. Also
# you need to specify a minimal size for the AOF file to be rewritten, this
# is useful to avoid rewriting the AOF file even if the percentage increase
# is reached but it is still pretty small.
#
# Specify a percentage of zero in order to disable the automatic AOF
# rewrite feature.

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# An AOF file may be found to be truncated at the end during the Redis
# startup process, when the AOF data gets loaded back into memory.
# This may happen when the system where Redis is running
# crashes, especially when an ext4 filesystem is mounted without the
# data=ordered option (however this can't happen when Redis itself
# crashes or aborts but the operating system still works correctly).
#
# Redis can either exit with an error when this happens, or load as much
# data as possible (the default now) and start if the AOF file is found
# to be truncated at the end. The following option controls this behavior.
#
# If aof-load-truncated is set to yes, a truncated AOF file is loaded and
# the Redis server starts emitting a log to inform the user of the event.
# Otherwise if the option is set to no, the server aborts with an error
# and refuses to start. When the option is set to no, the user requires
# to fix the AOF file using the "redis-check-aof" utility before to restart
# the server.
#
# Note that if the AOF file will be found to be corrupted in the middle
# the server will still exit with an error. This option only applies when
# Redis will try to read more data from the AOF file but not enough bytes
# will be found.
aof-load-truncated yes

# When rewriting the AOF file, Redis is able to use an RDB preamble in the
# AOF file for faster rewrites and recoveries. When this option is turned
# on the rewritten AOF file is composed of two different stanzas:
#
#   [RDB file][AOF tail]
#
# When loading Redis recognizes that the AOF file starts with the "REDIS"
# string and loads the prefixed RDB file, and continues loading the AOF
# tail.
aof-use-rdb-preamble yes

################################ LUA SCRIPTING  ###############################

# Max execution time of a Lua script in milliseconds.
#
# If the maximum execution time is reached Redis will log that a script is
# still in execution after the maximum allowed time and will start to
# reply to queries with an error.
#
# When a long running script exceeds the maximum execution time only the
# SCRIPT KILL and SHUTDOWN NOSAVE commands are available. The first can be
# used to stop a script that did not yet called write commands. The second
# is the only way to shut down the server in the case a write command was
# already issued by the script but the user doesn't want to wait for the natural
# termination of the script.
#
# Set it to 0 or a negative value for unlimited execution without warnings.
lua-time-limit 5000

################################ REDIS CLUSTER  ###############################

# Normal Redis instances can't be part of a Redis Cluster; only nodes that are
# started as cluster nodes can. In order to start a Redis instance as a
# cluster node enable the cluster support uncommenting the following:
#
# cluster-enabled yes

# Every cluster node has a cluster configuration file. This file is not
# intended to be edited by hand. It is created and updated by Redis nodes.
# Every Redis Cluster node requires a different cluster configuration file.
# Make sure that instances running in the same system do not have
# overlapping cluster configuration file names.
#
# cluster-config-file nodes-6379.conf

# Cluster node timeout is the amount of milliseconds a node must be unreachable
# for it to be considered in failure state.
# Most other internal time limits are multiple of the node timeout.
#
# cluster-node-timeout 15000

# A replica of a failing master will avoid to start a failover if its data
# looks too old.
#
# There is no simple way for a replica to actually have an exact measure of
# its "data age", so the following two checks are performed:
#
# 1) If there are multiple replicas able to failover, they exchange messages
#    in order to try to give an advantage to the replica with the best
#    replication offset (more data from the master processed).
#    Replicas will try to get their rank by offset, and apply to the start
#    of the failover a delay proportional to their rank.
#
# 2) Every single replica computes the time of the last interaction with
#    its master. This can be the last ping or command received (if the master
#    is still in the "connected" state), or the time that elapsed since the
#    disconnection with the master (if the replication link is currently down).
#    If the last interaction is too old, the replica will not try to failover
#    at all.
#
# The point "2" can be tuned by user. Specifically a replica will not perform
# the failover if, since the last interaction with the master, the time
# elapsed is greater than:
#
#   (node-timeout * replica-validity-factor) + repl-ping-replica-period
#
# So for example if node-timeout is 30 seconds, and the replica-validity-factor
# is 10, and assuming a default repl-ping-replica-period of 10 seconds, the
# replica will not try to failover if it was not able to talk with the master
# for longer than 310 seconds.
#
# A large replica-validity-factor may allow replicas with too old data to failover
# a master, while a too small value may prevent the cluster from being able to
# elect a replica at all.
#
# For maximum availability, it is possible to set the replica-validity-factor
# to a value of 0, which means, that replicas will always try to failover the
# master regardless of the last time they interacted with the master.
# (However they'll always try to apply a delay proportional to their
# offset rank).
#
# Zero is the only value able to guarantee that when all the partitions heal
# the cluster will always be able to continue.
#
# cluster-replica-validity-factor 10

# Cluster replicas are able to migrate to orphaned masters, that are masters
# that are left without working replicas. This improves the cluster ability
# to resist to failures as otherwise an orphaned master can't be failed over
# in case of failure if it has no working replicas.
#
# Replicas migrate to orphaned masters only if there are still at least a
# given number of other working replicas for their old master. This number
# is the "migration barrier". A migration barrier of 1 means that a replica
# will migrate only if there is at least 1 other working replica for its master
# and so forth. It usually reflects the number of replicas you want for every
# master in your cluster.
#
# Default is 1 (replicas migrate only if their masters remain with at least
# one replica). To disable migration just set it to a very large value.
# A value of 0 can be set but is useful only for debugging and dangerous
# in production.
#
# cluster-migration-barrier 1

# By default Redis Cluster nodes stop accepting queries if they detect there
# is at least an hash slot uncovered (no available node is serving it).
# This way if the cluster is partially down (for example a range of hash slots
# are no longer covered) all the cluster becomes, eventually, unavailable.
# It automatically returns available as soon as all the slots are covered again.
#
# However sometimes you want the subset of the cluster which is working,
# to continue to accept queries for the part of the key space that is still
# covered. In order to do so, just set the cluster-require-full-coverage
# option to no.
#
# cluster-require-full-coverage yes

# This option, when set to yes, prevents replicas from trying to failover its
# master during master failures. However the master can still perform a
# manual failover, if forced to do so.
#
# This is useful in different scenarios, especially in the case of multiple
# data center operations, where we want one side to never be promoted if not
# in the case of a total DC failure.
#
# cluster-replica-no-failover no

# In order to setup your cluster make sure to read the documentation
# available at http://redis.io web site.

########################## CLUSTER DOCKER/NAT support  ########################

# In certain deployments, Redis Cluster nodes address discovery fails, because
# addresses are NAT-ted or because ports are forwarded (the typical case is
# Docker and other containers).
#
# In order to make Redis Cluster working in such environments, a static
# configuration where each node knows its public address is needed. The
# following two options are used for this scope, and are:
#
# * cluster-announce-ip
# * cluster-announce-port
# * cluster-announce-bus-port
#
# Each instruct the node about its address, client port, and cluster message
# bus port. The information is then published in the header of the bus packets
# so that other nodes will be able to correctly map the address of the node
# publishing the information.
#
# If the above options are not used, the normal Redis Cluster auto-detection
# will be used instead.
#
# Note that when remapped, the bus port may not be at the fixed offset of
# clients port + 10000, so you can specify any port and bus-port depending
# on how they get remapped. If the bus-port is not set, a fixed offset of
# 10000 will be used as usually.
#
# Example:
#
# cluster-announce-ip 10.1.1.5
# cluster-announce-port 6379
# cluster-announce-bus-port 6380

################################## SLOW LOG ###################################

# The Redis Slow Log is a system to log queries that exceeded a specified
# execution time. The execution time does not include the I/O operations
# like talking with the client, sending the reply and so forth,
# but just the time needed to actually execute the command (this is the only
# stage of command execution where the thread is blocked and can not serve
# other requests in the meantime).
#
# You can configure the slow log with two parameters: one tells Redis
# what is the execution time, in microseconds, to exceed in order for the
# command to get logged, and the other parameter is the length of the
# slow log. When a new command is logged the oldest one is removed from the
# queue of logged commands.

# The following time is expressed in microseconds, so 1000000 is equivalent
# to one second. Note that a negative number disables the slow log, while
# a value of zero forces the logging of every command.
slowlog-log-slower-than 10000

# There is no limit to this length. Just be aware that it will consume memory.
# You can reclaim memory used by the slow log with SLOWLOG RESET.
slowlog-max-len 128

################################ LATENCY MONITOR ##############################

# The Redis latency monitoring subsystem samples different operations
# at runtime in order to collect data related to possible sources of
# latency of a Redis instance.
#
# Via the LATENCY command this information is available to the user that can
# print graphs and obtain reports.
#
# The system only logs operations that were performed in a time equal or
# greater than the amount of milliseconds specified via the
# latency-monitor-threshold configuration directive. When its value is set
# to zero, the latency monitor is turned off.
#
# By default latency monitoring is disabled since it is mostly not needed
# if you don't have latency issues, and collecting data has a performance
# impact, that while very small, can be measured under big load. Latency
# monitoring can easily be enabled at runtime using the command
# "CONFIG SET latency-monitor-threshold <milliseconds>" if needed.
latency-monitor-threshold 0

############################# EVENT NOTIFICATION ##############################

# Redis can notify Pub/Sub clients about events happening in the key space.
# This feature is documented at http://redis.io/topics/notifications
#
# For instance if keyspace events notification is enabled, and a client
# performs a DEL operation on key "foo" stored in the Database 0, two
# messages will be published via Pub/Sub:
#
# PUBLISH __keyspace@0__:foo del
# PUBLISH __keyevent@0__:del foo
#
# It is possible to select the events that Redis will notify among a set
# of classes. Every class is identified by a single character:
#
#  K     Keyspace events, published with __keyspace@<db>__ prefix.
#  E     Keyevent events, published with __keyevent@<db>__ prefix.
#  g     Generic commands (non-type specific) like DEL, EXPIRE, RENAME, ...
#  $     String commands
#  l     List commands
#  s     Set commands
#  h     Hash commands
#  z     Sorted set commands
#  x     Expired events (events generated every time a key expires)
#  e     Evicted events (events generated when a key is evicted for maxmemory)
#  A     Alias for g$lshzxe, so that the "AKE" string means all the events.
#
#  The "notify-keyspace-events" takes as argument a string that is composed
#  of zero or multiple characters. The empty string means that notifications
#  are disabled.
#
#  Example: to enable list and generic events, from the point of view of the
#           event name, use:
#
#  notify-keyspace-events Elg
#
#  Example 2: to get the stream of the expired keys subscribing to channel
#             name __keyevent@0__:expired use:
#
#  notify-keyspace-events Ex
#
#  By default all notifications are disabled because most users don't need
#  this feature and the feature has some overhead. Note that if you don't
#  specify at least one of K or E, no events will be delivered.
notify-keyspace-events ""

############################### ADVANCED CONFIG ###############################

# Hashes are encoded using a memory efficient data structure when they have a
# small number of entries, and the biggest entry does not exceed a given
# threshold. These thresholds can be configured using the following directives.
hash-max-ziplist-entries 512
hash-max-ziplist-value 64

# Lists are also encoded in a special way to save a lot of space.
# The number of entries allowed per internal list node can be specified
# as a fixed maximum size or a maximum number of elements.
# For a fixed maximum size, use -5 through -1, meaning:
# -5: max size: 64 Kb  <-- not recommended for normal workloads
# -4: max size: 32 Kb  <-- not recommended
# -3: max size: 16 Kb  <-- probably not recommended
# -2: max size: 8 Kb   <-- good
# -1: max size: 4 Kb   <-- good
# Positive numbers mean store up to _exactly_ that number of elements
# per list node.
# The highest performing option is usually -2 (8 Kb size) or -1 (4 Kb size),
# but if your use case is unique, adjust the settings as necessary.
list-max-ziplist-size -2

# Lists may also be compressed.
# Compress depth is the number of quicklist ziplist nodes from *each* side of
# the list to *exclude* from compression.  The head and tail of the list
# are always uncompressed for fast push/pop operations.  Settings are:
# 0: disable all list compression
# 1: depth 1 means "don't start compressing until after 1 node into the list,
#    going from either the head or tail"
#    So: [head]->node->node->...->node->[tail]
#    [head], [tail] will always be uncompressed; inner nodes will compress.
# 2: [head]->[next]->node->node->...->node->[prev]->[tail]
#    2 here means: don't compress head or head->next or tail->prev or tail,
#    but compress all nodes between them.
# 3: [head]->[next]->[next]->node->node->...->node->[prev]->[prev]->[tail]
# etc.
list-compress-depth 0

# Sets have a special encoding in just one case: when a set is composed
# of just strings that happen to be integers in radix 10 in the range
# of 64 bit signed integers.
# The following configuration setting sets the limit in the size of the
# set in order to use this special memory saving encoding.
set-max-intset-entries 512

# Similarly to hashes and lists, sorted sets are also specially encoded in
# order to save a lot of space. This encoding is only used when the length and
# elements of a sorted set are below the following limits:
zset-max-ziplist-entries 128
zset-max-ziplist-value 64

# HyperLogLog sparse representation bytes limit. The limit includes the
# 16 bytes header. When an HyperLogLog using the sparse representation crosses
# this limit, it is converted into the dense representation.
#
# A value greater than 16000 is totally useless, since at that point the
# dense representation is more memory efficient.
#
# The suggested value is ~ 3000 in order to have the benefits of
# the space efficient encoding without slowing down too much PFADD,
# which is O(N) with the sparse encoding. The value can be raised to
# ~ 10000 when CPU is not a concern, but space is, and the data set is
# composed of many HyperLogLogs with cardinality in the 0 - 15000 range.
hll-sparse-max-bytes 3000

# Streams macro node max size / items. The stream data structure is a radix
# tree of big nodes that encode multiple items inside. Using this configuration
# it is possible to configure how big a single node can be in bytes, and the
# maximum number of items it may contain before switching to a new node when
# appending new stream entries. If any of the following settings are set to
# zero, the limit is ignored, so for instance it is possible to set just a
# max entires limit by setting max-bytes to 0 and max-entries to the desired
# value.
stream-node-max-bytes 4096
stream-node-max-entries 100

# Active rehashing uses 1 millisecond every 100 milliseconds of CPU time in
# order to help rehashing the main Redis hash table (the one mapping top-level
# keys to values). The hash table implementation Redis uses (see dict.c)
# performs a lazy rehashing: the more operation you run into a hash table
# that is rehashing, the more rehashing "steps" are performed, so if the
# server is idle the rehashing is never complete and some more memory is used
# by the hash table.
#
# The default is to use this millisecond 10 times every second in order to
# actively rehash the main dictionaries, freeing memory when possible.
#
# If unsure:
# use "activerehashing no" if you have hard latency requirements and it is
# not a good thing in your environment that Redis can reply from time to time
# to queries with 2 milliseconds delay.
#
# use "activerehashing yes" if you don't have such hard requirements but
# want to free memory asap when possible.
activerehashing yes

# The client output buffer limits can be used to force disconnection of clients
# that are not reading data from the server fast enough for some reason (a
# common reason is that a Pub/Sub client can't consume messages as fast as the
# publisher can produce them).
#
# The limit can be set differently for the three different classes of clients:
#
# normal -> normal clients including MONITOR clients
# replica  -> replica clients
# pubsub -> clients subscribed to at least one pubsub channel or pattern
#
# The syntax of every client-output-buffer-limit directive is the following:
#
# client-output-buffer-limit <class> <hard limit> <soft limit> <soft seconds>
#
# A client is immediately disconnected once the hard limit is reached, or if
# the soft limit is reached and remains reached for the specified number of
# seconds (continuously).
# So for instance if the hard limit is 32 megabytes and the soft limit is
# 16 megabytes / 10 seconds, the client will get disconnected immediately
# if the size of the output buffers reach 32 megabytes, but will also get
# disconnected if the client reaches 16 megabytes and continuously overcomes
# the limit for 10 seconds.
#
# By default normal clients are not limited because they don't receive data
# without asking (in a push way), but just after a request, so only
# asynchronous clients may create a scenario where data is requested faster
# than it can read.
#
# Instead there is a default limit for pubsub and replica clients, since
# subscribers and replicas receive data in a push fashion.
#
# Both the hard or the soft limit can be disabled by setting them to zero.
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60

# Client query buffers accumulate new commands. They are limited to a fixed
# amount by default in order to avoid that a protocol desynchronization (for
# instance due to a bug in the client) will lead to unbound memory usage in
# the query buffer. However you can configure it here if you have very special
# needs, such us huge multi/exec requests or alike.
#
# client-query-buffer-limit 1gb

# In the Redis protocol, bulk requests, that are, elements representing single
# strings, are normally limited ot 512 mb. However you can change this limit
# here.
#
# proto-max-bulk-len 512mb

# Redis calls an internal function to perform many background tasks, like
# closing connections of clients in timeout, purging expired keys that are
# never requested, and so forth.
#
# Not all tasks are performed with the same frequency, but Redis checks for
# tasks to perform according to the specified "hz" value.
#
# By default "hz" is set to 10. Raising the value will use more CPU when
# Redis is idle, but at the same time will make Redis more responsive when
# there are many keys expiring at the same time, and timeouts may be
# handled with more precision.
#
# The range is between 1 and 500, however a value over 100 is usually not
# a good idea. Most users should use the default of 10 and raise this up to
# 100 only in environments where very low latency is required.
hz 10

# Normally it is useful to have an HZ value which is proportional to the
# number of clients connected. This is useful in order, for instance, to
# avoid too many clients are processed for each background task invocation
# in order to avoid latency spikes.
#
# Since the default HZ value by default is conservatively set to 10, Redis
# offers, and enables by default, the ability to use an adaptive HZ value
# which will temporary raise when there are many connected clients.
#
# When dynamic HZ is enabled, the actual configured HZ will be used as
# as a baseline, but multiples of the configured HZ value will be actually
# used as needed once more clients are connected. In this way an idle
# instance will use very little CPU time while a busy instance will be
# more responsive.
dynamic-hz yes

# When a child rewrites the AOF file, if the following option is enabled
# the file will be fsync-ed every 32 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
aof-rewrite-incremental-fsync yes

# When redis saves RDB file, if the following option is enabled
# the file will be fsync-ed every 32 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
rdb-save-incremental-fsync yes

# Redis LFU eviction (see maxmemory setting) can be tuned. However it is a good
# idea to start with the default settings and only change them after investigating
# how to improve the performances and how the keys LFU change over time, which
# is possible to inspect via the OBJECT FREQ command.
#
# There are two tunable parameters in the Redis LFU implementation: the
# counter logarithm factor and the counter decay time. It is important to
# understand what the two parameters mean before changing them.
#
# The LFU counter is just 8 bits per key, it's maximum value is 255, so Redis
# uses a probabilistic increment with logarithmic behavior. Given the value
# of the old counter, when a key is accessed, the counter is incremented in
# this way:
#
# 1. A random number R between 0 and 1 is extracted.
# 2. A probability P is calculated as 1/(old_value*lfu_log_factor+1).
# 3. The counter is incremented only if R < P.
#
# The default lfu-log-factor is 10. This is a table of how the frequency
# counter changes with a different number of accesses with different
# logarithmic factors:
#
# +--------+------------+------------+------------+------------+------------+
# | factor | 100 hits   | 1000 hits  | 100K hits  | 1M hits    | 10M hits   |
# +--------+------------+------------+------------+------------+------------+
# | 0      | 104        | 255        | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 1      | 18         | 49         | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 10     | 10         | 18         | 142        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 100    | 8          | 11         | 49         | 143        | 255        |
# +--------+------------+------------+------------+------------+------------+
#
# NOTE: The above table was obtained by running the following commands:
#
#   redis-benchmark -n 1000000 incr foo
#   redis-cli object freq foo
#
# NOTE 2: The counter initial value is 5 in order to give new objects a chance
# to accumulate hits.
#
# The counter decay time is the time, in minutes, that must elapse in order
# for the key counter to be divided by two (or decremented if it has a value
# less <= 10).
#
# The default value for the lfu-decay-time is 1. A Special value of 0 means to
# decay the counter every time it happens to be scanned.
#
# lfu-log-factor 10
# lfu-decay-time 1

########################### ACTIVE DEFRAGMENTATION #######################
#
# WARNING THIS FEATURE IS EXPERIMENTAL. However it was stress tested
# even in production and manually tested by multiple engineers for some
# time.
#
# What is active defragmentation?
# -------------------------------
#
# Active (online) defragmentation allows a Redis server to compact the
# spaces left between small allocations and deallocations of data in memory,
# thus allowing to reclaim back memory.
#
# Fragmentation is a natural process that happens with every allocator (but
# less so with Jemalloc, fortunately) and certain workloads. Normally a server
# restart is needed in order to lower the fragmentation, or at least to flush
# away all the data and create it again. However thanks to this feature
# implemented by Oran Agra for Redis 4.0 this process can happen at runtime
# in an "hot" way, while the server is running.
#
# Basically when the fragmentation is over a certain level (see the
# configuration options below) Redis will start to create new copies of the
# values in contiguous memory regions by exploiting certain specific Jemalloc
# features (in order to understand if an allocation is causing fragmentation
# and to allocate it in a better place), and at the same time, will release the
# old copies of the data. This process, repeated incrementally for all the keys
# will cause the fragmentation to drop back to normal values.
#
# Important things to understand:
#
# 1. This feature is disabled by default, and only works if you compiled Redis
#    to use the copy of Jemalloc we ship with the source code of Redis.
#    This is the default with Linux builds.
#
# 2. You never need to enable this feature if you don't have fragmentation
#    issues.
#
# 3. Once you experience fragmentation, you can enable this feature when
#    needed with the command "CONFIG SET activedefrag yes".
#
# The configuration parameters are able to fine tune the behavior of the
# defragmentation process. If you are not sure about what they mean it is
# a good idea to leave the defaults untouched.

# Enabled active defragmentation
# activedefrag yes

# Minimum amount of fragmentation waste to start active defrag
# active-defrag-ignore-bytes 100mb

# Minimum percentage of fragmentation to start active defrag
# active-defrag-threshold-lower 10

# Maximum percentage of fragmentation at which we use maximum effort
# active-defrag-threshold-upper 100

# Minimal effort for defrag in CPU percentage
# active-defrag-cycle-min 5

# Maximal effort for defrag in CPU percentage
# active-defrag-cycle-max 75

# Maximum number of set/hash/zset/list fields that will be processed from
# the main dictionary scan
# active-defrag-max-scan-fields 1000
```
[点击下载 redis.conf](http://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/download/Redis/redis.conf)

# 10 Redis 持久化
## 10.1 RDF 
### 10.1.1 特点
- RDB 将当前的`进程数据`生成`快照`保存到硬盘中。恢复的时候将`快照文件`直接读到内存中。
- Redis 会单独`创建`（fork）一个`子进程`进行`持久化`，会先将`数据`写入到一个`临时文件`中，待持久化过程中都结束了，再将用这个`临时文件`替换`上次持久化的文件`。整个过程中，主进程是不进行任何的IO操作。这就保证了极高的性能。
- 我们默认使用`RDB备份`，一般情况下`不需要`修改这个配置。
- rdb 保存的文件是 `dump.rdb` 都是我们再配置文件中快照进行配置的。
- rdb 文件一般会压缩处理，会占用一定的`cpu`。

### 10.1.2 触发机制
1. 手动触发
手动触发的命令：`save` 和 `bgsave`
- save 命令：会造成长时间的阻塞，线上环境不建议使用，`已废除`。
- bgsave命令：一般时间很短。

2. 自动触发
- 自动触发 `redis.conf` 里的`save m n` 会触发rdb规则
	```YAML
	save 900 1
	save 300 10
	save 60 10000
	```
- 执行`flushall `命令会触发rdb 规则
- 执行`shutdown`命令时，如果没有开启`AOF`持久化功能则自动执行rdb规则
- 执行`debug load`命令重复加载Redis时，会自动触发rdb规则
- `主从复制`中从节点中执行`全量复制`操作，`主节点`会`自动执行bgsave` 生成`RDB文件`并发送给`从节点`。

### 10.1.3 恢复rdb文件
1. 只需要将`rdb文件`放在我们的redis 启动目录`"/usr/local/bin"`，redis启动的时候会自动检查`dump.rdb` 恢复其中的数据！

2. 查看redis 启动目录的位置
```XML
127.0.0.1:6379> config get dir
1) "dir"
2) "/usr/local/bin"
```

### 10.1.4 优缺点
1. 优点
	- 适合大规模的数据恢复！
	- 对数据的完整性要求不高！
2. 缺点
	- 需要一定的时间间隔进程操作！如果redis意外宕机了，这个最后一次修改数据就没有了！
	- fork进程的时候，会占用一定的内容空间！

## 10.2 AOF

### 10.2.1 特点
- AOF 持久化：以独立日志的方式`记录命令`，重启的时候在重新执行AOF 文件。
- AOF 是为了解决数据持久化的`实时性`。AOF 是目前主流的Redis 持久化的主流方式。
- AOF 保存的文件是`appendonly.aof`文件


### 10.2.2 开启AOF 
1. 修改 redis.conf 
```Bash
############################## APPEND ONLY MODE ###############################

# By default Redis asynchronously dumps the dataset on disk. This mode is
# good enough in many applications, but an issue with the Redis process or
# a power outage may result into a few minutes of writes lost (depending on
# the configured save points).
#
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Please check http://redis.io/topics/persistence for more information.

appendonly yes
```

2. 重启redis

### 10.2.3 配置文件
```Bash
# 打开aof 模式
appendonly no 

# 持久化的文件的名字
appendfilename "appendonle.aof" 

# 每次修改都会 sync 。非常消耗性能。
# appendfsync always

# 每秒执行一次 sync,可能最丢失最后1秒的数据。
appendfsync everysec

# 不开启sync 但是，操作系统自己同步数据，速度最高！
# appendfsync no
```


### 10.2.4 修复AOF 

> 如果`.aof` 文件有错误，这个时候，redis 就不能启动
> 修复工具：`redis-check-aof` 
> 修复指令：`redis-check-aof —-fix appendonly.aof`

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705080.png)

### 10.2.5 重写.aof

> aof默认就是文件的诬陷追加，文件会越来越大！

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705260.png)

如果aof 文件大于64Mb !fork 一个新的进程将我们的文件进行重写！

### 10.2.6 优缺点

1. 优点
	- 时效性质比较好

2. 缺点

	- 相对于数据文件来说，aof 远远大于rdb ,修复速率也比rdb 慢。
	- aof 运行效率比 rdb 慢。
	
## 10.3 RDF 和 AOF 扩展
1. RDB持久化方式能够在指定的时间间隔内对数据进行快照。
2. AOF 持久化的方式记录每次对服务器写的操作，当服务器重启的时候就会重新执行命令从而恢复数据，AOF 命令以Redis 协议追加保存每次写的操作到文件末尾,Redis 还能对AOF 文件进行后台重写，使得AOF 文件的体积不至于过大。
3. `如果服务器只要做缓存，希望数据只在服务器运行的时候存在，不需要持久化。`
4. 同时开启两种持久化方式
	1. 优先使用AOF 来恢复原始的数据，因为AOF 比RDB 保存的数据更加完整。
	2. RDB的数据不实时，同时使用两种方式服务器重启只会使用AOF 文件。但是不要只使用AOF 方式，因为RDB 适合用于备份数据库（AOF在不断的变化，不好备份），快速重启，而且不会有AOF 可能存在的BUG。
5. 性能建议
	1. 因为RDB 文件只用作后备使用，建议在15分钟备份一次即可。只保留`save 900 1` 这一条规则。
	2. 如果使用AOF ,AOF 的好处是不会丢失超过两秒的数据，启动脚本只需要加载自己的AOF 文件即可。代价一：`带来了持续的IO`。代价二：`AOF rewrite 的最后将rewrite过程中产的新数据写道新文件造成的阻塞是无法避免的`。只要硬盘许可，尽量减少AOF rewrite 的频率，AOF 重写的基础大小为64M ，我们可以设置到`5G`以上，默认超过原大小`100%`大小重写可以改到适当的数值。
	3. 如果不适用AOF，仅靠Master-Slave Repllcation 实现高可用性也可以，能省掉一大笔IO ，也可以减少重写带来的系统波动。代价是如果Master/Slave 同时倒掉，会丢失实际分钟的数据，启动脚本也要比较两个Master/Slave 的RDB 文件，载入较新的文件，微博就是这个架构。

# 11 Redis 发布订阅
## 11.1 概念
Redis 发布订阅（pub/sub）是一种`消息通信模式`：发送者(pub)发送消息，订阅者(sub)接收信息。

Redis 客户端可以订阅任意数量的频道。

订阅/发布消息图：
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705546.png)


了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的关系：
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705200.png)

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户端：
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705298.png)

## 11.2 命令

| 序号 | 命令及描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | [PSUBSCRIBE pattern [pattern ...]](https://www.runoob.com/redis/pub-sub-psubscribe.html)订阅一个或多个符合给定模式的频道。 |
| 2    | [PUBSUB subcommand [argument [argument ...]]](https://www.runoob.com/redis/pub-sub-pubsub.html)查看订阅与发布系统状态。 |
| 3    | [PUBLISH channel message](https://www.runoob.com/redis/pub-sub-publish.html)将信息发送到指定的频道。 |
| 4    | [PUNSUBSCRIBE [pattern [pattern ...]]](https://www.runoob.com/redis/pub-sub-punsubscribe.html)退订所有给定模式的频道。 |
| 5    | [SUBSCRIBE channel [channel ...]](https://www.runoob.com/redis/pub-sub-subscribe.html)订阅给定的一个或多个频道的信息。 |
| 6    | [UNSUBSCRIBE [channel [channel ...]]](https://www.runoob.com/redis/pub-sub-unsubscribe.html)指退订给定的频道。 |

## 11.3 测试
> Redis

| 客户端                                                       | 服务端                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 127.0.0.1:6379> `SUBSCRIBE pig`<br />Reading messages... (press Ctrl-C to quit)<br />1) "subscribe"<br />2) "pig" |                                                              |
|                                                              | 127.0.0.1:6379> `PUBLISH pig "hello world"`<br />(integer) 1 |
| 1) "message"<br />2) "pig"<br />3) "hello world"             |                                                              |

## 11.4 原理
Redis是使用C实现的，通过分析 Redis 源码里的 pubsub.c 文件，了解发布和订阅机制的底层实现，因此加深对 Redis 的理解。Redis 通过 PUBLISH 、SUBSCRIBE 和 PSUBSCRIBE 等命令实现发布和订阅功能。

通过 SUBSCRIBE 命令订阅某频道后，redis-server 里维护了一个字典，字典的键就是一个个 频道！而字典的值则是一个链表，链表中保存了所有订阅这个 channel 的客户端。SUBSCRIBE 命令的关键，就是将客户端添加到给定 channel 的订阅链表中。
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705560.png)

Pub/Sub 从字面上理解就是发布（Publish）与订阅（Subscribe），在Redis中，你可以设定对某一个
key值进行消息发布及消息订阅，当一个key值上进行了消息发布后，所有订阅它的客户端都会收到相应
的消息。这一功能最明显的用法就是用作实时消息系统，比如普通的即时聊天，群聊等功能。

## 11.5 使用场景
1. 实时消息系统
2. 实时聊天系统！频道当做聊天室，将信息回显给所有人即可！
3. 订阅，关注系统

# 12 Redis主从复制
## 12.1 概念
主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点
(master/leader)，后者称为从节点(slave/follower)；`数据的复制是单向的，只能由主节点到从节点。`
Master以写为主，Slave 以读为主。

`默认情况下，每台Redis服务器都是主节点；`

`一个主节点可以有多个从节点(或没有从节点)，但一个从节点只能有一个主节点。`

## 12.2 解决问题
1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。
2. 数据故障：当主接待你出现问题时，可以由从节点提供服务，实现快速的故障恢复。实际上一种服务的冗余。
3. 负载均衡：在主从复制的基础的，配合读写份力，可以由主节点提供写服务，由从节点提供读服务（即写Redis数据时应用连接主节点，读Redis数据时应用连接从节点），分担服务器负载；尤其是在写少读少的场景下，通过多个从节点分担读负载，可以大大提高Redis 服务器的并发量。
4. 高可用（集群）基石：主从复制还是哨兵和集群能够实施的基础，因此说主从复制是Redis 高可用的基础。

## 12.3 架构
在项目工程中，只使用一台Redis是不够用的（宕机）：
1. 从结构上，单个Redis 服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力比较大。
2. 从容量上，单个Redis 服务器内存容量有限，单个服务琪最大使用的Redis 不应该超过20G。
电商网站上的商品，一般都是一次上传，无数次浏览的，说专业点也就是"多读少写"。
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705501.png)
主从复制，读写份力！80%的情况都是进行读操作！减缓服务器的压力！架构中经常使用一主二从！

## 12.4 主从复制
只需要配置从机，不需要配置主机！（默认是主机）
### 12.4.1 配置
1. 查看当前库的信息
```Bash
# 查看当前库的信息
127.0.0.1:6379> info replication
# Replication
role:master # 角色
connected_slaves:0 # 从机数量
master_replid:a0d45c4c44dd4c022835c8a5ab0885afd881767f
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

2. 复制配置文件
```Bash
[root@localhost config]# cp redis.conf redis79.conf
[root@localhost config]# cp redis.conf redis80.conf
[root@localhost config]# cp redis.conf redis81.conf
[root@localhost config]# ls
redis79.conf  redis80.conf  redis81.conf  redis.conf
```

3.  修改配置文件
```Bash
端口号
pid
log
dump.rdb
```

[redis81.conf](http://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/download/Mycode/redis81.conf)
[redis80.conf](http://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/download/Mycode/redis80.conf)
[redis79.conf](http://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/download/Mycode/redis79.conf)

3. 启动redis 
```Bash
redis-server config/redis79.conf
redis-server config/redis80.conf
redis-server config/redis81.conf
```

4. 配置一主二从
**命令（暂时）**
真实的从主配置应该在配置文件中配置，这样的话是永久的，我们这里使用的是命令，暂时的！
80：
```Bash
127.0.0.1:6380> SLAVEOF 127.0.0.1 6379
OK
127.0.0.1:6380> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6379
master_link_status:down
master_last_io_seconds_ago:-1
master_sync_in_progress:0
slave_repl_offset:1
master_link_down_since_seconds:1628540405
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:d48a2ccebce8e2a36c55db704dbd24d8a7a358b9
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

81:
```Bash
127.0.0.1:6381> SLAVEOF 127.0.0.1 6379
OK
127.0.0.1:6381> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6379
master_link_status:down
master_last_io_seconds_ago:-1
master_sync_in_progress:0
slave_repl_offset:1
master_link_down_since_seconds:1628540459
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:72180f8d2bb5e50448f88143c1dba33f6eb9fe1a
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
127.0.0.1:6381>
```

**配置文件(永久)**
```Bash
# redis 5.0之前
slaveof <masterport> <masterport>

# redis 5.0之后
replicaof <masterip> <masterport>
replicaof 192.168.249.20 6379
```

### 12.4.2 说明
主机可以写，从机不能写只能读。主机中的所有的信息和数据，都会被从机自动保存！

主机断开连接，从机依旧连接到主机，但是没有写操作，如果主机回来了，从机依旧可以直接获取到主机写的信息！

如果使用命令行配置的主从，如果重启，则变成主机。只要变为从机，立马会从主机中获取值。

### 12.4.3 复制原理
Slave 启动成功连接到 master 后会发送一个sync同步命令
Master 接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行
完毕之后，master将传送整个数据文件到slave，并完成一次完全同步。
`全量复制：`而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
`增量复制：`Master 继续将新的所有收集到的修改命令依次传给slave，完成同步
但是只要是重新连接master，一次完全同步（全量复制）将被自动执行！ 我们的数据一定可以在从机中
看到！

## 12.5 层层链路
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705493.png)

### 12.5.1 手动选择主机
如果主机断开了连接，我们可以使用 `SLAVEOF no one` 让自己变成主机！其他的节点就可以手动连
接到最新的这个主节点（手动）！如果这个时候老大修复了，那就重新连接！

## 12.6 哨兵模式
### 12.6.1 概念
自动选取老大的模式

主从切换技术的方法是：当主服务器宕机后，需要收到那个将一台服务器切换为主服务器，这就需要人工干预，还会造成一段时间服务不可用。这个时候选用哨兵模式。

层层链路的自动版，后台能够监控主机是否正常，如果故障了根据投票数自动将从库转换为主库。

哨兵模式是一种特殊的模式，首先Redis 是提供了哨兵的命令，哨兵是一个独立的进程，作为进程，他会独立运行。其原理是哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis 实例。
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705435.png)

### 12.6.2 作用
- 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。
- 当哨兵检测到主服务器宕机时，就会将一个从机切换为主机，然后通过`发布订阅的模式`通知其他的从服务器，修改配置文件，让他们切换回主机。

### 12.6.3 多哨兵
然而一个哨兵进程对Redis 进行监控，可能会出现问题。为此，我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控，这样iu形成了多哨兵模式。

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705960.png)![](image/image_2.png)

假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行failover过程，仅仅是哨兵1主观的认为主服务器不可用，这个现象成为主观下线。当后面的哨兵也检测到主服务器不可用，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行failover[故障转移]操作。切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为客观下线。

### 12.6.4 配置

1. 配置文件
`sentinel.conf
`
```bash
sentinel monitor myredis 127.0.0.1 6379 1
```

后面的这个数字1，代表主机挂了，slave投票看让谁接替成为主机，票数最多的，就会成为主机！

2. 启动哨兵
```Bash
[root@iZolr9hqmivdloZ bin]# redis-sentinel config/sentinel.conf

10715:X 10 Aug 2021 14:42:50.524 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
10715:X 10 Aug 2021 14:42:50.524 # Redis version=5.0.8, bits=64, commit=00000000, modified=0, pid=10715, just started
10715:X 10 Aug 2021 14:42:50.524 # Configuration loaded
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 5.0.8 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in sentinel mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 26379
 |    `-._   `._    /     _.-'    |     PID: 10715
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           http://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

10715:X 10 Aug 2021 14:42:50.525 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
10715:X 10 Aug 2021 14:42:50.525 # Sentinel ID is 94059a4f1b825bc7d7fc66a79dc7541696b3b91e
10715:X 10 Aug 2021 14:42:50.525 # +monitor master myredis 127.0.0.1 6379 quorum 1
```

如果主机此时回来了，只能归并到新的主机下，当做从机，这就是哨兵模式的规则！

### 12.6.5 优点和缺点

1. 优点
	- 哨兵集群，基于主从复模式，所有的主从配置的优点，哨兵全都有。
	- 主从可以切换，故障可以转移，系统的可用性就会更好。
	- 哨兵模式是主从模式的升级，手动到自动，更加健壮。
	- 
2. 缺点
	- 哨兵模式不可以在线扩容，集群容量一旦到达上限，在线扩容就十分麻烦！

### 12.6.6 全部配置
```Bash
# Example sentinel.conf
# 哨兵sentinel实例运行的端口 默认26379
port 26379

# 哨兵sentinel的工作目录
dir /tmp

# 哨兵sentinel监控的redis主节点的 ip port
# master-name 可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 配置多少个sentinel哨兵统一认为master主节点失联 那么这时客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 2

# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd

# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000

# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，
# 这个数字越小，完成failover所需的时间就越长，但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。
# 可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1

# 故障转移的超时时间 failover-timeout 可以用在以下这些方面：
#  1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#  2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#  3.当想要取消一个正在进行的failover所需要的时间。
#  4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了

# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000

# SCRIPTS EXECUTION
# 配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
# 对于脚本的运行结果有以下规则：
#  若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#  若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#  如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#  一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
#  通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，一个是事件的类型，一个是事件的描述。如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
# 通知脚本
# shell编程
# sentinel notification-script <master-name> <script-path>
sentinel notification-script mymaster /var/redis/notify.sh

# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh 
# 一般都是由运维来配置！
```

[sentinel.conf](http://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/download/Mycode/sentinel.conf)

# 13 Redis 缓存穿透和雪崩
## 13.1 穿透
### 13.1.1 概念

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705685.png)

用户想查询一个数据，发现`redis `中没有，也就是缓存没有命中，于是向`持久层数据库中`查询。如果`持久层数据库`中也没有，于是查询失败。当用户很多的时候，`缓存`都没有命中，于是都去请求`持久层数据库`。这个时候会给`持久层数据库`造成很大的压力，这个时候就造成了`缓存穿透`。

### 13.1.2 解决方法

1. 布隆过滤器
布隆过滤器是一种数据结构，对所有可能查询的参数都以hash形式存储，在控制层先校验，不符合则丢弃，从而避免了对底层存储系统的查询压力。
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705008.png)

2. 缓存空对象
- 方法

当存储层不命中时候，即使返回的空对象也将其缓存起来，同时设置一个过期时间，之后再访问这个数据就会从缓存中获取，保护了后端数据源。
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705114.png)

- 问题
1. 如果空值被缓存起来，这就意味着缓存需要更多的空间存储，需要更多的键，因为这当中可能会有的空值的键。
2. 即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响。

## 13.2 击穿
### 13.2.1 概述
这里需要注意和缓存击穿的区别，缓存击穿，是指一个key 非常热点，在不停的扛着大并发，大并发集中对一个点进行访问，当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库，就像在一个墙上凿开了一个洞。

当某个key在过期的瞬间，由大量的请求并发访问，这类数据一般是热点数据，由于缓存过期，会同时访问数据库来查询最新数据，并且回写缓存，会导致数据库瞬间压力过大。

### 13.2.2 解决方案
1. 设置热点数据永不过期：
从缓存层面来看，没有设置过期时间，所以不会出现热点key 过期后产生的问题。

2. 加互斥锁：
分布式锁：使用分布式锁，保证对于每个key 同时只有一个线程去查询后端服务，其他线程没有获得分布式锁的权限，因此只需要等待时间即可。这种方式将高并发的压力转移到了分布式锁，因此对分布式锁的考验很大。
![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705305.png)

## 13.3 雪崩

### 13.3.1 概念
缓存雪崩，是指在某一个时间段，缓存集中过期失效。Redis宕机！

产生雪崩的原因之一，比如马上就要到双十二零点，很快就会迎来一波抢购，这波商品时间比较集中的放入了缓存，假设缓存一个小时。那么到了凌晨一点钟的时候，这批商品的缓存就都过期了。而对这批商品的访问查询，都落到了数据库上，对于数据库而言，就会产生周期性的压力波峰。于是所有的请求都会达到存储层，存储层的调用量会暴增，造成存储层也会挂掉的情况。

![image.png](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208140705688.png)

其实集中过期，倒不是非常致命，比较致命的缓存雪崩，是缓存服务器某个节点宕机或断网。因为自然形成的缓存雪崩，一定是在某个时间段集中创建缓存，这个时候，数据库也是可以顶住压力的。无非就是对数据库产生周期性的压力而已。而缓存服务节点的宕机，对数据库服务器造成的压力是不可预知的，很有可能瞬间就把数据库压垮。

### 13.3.2 解决方案

1. redis 高可用
这个思想的含义是，既然redis有可能挂掉，那我多增设几台redis，这样一台挂掉之后其他的还可以继续工作，其实就是搭建的集群。（异地多活！）

2. 限流降级
这个解决方案的思想是，在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。

3. 数据预热
数据加热的含义就是在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中。在即将发生大并发访问前手动触发加载缓存不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀。