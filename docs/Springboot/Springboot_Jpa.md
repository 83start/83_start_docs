## 1 参考资料

1. [Spring Boot(三)：Spring Boot Jpa的介绍及Jpa的使用](http://t.csdn.cn/XH5sv)
2. [spring-boot 整合 JPA](http://docs.tumo.tycoding.cn/#/docs/boot/spring-boot-jpa?id=delete)
3. [springboot整合jpa，步骤详细（图文结合讲解）](http://t.csdn.cn/VCUbW)
4. [Spring Data JPA 简单查询-接口方法](https://www.cnblogs.com/logsharing/p/8462026.html)
5. [Spring Data JPA 简单查询--方法定义规则](https://www.cnblogs.com/rulian/p/6434631.html)
6. [Spring Data JPA 进阶（自定义查询，原生 SQL 查询）](https://blog.csdn.net/chaotiantian/article/details/115933280)
7. [Jpa 实体类常用注解](http://t.csdn.cn/UgWmF)

## 2 Springboot Jpa简介

### 2.1 什么是Jpa

JPA顾名思义就是Java Persistence API的意思，是JDK 5.0注解或XML描述对象－关系表的映射关系，并将运行期的实体对象持久化到数据库中。Jpa 是一套规范，不是一套产品。

Jpa (Java Persistence API) 是 Sun 官方提出的 Java 持久化规范。它为 Java 开发人员提供了一种对象/关联映射工具来管理 Java 应用中的关系数据。它的出现主要是为了简化现有的持久化开发工作和整合 ORM 技术，结束现在 Hibernate，TopLink，JDO 等 ORM 框架各自为营的局面。Jpa是在充分吸收了现有 Hibernate，TopLink，JDO 等 ORM 框架的基础上发展而来的，具有易于使用，伸缩性强等优点。从目前的开发社区的反应上看，Jpa 受到了极大的支持和赞扬，其中就包括了 Spring 与 EJB3. 0的开发团队。

### 2.2 什么是Springboot Jpa

Spring Boot Jpa 是 Spring 基于 ORM 框架、Jpa 规范的基础上封装的一套 Jpa 应用框架，可使开发者用极简的代码即可实现对数据的访问和操作。它提供了包括增删改查等在内的常用功能，且易于扩展！学习并使用 Spring Data Jpa 可以极大提高开发效率！

## 3 Springboot 整合 Jpa

### 3.1 创建项目

1. 创建Springboot -maven 项目
2. Springboot 的版本为 `2.1.17.RELEASE`
3. 项目的名称为 `Springboot-Jpa`

### 3.2 导入Maven依赖

```xml
<!-- 👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇引用常用的依赖👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.17</version>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>

<!-- 👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆引用常用的依赖👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆👆 -->
```

### 3.3 编写配置文件

> 由于使用了spring-boot开发，spring-boot能够减轻我们在XML文件中繁琐的配置，因此在spring-boot中配置JPA也格外简单。

 **application.properties**

```properties
# 应用名称
spring.application.name=jpa

#自动生成数据库表（关键）
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
#jpa配置：在控制台显示Hibernate的sql(可选)
spring.jpa.show-sql = true

#mysql数据库连接配置（非常重要）
spring.datasource.url = jdbc:mysql://localhost:3306/springboot_jpa?serverTimezone=Asia/Shanghai
#数据库用户名
spring.datasource.username = root
#数据库密码
spring.datasource.password = root
#mysql数据库驱动程序（重要）
spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver
#其他配置：关闭Thymeleaf 的缓存
spring.thymeleaf.cache = false

server.port=8080
```

**application.yml**

```yml
spring:
  application:
    # 应用名称
    name: jpa
    
  # springboot - jpa 配置
  jpa:
    hibernate:
      # 自动生成数据库表（关键）
      ddl-auto: update
      dialect: org.hibernate.dialect.MySQL5Dialect
    # 在控制台显示hibernate的sql
    show-sql: true
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL5InnoDBDialect

  # mysql 数据库连接配置（非常重要）
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost.3306/springboot_jpa?serverTimezone=Asia/Shanghai
    username: root
    password: root

  # 关闭 thymeleaf 的缓存
  thymeleaf:
    cache: false

server:
  port: 8080
```

- `spring.jpa.hibernate.ddl-auto=update`：`jpa`是一套规范，而如`hibernate`仅是基于`jpa`的一套产品。开启update，hibernate可以根据`Entity`属性自动创建数据库，如果想要手动创建表结构，就不要配置`auto=update`
- `spring.datasource.url`：`datasource`下的`url`数据库连接在`数据库名`后面一定要拼接`?characterEncoding=utf-8`，目的是避免调用JPA的方法存入数据库中的数据**中文乱码**

### 3.4 自动创建数据表

> `hibernate` 是`jpa` 的一套产品。`hibernate` 可以根据 `Entity/Pojo` 实体类自动创建数据库，`Jpa`也是可以使用的。
>
> - 需要在配置文件`application.yml` 中开启`spring.jpa.hibernate.ddl-auto=update`。
> - JPA不能自动创建数据库，数据库需要手动创建。

1. 修改配置文件(application.properties)

   ```xml
   spring.jpa.properties.hibernate.hbm2ddl.auto=update
   ```

2. 创建实体类

   ```java
   package com.test.springboot.jpa.pojo;
   
   import lombok.Data;
   import org.springframework.data.annotation.CreatedDate;
   import org.springframework.data.annotation.LastModifiedDate;
   import org.springframework.data.jpa.domain.support.AuditingEntityListener;
   import org.springframework.format.annotation.DateTimeFormat;
   
   import javax.persistence.*;
   import java.io.Serializable;
   import java.util.Date;
   
   /**
    * @author thinkpad - 83start
    * @version v1.0
    * @create 2022/8/18 2:24
    * @package com.test.springboot.jpa.pojo
    * @description 根据实体类自动创建 表
    *              Java类型和对应的表属性关系
    *              JPA 常见注解 和 操作
    */
   
   @Entity //指定当前类是实体类，对应数据库中的一个表
   @Data
   @Table(name = "tb_create_table")
   @EntityListeners(AuditingEntityListener.class)// 封装生成自动时间
   public class CreateTable implements Serializable {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY) // 设定主键生成策略：IDENTITY 表示由数据库自动生成
       private Long id; // Long 对应 MySQL 数据库的 bigint 类型
   
       @Column(name = "string_value",nullable = false,unique = true,length = 20) //  不为空，唯一，且字符最大长度为 20
       private String stringValue; // String 对应 MySQL 数据库中的 varchar 类型
   
       @Column(name = "integer_value")
       private Integer integerValue = 3; // 赋值默认值
   
       @Column(name = "double_value")
       private Double doubleValue;
   
       /**
        * 自动生成时间：create 生效
        */
       @CreatedDate
       @Column(name = "create_time")
       @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") // 定义时间格式
       private Date createTime;
   
       /**
        * 自动生成时间：update 生效
        */
       @LastModifiedDate
       @Column(name = "update_time")
       @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") // 定义时间格式
       private Date updateTime;
   
       @Transient //如果你想要Entity中的某个属性不被识别为表字段，可以用@Transient注解标记。
       private String transientValue;
   }
   ```

3. 重启 Springboot 项目

   重新启动项目，就会自动生成数据库表。

   ![image-20220818222135196](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208182221355.png)

### 3.5 编写实体类

> 实体类于数据库相对应。
>
> ​		class –> table
>
> ​		常量 –> column

#### 3.5.1 Java数据类型于数据库数据类型对应

> 对于不同的DB可能有所差异)

| Java数据类型                       | Hibernate数据类型 | 标准SQL数据类型   |
| ---------------------------------- | ----------------- | ----------------- |
| byte、java.lang.Byte               | byte              | TINYINT           |
| short、java.lang.Short             | short             | SMALLINT          |
| int、java.lang.Integer             | integer           | INGEGER           |
| long、java.lang.Long               | long              | BIGINT            |
| float、java.lang.Float             | float             | FLOAT             |
| double、java.lang.Double           | double            | DOUBLE            |
| java.math.BigDecimal               | big_decimal       | NUMERIC           |
| char、java.lang.Character          | character         | CHAR(1)           |
| boolean、java.lang.Boolean         | boolean           | BIT               |
| java.lang.String                   | string            | VARCHAR           |
| boolean、java.lang.Boolean         | yes_no            | CHAR(1)(‘Y’或‘N’) |
| boolean、java.lang.Boolean         | true_false        | CHAR(1)(‘Y’或‘N’) |
| java.util.Date、java.sql.Date      | date              | DATE              |
| java.util.Date、java.sql.Time      | time              | TIME              |
| java.util.Date、java.sql.Timestamp | timestamp         | TIMESTAMP         |
| java.util.Calendar                 | calendar          | TIMESTAMP         |
| java.util.Calendar                 | calendar_date     | DATE              |
| byte[]                             | binary            | VARBINARY、BLOB   |
| java.lang.String                   | text              | CLOB              |
| java.io.Serializable               | serializable      | VARBINARY、BLOB   |
| java.sql.Clob                      | clob              | CLOB              |
| java.sql.Blob                      | blob              | BLOB              |
| java.lang.Class                    | class             | VARCHAR           |
| java.util.Locale                   | locale            | VARCHAR           |
| java.util.TimeZone                 | timezone          | VARCHAR           |
| java.util.Currency                 | currency          | VARCHAR           |

#### 3.5.2 常见注解（Class）：

- @Entity：被Entity标注的实体类将会被JPA管理控制，在程序运行时，JPA会识别并映射到指定的数据库表

  - name：指定实体类名称，默认为当前实体类的非限定名称。

    > 若给了name属性值即@Entity(name="XXX")，则jpa在仓储层(数据层)进行自定义查询时，所查的表名应是XXX。 如：select s from XXX s

- @Table：当数据库表名与实体类名称不同时，使用 @Table(name="数据库表名"),与@Entity标注并列使用，置于实体类声明语句之前

  - name：指定对应数据库中的表名
  - catalog（不常用）：用于设置表所映射到的数据库的目录
  - schema（不常用）：用于设置表所映射到的数据库的模式
  - uniqueConstraints（不常用）： 设置约束条件

#### 3.5.3 常见注解（常量）：

- @Id：被标注的常量将映射为数据库的主键

  > JPA官方规定数据表必须有主键（id），而之前权限管理项目中一些关联表（用户角色关联表）是不需要主键的，但如果使用JPA就必须配置主键，不管你用不用。

- @GeneratedValue：与@Id一同使用，用于标注主键的生成策略。

  - strategy： 
    - IDENTITY：采用数据库ID自增长的方式产生主键，Oracle 不支持这种方式。
    - AUTO： JPA 自动选择合适的策略，是默认选项。
    - SEQUENCE：通过序列产生主键，通过@SequenceGenerator标注指定序列名，MySQL 不支持这种方式。
    - TABLE：通过表产生主键，框架借由表模拟序列产生主键，使用该策略更易于做数据库移植。
  
- @Basic：一个简单的属性到数据库表的字段的映射,对于没有任何标注的 getXxxx() 方法,默认即为@Basic(fetch=FetechType.EAGER)

  - fetch：表示该属性的加载读取策略
    - EAGER 主动抓取 （默认为EAGER）
    - LAZY 延迟加载,只有用到该属性时才会去加载
  
  -  optional (默认为 true)：表示该属性是否允许为null
  

- @Column：声明实体的属性

  - name: 指定映射到数据库中的字段名
    2. unique: 是否唯一，默认为false
    3. nullable: 是否允许为null，默认为true
    4. insertable: 是否允许插入，默认为true
    5. updatetable: 是否允许更新，默认为true
    - columnDefinition: 指定该属性映射到数据库中的实际类型，通常是自动判断。

- @Transient：JPA会忽略该属性，不会映射到数据库中，即程序运行后数据库中将不会有该字段

- @Temporal：Java中没有定义 Date 类型的精度，而数据库中，表示时间类型的数据有 DATE,TIME,TIMESTAMP三种精度

  - @Temporal(TemporalType.DATE) 表示映射到数据库中的时间类型为 DATE，只有日期
    - @Temporal(TemporalType.TIME) 表示映射到数据库中的时间类型为 TIME，只有时间
    - @Temporal(TemporalType.TIMESTAMP) 表示映射到数据库中的时间类型为 TIMESTAMP,日期和时间都有

- @Embedded 和 @Embeddable：用于一个实体类要在多个不同的实体类中进行使用，而本身又不需要独立生成一个数据库表

  > 具体参考：[Hibernate中@Embedded和@Embeddable注解的使用](https://blog.csdn.net/lmy86263/article/details/52108130)

- @JoinColumn：定义表关联的外键字段名

  - name: 指定映射到数据库中的外键的字段名
    2. unique: 是否唯一，默认为false
    3. nullable: 是否允许为null，默认为true
    4. insertable: 是否允许插入，默认为true
    5. updatetable: 是否允许更新，默认为true
    6. columnDefinition: 指定该属性映射到数据库中的实际类型，通常是自动判断。
    7. foreignKey = @ForeignKey(name = "none",value = ConstraintMode.NO_CONSTRAINT)：指定外键相关信息，这里用法是指定外联关系但是不建立数据库外键

- @OneToOne：

  - targetEntity： 指定关联实体类型，默认为被注解的属性或方法所属的类

  - cascade： 级联操作策略

    - CascadeType.ALL 级联所有操作
    - CascadeType.PERSIST 级联新增
    - CascadeType.MERGE 级联归并更新
    - CascadeType.REMOVE 级联删除
    - CascadeType.REFRESH 级联刷新
    - CascadeType.DETACH 级联分离

  - fetch： fetch 表示该属性的加载读取策略 (默认值为 EAGER)

    - EAGER 主动抓取
    - LAZY 延迟加载,只有用到该属性时才会去加载

  - optional： 

    - 默认为true，关联字段是否为空
    - 如果为false，则常与@JoinColumn一起使用

  - mappedBy： 指定关联关系，该参数只用于关联关系被拥有方只用于双向关联@OneToOne，@OneToMany，@ManyToMany。而@ManyToOne中没有@OneToOne(mappedBy = “xxx”)
    表示xxx所对应的类为关系被拥有方，而关联的另一方为关系拥有方

    - 关系拥有方：对应拥有外键的数据库表
    - 关系被拥有方：对应主键被子表引用为外键的数据库表

  - orphanRemoval:默认值为false
    判断是否自动删除与关系拥有方不存在联系的关系被拥有方(关系被拥有方的一个主键在关系拥有方中未被引用，当jpa执行更新操作时，是否删除数据库中此主键所对应的一条记录，若为true则删除)

    ```java
    //单向 一对一
    @Entity  
    public class Emp{//员工
      @Id
      @GeneratedValue
      privte Integer eId;
    
      @Column(length = 40)
      private String empName;
    
      @OneToOne(cascade = CascadeType.ALL)
      private Identity identity;
      //get,set方法省略
    }
    
    @Entity
    public class Identity{//身份证
      //...
    }
    ```

    ```java
    //双向 一对一
    @Entity  
    public class Emp{
      @Id
      @GeneratedValue
      privte Integer eId;
    
      @Column(length = 40)
      private String empName;
    
      @OneToOne(cascade = CascadeType.ALL)
      private Identity identity;
      //get,set方法省略
    }
    
    @Entity
    public class Identity{
      @Id
      @GeneratedValue
      privte Integer iId;
    
      @OneToOne(cascade = CascadeType.ALL, mappedBy = "identity")
      private Emp emp;
      //...
    }
    ```

    以上例子，双向一对一，Emp 为关系拥有方，Identity 为关系被拥有方。
    执行spring-data-jpa新增操作时，如果通过Identity的数据访问层进行新增操作(IdentityRepository.save())
    ，Emp表和Identity表都有数据，但是不会设置这两条数据的关系，Emp表中的外键为null。
    反之，以关系拥有方Emp的数据访问层进行新增操作(EmpRepository.save()),Emp表和Identity表都有数据，并且设置了两条数据的关系，即Emp表中的外键也得到正确新增。

- @ManyToOne、@OneToMany

  > 多对一(也可叫一对多，只是前后表颠倒一下而已)，只有双向多对一时才用得到@OneToMany。多对一中多的一方必定是对应数据库中拥有外键的表，即是关系拥有方，@ManyToOne只用在多对一中代表多的一类中，因为mappedBy只用于关系被拥有方，所以@ManyToOne参数中不包含mappedBy。

  **@ManyToOne参数：**

  - targetEntity： 指定关联实体类型，默认为被注解的属性或方法所属的类
  - cascade： 级联操作策略
    - CascadeType.ALL 级联所有操作
    - CascadeType.PERSIST 级联新增
    - CascadeType.MERGE 级联归并更新
    - CascadeType.REMOVE 级联删除
    - CascadeType.REFRESH 级联刷新
    - CascadeType.DETACH 级联分离
  - fetch： fetch 表示该属性的加载读取策略(@ManyToOne 的默认值是 EAGER，@OneToMany 的默认值是 LAZY)
    - EAGER 主动抓取
    - LAZY 延迟加载,只有用到该属性时才会去加载
  - optional： 
    - 默认为true，关联字段是否为空
    - 如果为false，则常与@JoinColumn一起使用

   **@OneToMany参数除上述以外还有：**

  - mappedBy: 指定关联关系，该参数只用于关联关系被拥有方
    只用于双向关联@OneToOne,@OneToMany,@ManyToMany。而@ManyToOne中没有
    @OneToMany(mappedBy = “xxx”)
    表示xxx所对应的类为关系被拥有方，而关联的另一方为关系拥有方
    - 关系拥有方：对应拥有外键的数据库表
    - 关系被拥有方：对应主键被子表引用为外键的数据库表
  - orphanRemoval:默认值为false
    判断是否自动删除与关系拥有方不存在联系的关系被拥有方(关系被拥有方的一个主键在关系拥有方中未被引用，当jpa执行更新操作时，是否删除数据库中此主键所对应的一条记录，若为true则删除)

  ```java
  //单向 多对一
  @Entity
  public class Emp{
    @Id
    @GeneratedValue
    privte Integer eId;
  
    @Column(length = 40)
    private String empName;
  
    @ManyToOne(cascade = CascadeType.ALL)
    private Dept dept;
    //...
  }
  
  @Entity
  public class Dept{
    @Id
    @GeneratedValue
    privte Integer dId;
  
    @Column(length = 40)
    private String deptName;
    //...
  }
  ```

  ```java
  //双向 多对一
  @Entity
  public class Emp{
    @Id
    @GeneratedValue
    privte Integer eId;
  
    @Column(length = 40)
    private String empName;
  
    @ManyToOne(cascade = CascadeType.ALL)
    private Emp emp;
    //...
  }
  
  @Entity
  public class Dept{
    @Id
    @GeneratedValue
    privte Integer dId;
  
    @Column(length = 40)
    private String deptName;
  
    @OneToMany(cascade = CascadeType.ALL, mappedBy = "emp")
    private List<Emp> emps;
    //...
  }
  ```

  > 无论双向关联还是单向关联，数据库中均会在Emp表中自动生成一个外键（dept_d_id）

- @ManyToMany：

  - targetEntity： 指定关联实体类型，默认为被注解的属性或方法所属的类
  - cascade： 级联操作策略
    - CascadeType.ALL 级联所有操作
    - CascadeType.PERSIST 级联新增
    - CascadeType.MERGE 级联归并更新
    - CascadeType.REMOVE 级联删除
    - CascadeType.REFRESH 级联刷新
    - CascadeType.DETACH 级联分离
  - fetch： fetch 表示该属性的加载读取策略 (默认值为 LAZY)
    - EAGER 主动抓取
    - LAZY 延迟加载,只有用到该属性时才会去加载
  - mappedBy: 指定关联关系，该参数只用于关联关系被拥有方
    只用于双向关联@OneToOne,@OneToMany,@ManyToMany。而@ManyToOne中没有。
    @ManyToMany(mappedBy = “xxx”)表示xxx所对应的类为关系被拥有方，而关联的另一方为关系拥有方：
    - 关系拥有方：对应拥有外键的数据库表
    - 关系被拥有方：对应主键被子表引用为外键的数据库表

  ```java
  //单向 多对多
  @Entity
  public class Student{
    @ManyToMany(cascade = CascadeType.ALL)
    private List<Course> courses;
    //...
  }
  
  @Entity
  public class Course{
    //...
  }
  ```

  ```java
  //双向 多对多
  @Entity
  public class Student{
    @ManyToMany(cascade = CascadeType.ALL)
    private List<Course> courses;
    //...
  }
  
  @Entity
  public class Course{
    @ManyToMany(cascade = CascadeType.ALL, mappedBy = "courses")
    private List<Student> students;
    //...
  }
  ```

  > 所有双向关联使用时需谨慎，查询时容易引起栈内存溢出，尽量使用单向关联

-  @Enumerated：当[实体类](https://so.csdn.net/so/search?q=实体类&spm=1001.2101.3001.7020)中有枚举类型的属性时，默认情况下自动生成的数据库表中对应的字段类型是枚举的索引值，是数字类型的，若希望数据库中存储的是枚举对应的String类型，在属性上加入`@Enumerated(EnumType.STRING)`注解即可。

  ```java
  @Enumerated(EnumType.STRING)
  @Column(nullable = true)
  private RoleEnum role;
  ```

#### 3.5.4 创建自动时间

> 对应的SQL语句：
>
> - `create_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间', 
> - `update_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
>
> `create_time`根据自动封装创建时间、`update_time`自动更新为最新操作的时间。

1. 修改Entity 类

   ```JAVA
   @EntityListeners(AuditingEntityListener.class)
   public class User implements Serializable { 
   	@CreatedDate
       @Column(name = "create_time")
       private Date createTime;
   
       @LastModifiedDate
       @Column(name = "update_time")
       private Date updateTime;
   }
   ```

2. 修改启动文件

   ```java
   @EnableJpaAuditing
   public class SpringBootJpaApplication { ... }
   ```

### 3.6 创建Dao文件

#### 3.6.1 Repository的继承关系

![img](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208182353073.png)

> 可能你会见到有的案例中`extends CrudRepository<T, ID>`等，其实都是可以的，翻看源码就知道，继承`JpaRepository`也可以使用`CrudRepository`的方法

#### 3.6.2 继承创建Dao文件

```java
public interface UserDao extends JpaRepository<User, Long> {  }
```

> `JpaRepository<T, ID>`，要指定泛型类型，否则Spring注入这个Bean的时候发现该Bean泛型类型不定而注入失败。

### 3.7 内置的查询方法

#### 3.7.1  方法列表

**JpaRepository：**

![img](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208182359646.png)

**CrudRepository：**

![img](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208190000242.png)

1. 先按照功能进行分类整理，分为保存、删除、查找单个、查找多个、其他5类。
2. 将不建议使用的方法置灰，此类方法多为CrudRepository接口、PagingAndSortingRepository接口中定义，后来JpaRepository接口中又定义了替代方法，更方便使用，比如：查找多个对象时，返回 List 比返回 Iterable 更容易处理。

![img](https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/HaloFile/202208190003998.png)

#### 3.7.2 编写service 方法

```java
// >>>>>>>>>>>>>>>>>>>>>>>>>>>> (interface) UserCrudService.java <<<<<<<<<<<<<<<<<<<<<<<<<
    
package com.test.springboot.jpa.service;

import com.test.springboot.jpa.pojo.User;
import org.springframework.data.domain.Example;
import java.util.List;

public interface UserCrudService {
    // ================================= count exists ==============================
    /**
     * 计数
     * @return
     */
    long countUser();

    /**
     * 判断用户是否存在
     * @param userExample
     * @return
     */
    boolean existUser(Example<User> userExample);

    /**
     * 通过主键判断记录是否存在
     * @param userId
     * @return
     */
    boolean existByPrimaryKey(Long userId);

    // ================================= select ==============================
    /**
     * 查询所有的记录
     * @return
     */
    List<User> selectAllUser();

    /**
     * 根据主键查询记录
     * @param userId
     * @return
     */
    User selectByPrimaryKey(Long userId);

    /**
     * 动态查询记录
     * @param userExample
     * @return
     */
    List<User>  selectByUserExample(Example<User> userExample);

    // ================================= create ==============================
    /**
     * 添加一条记录
     * @param user
     * @return
     */
    boolean insertUser(User user);

    /**
     * 批量添加记录
     * @param userList
     * @return
     */
    boolean insertAllUsers(List<User> userList);

    // ================================= update ==============================
    /**
     * 更新记录
     * @param user
     * @return
     */
    boolean updateUser(User user);

    // ================================= delete ==============================

    /**
     * 删除表中所有的记录
     * @return
     */
    boolean deleteAll();

    /**
     * 删除指定主键的记录
     * @param userId
     * @return
     */
    boolean deleteByPrimaryKey(Long userId);

    /**
     * 批量删除记录
     * @param userList
     * @return
     */
    boolean deleteInBranch(List<User> userList);

    /**
     * 动态删除记录
     * @param user
     * @return
     */
    boolean deleteByUser(User user);
}
```

```java
// >>>>>>>>>>>>>>>>>>>>>>>>>>>>  UserCrudServiceImpl.java <<<<<<<<<<<<<<<<<<<<<<<<<

package com.test.springboot.jpa.service.impl;

import com.test.springboot.jpa.dao.UserDao;
import com.test.springboot.jpa.pojo.User;
import com.test.springboot.jpa.service.UserCrudService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.dao.DataIntegrityViolationException;
import org.springframework.data.domain.Example;
import org.springframework.stereotype.Service;
import java.util.ArrayList;
import java.util.List;
import java.util.NoSuchElementException;

@Service
@Slf4j
public class UserCrudServiceImpl implements UserCrudService {

    @Autowired
    private UserDao userDao;


    @Override
    public long countUser() {
        return userDao.count();
    }

    @Override
    public boolean existUser(Example<User> userExample) {
        return userDao.exists(userExample);
    }

    @Override
    public boolean existByPrimaryKey(Long userId) {
        return userDao.existsById(userId);
    }

    @Override
    public List<User> selectAllUser() {
        return userDao.findAll();
    }

    @Override
    public User selectByPrimaryKey(Long userId) {
        User user = null;
        try {
             user = userDao.findById(userId).get();
        }catch (NoSuchElementException e){
            log.info("java.util.NoSuchElementException: No value present:没有这条记录：====>>> user_id = {}",userId);
        }
        catch (Exception e) {
            log.info("Exception: 有其他错误");
        }
        return user;
    }

    @Override
    public List<User> selectByUserExample(Example<User> userExample) {
        List<User> userExampleList = userDao.findAll(userExample);
        return userExampleList;
    }

    @Override
    public boolean insertUser(User user) {
        User createUser = null;
        try {
            createUser = userDao.save(user);
            log.info("insert user info log ===>>> {}",createUser);
        }catch (DataIntegrityViolationException e){
            log.info("org.springframework.dao.DataIntegrityViolationException: 唯一值冲突");
        }
        catch (Exception e) {
            log.info("Exception: 其他错误");
            e.printStackTrace();
        }
        return createUser != null;
    }

    @Override
    public boolean insertAllUsers(List<User> userList) {
        List<User> createUserList = userDao.saveAll(userList);
        log.info("insert into nums log ===>>> {}",createUserList.size());
        return createUserList.size() != 0;
    }

    @Override
    public boolean updateUser(User user) {
        if (user.getUserId() == null){
            log.info("没有主键，请执行 insert 操作");
            return false;
        }
        User startUser = selectByPrimaryKey(user.getUserId());
        userDao.save(user);
        User updateUser = selectByPrimaryKey(user.getUserId());
        return startUser != updateUser;
    }

    @Override
    public boolean deleteAll() {
        long startNum = countUser();

        userDao.deleteAll();
        userDao.flush();

        long endNum = countUser();

        return startNum != endNum;
    }

    @Override
    public boolean deleteByPrimaryKey(Long userId) {
        long startCount = userDao.count();

        userDao.deleteById(userId);

        long endCount = userDao.count();

        return startCount != endCount;
    }

    @Override
    public boolean deleteInBranch(List<User> userList) {
        long startCount = userDao.count();
        userDao.deleteInBatch(userList);

        long endCount = userDao.count();

        return startCount != endCount;
    }

    /**
     * TODO need to test
     * @param user
     * @return
     */
    @Override
    public boolean deleteByUser(User user) {
        return false;
    }
}

```

#### 3.7.3 编写test方法

使用Idea的话，在`UserDao.java`接口名前按`Alt + Enter`键(mac)，快速生成测试类`UserDaoTest.java`

```JAVA
// >>>>>>>>>>>>>>>>>>>>>>>>>>>>  UserCrudServiceImplTest.java <<<<<<<<<<<<<<<<<<<<<<<<<

package com.test.springboot.jpa.service.impl;

import com.test.springboot.jpa.JpaApplicationTests;
import com.test.springboot.jpa.dao.UserDao;
import com.test.springboot.jpa.pojo.User;
import com.test.springboot.jpa.service.UserCrudService;
import lombok.extern.slf4j.Slf4j;
import org.junit.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Example;
import java.util.ArrayList;
import java.util.List;

@Slf4j
public class UserCrudServiceImplTest extends JpaApplicationTests {
    @Autowired
    private UserCrudService userCrudService;

    @Autowired
    private UserDao userDao;


    @Test
    public void countUser() {
        long userCount = userCrudService.countUser();
        log.info("count user number log ===>>> {}",userCount);
    }

    @Test
    public void existUser(){
        Long userId = 15L;
        User user = userCrudService.selectByPrimaryKey(userId);
        Example<User> userExample = Example.of(user);

        boolean flag = userCrudService.existUser(userExample);
        log.info("user is exist flag by user log ===>>> {}",flag);
    }

    @Test
    public void existByPrimaryKey(){
        Long userId = 75L;
        boolean flag = userCrudService.existByPrimaryKey(userId);

        log.info("user is exist by primary key flag log ===>>> {}",flag);
    }

    @Test
    public void selectAllUser() {
        List<User> userList = userCrudService.selectAllUser();

        userList.forEach(user -> {
            log.info("select all user log ===>>> {}",user);
        });
    }

    @Test
    public void selectByPrimaryKey() {
        Long user_id = 1L;
        User user = userCrudService.selectByPrimaryKey(user_id);
        if(user != null){
            log.info("select user by primary key log ===>>> {}",user);
        }
    }

    @Test
    public void selectByUserExample() {
        User user1 = new User();
        user1.setUserName("caojurfsdan");

        Example<User> userExample = Example.of(user1);

        List<User> userList = userCrudService.selectByUserExample(userExample);

        userList.forEach(user -> {
            log.info("select all user log ===>>> {}",user);
        });
    }

    @Test
    public void insertUser() {
        User user = new User();
        user.setUserName("cao78j3un");
        user.setUserPassword("java");

        boolean flag = userCrudService.insertUser(user);
        log.info("create user log ===>>> {}",flag);
    }

    @Test
    public void insertAllUsers() {
        List<User> userList = new ArrayList<>();

        userList.add(new User("java12","language"));
        userList.add(new User("java23","language"));
        userList.add(new User("java313","language"));

        boolean flag = userCrudService.insertAllUsers(userList);
        System.out.println(flag);
    }

    @Test
    public void updateUser() {
        User user = new User(1L,"chenggong","jkflsd");
        boolean flag = userCrudService.updateUser(user);

        log.info("update user flag log ===>>> {}",flag);

        User updateUser = userCrudService.selectByPrimaryKey(user.getUserId());

        log.info("update user info log ===>>> {}",updateUser);
    }

    @Test
    public void deleteAll() {
        boolean flag = userCrudService.deleteAll();

        log.info("delete all user flag log ===>>> {}",flag);
    }

    @Test
    public void deleteByPrimaryKey() {
        User user = new User(12L);
        boolean flag = userCrudService.deleteByPrimaryKey(user.getUserId());

        log.info("delete by primary key flag log ===>>> {}",flag);
    }

    @Test
    public void deleteInBranch() {
        List<User> userList = new ArrayList<>();

        userList.add(new User(13L));
        userList.add(new User(14L));

        boolean flag = userCrudService.deleteInBranch(userList);

        log.info("delete in branch flag log ===>>> {}",flag);
    }

    @Test
    public void deleteByUser() {
    }
}
```

### 3.8 自定义简单查询

自定义简单查询就是根据方法名自动生成SQL。前面是用JPA内置的方法进行简单的CRUD，下面可以自定义方法名实现自定义SQL：主要语法是：`findByXX`,`readByXX`,`queryByXX`,`countByXX`。`XX`代表属性名。

#### 3.8.1 常用关键字

基本上SQL体系中的关键字在JPA上都有对应的，具体的关键字和使用方法生成的SQL如下表：

| Keyword           | Sample                                  | JPQL snippet                                                 |
| ----------------- | --------------------------------------- | ------------------------------------------------------------ |
| And               | findByLastnameAndFirstname              | … where x.lastname = ?1 and x.firstname = ?2                 |
| Or                | findByLastnameOrFirstname               | … where x.lastname = ?1 or x.firstname = ?2                  |
| Is,Equals         | findByFirstnameIs,findByFirstnameEquals | … where x.firstname = ?1                                     |
| Between           | findByStartDateBetween                  | … where x.startDate between ?1 and ?2                        |
| LessThan          | findByAgeLessThan                       | … where x.age < ?1                                           |
| LessThanEqual     | findByAgeLessThanEqual                  | … where x.age ⇐ ?1                                           |
| GreaterThan       | findByAgeGreaterThan                    | … where x.age > ?1                                           |
| GreaterThanEqual  | findByAgeGreaterThanEqual               | … where x.age >= ?1                                          |
| After             | findByStartDateAfter                    | … where x.startDate > ?1                                     |
| Before            | findByStartDateBefore                   | … where x.startDate < ?1                                     |
| IsNull            | findByAgeIsNull                         | … where x.age is null                                        |
| IsNotNull,NotNull | findByAge(Is)NotNull                    | … where x.age not null                                       |
| Like              | findByFirstnameLike                     | … where x.firstname like ?1                                  |
| NotLike           | findByFirstnameNotLike                  | … where x.firstname not like ?1                              |
| StartingWith      | findByFirstnameStartingWith             | … where x.firstname like ?1 (parameter bound with appended %) |
| EndingWith        | findByFirstnameEndingWith               | … where x.firstname like ?1 (parameter bound with prepended %) |
| Containing        | findByFirstnameContaining               | … where x.firstname like ?1 (parameter bound wrapped in %)   |
| OrderBy           | findByAgeOrderByLastnameDesc            | … where x.age = ?1 order by x.lastname desc                  |
| Not               | findByLastnameNot                       | … where x.lastname <> ?1                                     |
| In                | findByAgeIn(Collection ages)            | … where x.age in ?1                                          |
| NotIn             | findByAgeNotIn(Collection age)          | … where x.age not in ?1                                      |
| TRUE              | findByActiveTrue()                      | … where x.active = true                                      |
| FALSE             | findByActiveFalse()                     | … where x.active = false                                     |
| IgnoreCase        | findByFirstnameIgnoreCase               | … where UPPER(x.firstame) = UPPER(?1)                        |
| top               | findTop100                              | top 10/where ROWNUM <=10                                     |

#### 3.8.2 编写dao文件

```java
// >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> UserDao.java <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
package com.test.springboot.jpa.dao;

import com.test.springboot.jpa.pojo.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;
import java.util.List;

@Repository
public interface UserDao extends JpaRepository<User, Long> {
    // ================================ 自定义简单查询 =================================
    /*
    主要语法：(……代表属性名)
        * findBy……
        * readBy……
        * queryBy……
        * countBy……
    其他关键字：（具体参考文档）
        And
        Or
        Is,Equals
        Between
        ……
     */

    /**
     * 根据密码查询记录
     * @param userPassword 用户密码
     * @return 用户列表
     */
    List<User> findByUserPassword(String userPassword);

    /**
     * 根据密码删除记录
     * @param userPassword 用户密码
     * @return 删除的数目
     */
    void deleteByUserPassword(String userPassword);

    /**
     * 根据密码统计记录
     * @param userPassword 用户密码
     * @return 统计数
     */
    long countByUserPassword(String userPassword);
}
```

#### 3.8.3 编写service 方法

```java
// >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> UserEasyService.java <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<

package com.test.springboot.jpa.service;

import com.test.springboot.jpa.pojo.User;
import java.util.List;

public interface UserEasyService {
    /**
     * 根据密码查询记录
     * @param userPassword 用户密码
     * @return 用户列表
     */
    List<User> findByUserPassword(String userPassword);

    /**
     * 根据密码删除记录
     * @param userPassword 用户密码
     * @return 删除的数目
     */
    void deleteByUserPassword(String userPassword);

    /**
     * 根据密码统计记录
     * @param userPassword 用户密码
     * @return 统计数
     */
    long countByUserPassword(String userPassword);
}
```

```java
// >>>>>>>>>>>>>>>>>>>>>>>>>>>>> UserEasyServiceImpl.java <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<

package com.test.springboot.jpa.service.impl;

import com.test.springboot.jpa.dao.UserDao;
import com.test.springboot.jpa.pojo.User;
import com.test.springboot.jpa.service.UserEasyService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import javax.transaction.Transactional;
import java.util.List;

@Service
public class UserEasyServiceImpl implements UserEasyService {
    @Autowired
    private UserDao userDao;

    @Override
    public List<User> findByUserPassword(String userPassword) {
        return userDao.findByUserPassword(userPassword);
    }

    @Override
    @Transactional
    public void  deleteByUserPassword(String userPassword) {
         userDao.deleteByUserPassword(userPassword);
    }

    @Override
    public long countByUserPassword(String userPassword) {
        return userDao.countByUserPassword(userPassword);
    }
}
```

#### 3.8.4 编写test方法

```java
// >>>>>>>>>>>>>>>>>>>>>>>>> UserEasyServiceImplTest <<<<<<<<<<<<<<<<<<<<<<<<<<<<

package com.test.springboot.jpa.service.impl;

import com.test.springboot.jpa.JpaApplicationTests;
import com.test.springboot.jpa.pojo.User;
import com.test.springboot.jpa.service.UserEasyService;
import lombok.extern.slf4j.Slf4j;
import org.junit.Test;
import org.springframework.beans.factory.annotation.Autowired;
import javax.transaction.Transactional;
import java.util.List;
import static org.junit.Assert.*;

@Slf4j
public class UserEasyServiceImplTest extends JpaApplicationTests {

    @Autowired
    private UserEasyService userEasyService;

    @Test
    public void findByUserPassword() {
        List<User> userList = userEasyService.findByUserPassword("NVOWM1");

        userList.forEach(user -> {
            log.info("Easy find user list by password ===>>> {}",user);
        });
    }

    @Test
    public void deleteByUserPassword() {
        String userPassword = "I2VYDC";
        userEasyService.deleteByUserPassword(userPassword);

        List<User> userList = userEasyService.findByUserPassword(userPassword);
    }

    @Test
    public void countByUserPassword() {
        String userPassword = "S4KJ72";
        long countUser = userEasyService.countByUserPassword(userPassword);

        log.info("count user by password ===>>> {}",countUser);
    }
}
```

### 3.9 自定义SQL查询

1. 使用自定义SQL查询用`@Query(sql)`即可，如果涉及更新、添加、删除（事务）操作需要再添加`@Modifying`注解。也可以根据需要添加`@Transactional`注解对事务的支持和、查询超时的设置等。

2. 之前在用`PageHelper`的时候，默认第一页是`1`，这样查询出来就是数据库第一页的内容。**But**，使用了JPA发现它的页码是从`0`开始的，也就是说0=第一页、1=第二页…

   这和PageHelper差别还是蛮大的，若：数据库一共两条记录，你查询`new PageRequest(1,3)`得到的`content`是`[]`

#### 3.9.1 编写dao文件

```java
// >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> UserDao.java <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<

package com.test.springboot.jpa.dao;

import com.test.springboot.jpa.pojo.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;
import java.util.List;

@Repository
public interface UserDao extends JpaRepository<User, Long> {
    // ================================= 自定义 SQL 查询 =================================

    /*
    使用自定义SQL查询用@Query(sql)即可
    如果涉及更新、添加、删除（事务）操作需要再添加@Modifying注解。
    也可以根据需要添加@Transactional注解对事务的支持和、查询超时的设置等。
     */

    /**
     * 根据用户的密码模糊查询：查询以0开头的文件
     * query 的参数，采用Java的entity 实体类
     * @param likePassword
     * @return
     */
    @Query("select u from User u where u.userName like ?1")
    List<User> findByLikePassword(String likePassword);
}
```

#### 3.9.2 编写service方法

```java
// >>>>>>>>>>>>>>>>>>>>>>>>>>>> UserComplexService.java <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<

package com.test.springboot.jpa.service;

import com.test.springboot.jpa.pojo.User;
import org.springframework.data.domain.Page;
import java.util.List;

public interface UserComplexService {

    /**
     * 自定义分页查询
     * @param pageCode 当前页数
     * @param pageSize 每一页显示记录数
     * @param sortName 排序的字段名
     * @return
     */
    Page<User> selectUserByPage(int pageCode, int pageSize, String sortName);

    /**
     * 通过
     * @param likePassword 模糊查询的密码
     * @return
     */
    List<User> selectUsersByLikePassword(String likePassword);
}
```

```java
// >>>>>>>>>>>>>>>>>>>>> UserComplexServiceImpl.java <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<

package com.test.springboot.jpa.service.impl;

import com.test.springboot.jpa.dao.UserDao;
import com.test.springboot.jpa.pojo.User;
import com.test.springboot.jpa.service.UserComplexService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;
import java.util.List;

@Slf4j
@Service
public class UserComplexServiceImpl implements UserComplexService {

    @Autowired
    private UserDao userDao;

    @Override
    public Page<User> selectUserByPage(int pageCode, int pageSize, String sortName) {
        Sort sort = new Sort(Sort.Direction.ASC, sortName);
        Pageable pageable = new PageRequest(pageCode, pageSize, sort);
        return userDao.findAll(pageable);
    }

    @Override
    public List<User> selectUsersByLikePassword(String likePassword) {
        return userDao.findByLikePassword(likePassword);
    }
}
```

#### 3.9.3 编写test方法

```java
// >>>>>>>>>>>>>>>>>>>>>>>>>> UserComplexServiceImplTest.java <<<<<<<<<<<<<<<<<<<<<<<<<<<

package com.test.springboot.jpa.service.impl;

import com.test.springboot.jpa.JpaApplicationTests;
import com.test.springboot.jpa.pojo.User;
import com.test.springboot.jpa.service.UserComplexService;
import lombok.extern.slf4j.Slf4j;
import org.junit.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import java.util.List;
import static org.junit.Assert.*;

@Slf4j
public class UserComplexServiceImplTest extends JpaApplicationTests {

    @Autowired
    private UserComplexService userComplexService;

    @Test
    public void selectUserByPage() {
        int pageCode = 0;
        int pageSize = 20;
        // 使用Entity 中的字段
        String sortName = "userId";

        Page<User> userPage = userComplexService.selectUserByPage(pageCode, pageSize, sortName);

        log.info("总记录数 ===>>> {}",userPage.getTotalElements());

        log.info("总页数 ===>>> {}",userPage.getTotalPages());

//        log.info("记录 ===>>> {}",userPage.getContent());

        userPage.forEach(user -> {
            log.info("记录 ===>>> {}",user);
        });
    }

    @Test
    public void selectUsersByLikePassword(){
        String likePassword = "0";
        List<User> userList = userComplexService.selectUsersByLikePassword(likePassword);
        userList.forEach(user -> log.info("select user by like password log ===>>> {}",user));
    }
}
```

### 3.10 多表查询

实在想吐槽，JPA这样对多表查询的支持太差了，**多表查询**就必须手动写SQL，然后放在`@Query`注解中，这样SQL真的很难看，而且需要额外遵循JPA的规范。

## 4 常见问题

### 4.1自定义简单查询，delete方法报错
> 问题描述

**问题代码**
```java
// ==== UserDao.java ====
    /**
     * 根据密码删除记录
     * @param userPassword 用户密码
     * @return 删除的数目
     */
    void deleteByUserPassword(String userPassword);

// ==== UserEasyServiceImpl.java ====
    @Override
    public void  deleteByUserPassword(String userPassword) {
         userDao.deleteByUserPassword(userPassword);
    }

// ==== UserEasyServiceImplTest.java ====
    @Test
    public void deleteByUserPassword() {
        String userPassword = "I2VYDC";
        userEasyService.deleteByUserPassword(userPassword);

//        List<User> userList = userEasyService.findByUserPassword(userPassword);
    }
```

**运行错误信息**
```bash
org.springframework.dao.InvalidDataAccessApiUsageException: No EntityManager with actual transaction available for current thread - cannot reliably process 'remove' call; nested exception is javax.persistence.TransactionRequiredException: No EntityManager with actual transaction available for current thread - cannot reliably process 'remove' call
```

> 问题分析

  除了query外的modiy和delete如果各层的方法中没有进行事务管理的话(没加@Transactional)话会报错

  在对应的Service上添加@Transactional注解即可。

> 参考网站

    1. [org.springframework.dao.InvalidDataAccessApiUsageException: No EntityManager with actual transaction_1](http://t.csdn.cn/C8n0M)
    2. [org.springframework.dao.InvalidDataAccessApiUsageException: No EntityManager with actual transaction_2](http://t.csdn.cn/kbMaE)



## 5. 联系地址

<img src="https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/DocsifyFile/gitee_16.svg"/> gitee：[gitee.com/salute83/springboot-jpa](https://gitee.com/salute83/springboot-jpa.git)

<img src="https://83-cloud-space.oss-cn-shenzhen.aliyuncs.com/File/DocsifyFile/doc_32.svg" alt="img" style="zoom:60%;" /> docsift：[docsify/83_start_docks/springbot-jpa](https://83start.github.io/83_start_docs/#/./Springboot/Springboot_Jpa)









































































