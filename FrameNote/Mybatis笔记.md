# 目录

[toc]

# Mybatis

# 1. 简介

* MyBatis 是一款优秀的**持久层框架**

* 它支持自定义 SQL、存储过程以及高级映射

* MyBatis免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作

* MyBatis可以通过简单的 **XML** 或**注解**来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

  

## 1.1 如何获得Mybatis

* Maven仓库

  ```xml
  <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.2</version>
  </dependency>
  
  ```

* Github:https://github.com/mybatis/mybatis-3/releases

  Github上Mybatis项目中的Release标签下找自己需要的版本。

* 文档：https://mybatis.org/mybatis-3/zh/index.html

  

## 1.2 持久化

* 持久化：把数据保存到可掉电式存储设备中以供之后使用。

* 持久化主要是将内存中的数据存储在**关系型数据库**中，也可以存储在**磁盘文件（I/O）**、**xml数据文件**中。



## 1.3 面向对象三层架构

**1. 数据持久层**

采用DAO模式，建立实体类和数据库表映射（ORM映射）。也就是哪个类对应哪个表，哪个属性对应哪个列。

持久层的目的是完成对象数据和关系数据（数据库）的转化。

**2.业务层**

采用事务脚本模式。将一个业务中的所有操作封装成一个方法，保证方法中的所有数据库操作全部成功或者全部失败。

**3.表现层（Presentation）**

采用**MVC模式**。

**M-Model**：表示应用程序核心（比如数据库记录列表），通常负责在数据库存取数据。

**V-View**：显示数据（数据库记录），应用程序中用于处理数据显示的部分。

**C-Controller**：（控制器）处理输入（写入数据库记录），应用程序中用于处理用户交互的部分。



# 2. 创建一个Mybatis程序



## 2.1 搭建环境（配置文件）

在pom.xml文件中配置**数据库驱动**、**Mybatis**和**Junit依赖**

```xml
<!--数据库驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>
    <!--mybatis-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.2</version>
    </dependency>
    <!--Junit-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
```

**maven资源没有导出问题（maven静态资源过滤问题）**：

* IDEA的maven项目中，默认源代码目录下（src/main/java目录）的xml等资源文件并不会在编译的时候一块打包进classes文件夹，而是直接舍弃掉

解决：将IDEA maven项目中**src源代码下的xml等资源文件**编译进classes文件夹

在父工程和子工程的pom配置文件中添加下面这段代码(**xml文件放在resources下和接口同名的包中，因为xml和接口的包名相同，编译后会放在classes下相同的文件夹内，这样xml才能通过namespace找到对应的接口文件**)，下面这段代码是把src/main/java和src/main/resources文件夹下的资源文件全部不编译进classes文件夹

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```





## 2.2 创建一个模块

1. **连接数据库（核心配置文件）**

   在resource文件夹下创建mybatis-config.xml文件

   **注意**：所有接口实现类（mapper.xml文件）都要在mybatis配置文件中的mappers标签中注册。**这里的全限定包名用/分隔**。

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
     PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
     "http://mybatis.org/dtd/mybatis-3-config.dtd">
   
   <configuration>
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"/>
               <dataSource type="POOLED">
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=false&amp;characterEncoding=UTF-8"/>
                   <property name="username" value="root"/>
                   <property name="password" value="3.1415926"/>
               </dataSource>
           </environment>
       </environments>
       <mappers>
       	<mapper resource="com/maowei/dao/UserMapper.xml"/>
     	</mappers>
   
   </configuration>
   ```

2. **编写工具类（生成sqlSessionFactory对象）**

   ```java
   package utils;
   
   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   
   import java.io.IOException;
   import java.io.InputStream;
   
   public class MybatisUtils {
   
       private static SqlSessionFactory sqlSessionFactory;
   
       static{
           try {
               //使用Mybatis第一步，获取SqlSessionFactory对象
               String resource = "mybatis-config.xml";
               InputStream inputStream = Resources.getResourceAsStream(resource);
               sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   
       public static SqlSession getSqlSession() {
           return sqlSessionFactory.openSession();
       }
   
   }
   
   ```



## 2.3 编写代码

* **实体类（pojo）**

  **实体类中的属性名最好和数据库表的列名相同**。

  方便后面代码的编写

  ```java
  //实体类
  public class User {
  
      private int id;
      private String name;
      private String pwd;
  
      public User() {
      }
  
      public User(int id, String name, String pwd) {
          this.id = id;
          this.name = name;
          this.pwd = pwd;
      }
  
      public int getId() {
          return id;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public String getPwd() {
          return pwd;
      }
  
      public void setPwd(String pwd) {
          this.pwd = pwd;
      }
  }
  ```

  

* **接口（dao）**

  ```java
  public interface UserDao {
  
      List<User> getUser();
  }
  
  ```

  

* **接口实现类(包含sql语句的xml文件)**

  mapper标签内的**namespace参数表示对应的dao接口**

  对应的sql操作的标签内的id对应dao接口中的方法，resultType参数对应返回的实体类型

  目的是将接口和xml联系起来

  **注意**：namespace和resultType都需要使用**全限定路径**。

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--绑定一个Dao/Mapper接口-->
  <mapper namespace="com.maowei.dao.UserDao">
      <select id="getUser" resultType="com.maowei.pojo.User">
      	select * from users;
    	</select>
  </mapper>
  ```



## 2.4 使用Junit测试

**注意**：核心配置文件（mybatis-config）中要注册mappers

即每个接口实现xml的完全限定名，这里包名用/分隔，不用点号

```xml
<mappers>
	<mapper resource="com/maowei/dao/UserMapper.xml"/>
</mappers>
```



# 3. CRUD

1. **编写接口**

2. **编写xml中对应的sql语句**

   * mapper标签中**namespace参数**：绑定的接口的**全限定名**

   * select，insert，update，delete语句的参数：

     id：接口中的方法名

     resultType：返回数据的类型（**也需要全限定名**）

     parameterType：接口方法的参数类型

     sql语句中需要使用方法传入的参数的地方用#{}，如果传入一个类，可以直接写类的属性名

     ```xml
     <mapper namespace="com.maowei.dao.UserMapper">
         
         <select id="getUserById" resultType="com.maowei.pojo.User" parameterType="int">
             select * from users where id=#{id};
         </select>
     
         <!--实体类中的属性可以直接通过属性名取出来（#{}花括号中的是属性名）-->
         <insert id="addUser" parameterType="com.maowei.pojo.User">
             insert into users(id,name,pwd) values(#{id},#{name},#{pwd})
         </insert>
     </mapper>
     ```

     

3. **测试**

   1. 获取SqlSession
   2. 获取接口
   3. 使用方法
   4. **增删改需要提交事务**，查询不用
   5. 关闭SqlSession

   ```java
   @Test
   public void deleteUserTest() {
       SqlSession sqlSession = MybatisUtils.getSqlSession(); // 获取SqlSession
       UserMapper userMapper = sqlSession.getMapper(UserMapper.class); //获取接口对象
       userMapper.deleteUser(5); //使用接口方法
       sqlSession.commit(); //增删改需要提交事务
       sqlSession.close(); //关闭SqlSession
   }
   ```



# 4. 定制化传参

当实体类参数非常多的时候，向接口传入Map<String,Object>可以实现传入**特定的参数**。



在xml配置中传入的参数类型，parameterType="map"

```xml
<insert id="addUser2" parameterType="map">
        insert into users(id,pwd) values(#{id},#{password})
    </insert>
```

在接口中声明变量。并且在测试代码中，创建一个包含想要传入的参数的HashMap

```java
int addUser2(Map<String,Object> map); //这里值类型为Object，可以接收任何类型的值

//单元测试代码如下
//加入了一个没有用户名的用户，实现了只传入几个特定的参数
@Test
    public void addUser2Test() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        Map<String,Object> map = new HashMap<String,Object>();
        map.put("id",9);
        map.put("password","123456");
        userMapper.addUser2(map);
        sqlSession.commit();
        sqlSession.close();
    }
```



# 5. 配置解析（XML配置）

## 5.1 核心配置文件

配置文档的顶层结构：

XML中的标签顺序需要按照下面的顺序。

<img src="E:\typora\笔记图片\Mybatis笔记\image-20200710101126155.png" alt="image-20200710101126155" style="zoom:67%;" />

## 5.2 环境配置(environments)

Mybatis可以配置成适应多个环境。

**不过要记住：尽管可以配置多个环境，但每个SqlSessionFactory实例只能选择一种环境。**

所以，如果你想连接两个数据库，就需要创建两个 SqlSessionFactory实例，每个数据库对应一个。而如果是三个数据库，就需要三个实例，依此类推，记起来很简单：

* **每个数据库对应一个 SqlSessionFactory 实例**

指定创建哪种环境，只要将环境名多为参数传递给SqlSessionFactoryBuilder即可。

```java
//可以接收环境配置的两个方法签名是
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, 		properties);

//如果忽略了环境参数，会加载默认的环境
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, properties);
```

* Mybatis默认的事务管理器是JDBC，默认使用连接池：POOLED



## 5.3 属性配置(properties)

配置文件中的property属性可以在外部文件中配置，并可以动态替换。

**在configuration标签中指定引用的properties文件**。

**外部properties文件：**

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=utf8
username=root
password=3.1415926
```

**xml配置文件**：

```xml
<!--properties元素体内的属性值优先级最低-->
<properties resource="db.properties">
    <!--resource也可以使用绝对路径，以url格式：com/maowei/*.properties-->
    <property name="username" value="xmw"/>
    <property name="password" value="333333"/>
</properties>

//设置好的属性可以在整个配置文件中用来替换需要动态配置的属性值。
//这里通过propertis中定义的键来引用对应的值，比如: 
<environment id="development">
    <transactionManager type="JDBC"/>
    <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
    </dataSource>
</environment>
```

**也可以在SqlSessionFactoryBuilder.buil( )方法中传入属性值，如：**

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, props);

// ... 或者 ...

SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, props);
```



如果在同一个属性在多个地方进行了配置，Mybatis按照下面的顺序来加载：

* 首先读取properties元素提内指定的属性
* 然年根据properties元素中的resource属性读取类路径下的属性文件，或根据url属性指定的路径读取属性文件，并覆盖之前读取过的属性
* 最后读取作为方法参数传递的属性，并覆盖之前读取过的属性

**总结：通过方法参数传递的属性值优先级最高，resource/url属性中指定的配置文件次之，最低优先级的则是properties元素中指定的属性**



## 5.4 类型别名(typeAliases)

* 类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在**降低冗余的全限定类名**书写。

* 在实体类比较多的情况下可以使用方式二，实体类比较少的情况下可以使用方式一。

**方式一**：自定义别名

```xml
<typeAliases>
  <typeAlias alias="User" type="com.maowei.User"/>
</typeAliases>
```



**方式二**：指定一个包名，Mybatis会在包名下搜索需要的Java Bean

```xml
<typeAliases>
  <package name="com.maowei.pojo"/>
</typeAliases>
```

每一个在指定包中的Java Bean，

* 在**实体类没有注解**的情况下**，使用Bean的首字母小写的非限定类名**来作为他的别名，如`com.maowei.User`的别名为`user`

* 如果**实体类有注解**，则别名是它的注解值，如

```java
@Alias("User")
public class User {
    
}
```



**特别的**：

基本数据类型的别名是`_int,_double,_float`，如果类型参数选择`int,double,float`则返回的是包装类型`Integer,Double,Float`

<img src="E:\typora\笔记图片\Mybatis笔记\image-20200710095633092.png" alt="image-20200710095633092" style="zoom:67%;" />

<img src="E:\typora\笔记图片\Mybatis笔记\image-20200710095653127.png" alt="image-20200710095653127" style="zoom:67%;" />

​          

## 5.5 设置(settings)

一些比较常用的设置，其他设置在官方文档中查阅。

![image-20200710100350460](E:\typora\笔记图片\Mybatis笔记\image-20200710100350460.png)

数据库中的列名一般用下划线分隔，如果数据库中的列名使用下划线分隔的，映射到实体类中用驼峰法命名的属性需要开启驼峰命名自动映射。

![image-20200710100418687](E:\typora\笔记图片\Mybatis笔记\image-20200710100418687.png)

指定日志实现方式：

![image-20200710100437925](E:\typora\笔记图片\Mybatis笔记\image-20200710100437925.png)

## 5.6 映射器(mappers)

MapperRegistry：注册绑定Mapper文件

SQL映射语句定义在XML文件中，需要告诉Mybatis去哪里找映射文件

**方式一**：【推荐使用】

资源引用（resource），路径为URL形式，**此时无法使用注解**

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
    <mapper resource="com/maowei/dao/UserMapper.xml"/>
</mappers>
```

方式一，Mapper映射文件和接口文件不同名，或者不在同一个包下都可以。



**方式二**：使用class文件绑定注册（使用注解）

**此时，既可以使用注解，也可以使用xml映射**（简单的sql可以直接使用注解，复杂的使用xml映射）

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="com.maowei.dao.UserMapper"/>
</mappers>
```

**注意**：

* 接口和它的Mapper映射文件（XML实现）必须同名
* 接口和配置文件必须在同一个包下



**方式三**：使用扫描包进行注入绑定

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="com.maowei.dao"/>
</mappers>
```

注意点和方式二相同，接口文件和对应的Mapper（XML实现）映射文件，**必须在同一个包下且同名**。



## 5.7 生命周期和作用域

理解不同作用域（Scope）和生命周期类别是至关重要的，因为错误的使用会导致非常严重的**并发问题**。

下面是程序的执行顺序：

<img src="E:\typora\笔记图片\Mybatis笔记\image-20200710110841252.png" alt="image-20200710110841252" style="zoom:90%;" />

不同对象的生命周期和作用域：

**SqlSessionFactoryBuilder**：目的是为了创建SqlSessionFactory

* 这个类可以被实例化、使用和丢弃，**一旦创建了 SqlSessionFactory，就不再需要它了**
* SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是**局部方法变量**）
* 可以重用 SqlSessionFactoryBuilder 来创建多个 SqlSessionFactory 实例，但最好还是不要一直保留着它



**SqlSessionFactory**：

* SqlSessionFactory 一旦被创建就应该**在应用的运行期间一直存在**，没有任何理由丢弃它或重新创建另一个实例
* 最佳实践是在应用运行期间不要重复创建多次
*  SqlSessionFactory 的最佳作用域是应用作用域。 有很多方法可以做到，最简单的就是使用**单例模式**或者**静态单例模式**。



**SqlSession**：

* 每个线程都应该有它自己的 SqlSession 实例
* SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的**最佳的作用域是请求或方法作用域**
* 绝对不能将 SqlSession 实例的引用放在一个类的静态域，甚至一个类的实例变量也不行。 也绝不能将 SqlSession 实例的引用放在任何类型的托管作用域中，比如 Servlet 框架中的 HttpSession
* 关闭操作很重要，为了确保每次都能执行关闭操作，你应该把这个关闭操作放到 finally 块中，`sqlSession.close()`

<img src="E:\typora\笔记图片\Mybatis笔记\image-20200710112025134.png" alt="image-20200710112025134" style="zoom:80%;" />

**其中每一个Mapper就是一个业务**



**映射器实例**：

映射器是一些绑定映射语句的**接口**。

映射器接口的实例是从 SqlSession 中获得的。

**方法作用域**才是映射器实例的最合适的作用域。 也就是说，映射器实例应该在调用它们的方法中被获取，使用完毕之后即可丢弃。

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class); //映射器实例
  // 你的应用逻辑代码
}
```



# 6. 解决属性名和字段名不一致的问题（结果映射）

**结果映射（resultMap）**：

`resultMap`可以让你从 90% 的 JDBC `ResultSets` 数据提取代码中解放出来，并在一些情形下允许你进行一些JDBC 不支持的操作。

* ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要**描述语句之间的关系**就行了。 

  

**非显式配置resultMap**：

简单的情况下，`resultMap`由Mybatis在幕后自动创建，再根据属性名来映射到Java Bean的属性上。如果**列名和属性名不能匹配上，可以在SELECT语句中设置别名**。

```xml
<!--最简单的情况，列名和属性名匹配-->
<select id="getUserById" resultType="com.maowei.pojo.User" parameterType="int" >
    select * from users where id=#{id};
</select>

<!--属性名和列名不匹配，最简单的处理方法是在SQL语句中使用别名,别名为属性名-->
<select id="getUserById" resultType="com.maowei.pojo.User">
	select 
    id,name,
    pwd as password
    from users where id=#{id}
</select>
```



**显式地配置resultMap**:

假设

数据库中的列名为：    id，name，pwd

使用对象中的属性为：id，name，password

显式地使用外部resultMap解决列名不匹配,**当显示地使用外部的resuleMap时，引用它的语句只需要resultMap属性就可以了，去掉了resultType属性**

其中，**已经相等的列名和属性名可以不用重新在resultMap中指出对应关系**。

```xml
<!--配置resultMap-->
<resultMap id="userResuleMap" type="com.maowei.pojo.User">
	<!--<result column="id" property="id"/>-->
    <!--<result column="name" property="name"/>-->
    <result column="pwd" property="password"/>
</resultMap>

<select id="getUserById" resultMap="userResuleMap">
	select * from users where id=#{id}
</select>
```



# 7. 日志

## 7.1 日志工厂

所有可以选择的日志

在mybatis-config.xml中settings标签下配置想要使用的日志。

![image-20200710151957244](E:\typora\笔记图片\Mybatis笔记\image-20200710151957244.png)

其中**LOG4J**和**STDOUT_LOGGING**比较常用。

**STDOUT_LOGGING**：标准的日志工厂

下图是使用STDOUT_LOGGING日志的一个例子。

<img src="E:\typora\笔记图片\Mybatis笔记\image-20200710153445346.png" alt="image-20200710153445346" style="zoom:80%;" />



## 7.2 Log4j的使用

什么是Log4j：

* Log4j是Apache的一个开源项目

* 可以控制日志信息输送的目的地是控制台、文件、GUI组件，甚至是套接口服务器、NT的事件记录器、UNIX Syslog守护进程等

* 可以控制每一条日志的输出格式

* 通过定义每一条日志信息的级别，能够更加细致地控制日志的生成过程

* 可以通过一个**配置文件**来灵活地进行配置，而不需要修改应用的代码

  

**1.导入Log4j包**

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```



**2.Log4j.properties**

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold = DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern = [%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File = ./log/maowei.log
log4j.appender.file.MaxFileSize = 10mb
log4j.appender.file.Threshold = DEBUG
log4j.appender.file.layout = org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern = [%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis = DEBUG
log4j.logger.java.sql = DEBUG
log4j.logger.java.sql.Statement = DEBUG
log4j.logger.java.sql.ResultSet = DEBUG
log4j.logger.java.sql.PreparedStatement = DEBUG
```



**3.在mybatis中配置Log4j为日志的实现**

在mybatis-config.xml中settings元素下配置日志为Log4j

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```



**4.Log4j测试运行的一个例子**

<img src="E:\typora\笔记图片\Mybatis笔记\image-20200710162914518.png" alt="image-20200710162914518" style="zoom:80%;" />



**5.简单使用**

1. 在要使用Log4j的类中导入包

   `import org.apache.log4j.Logger;`

   

2. 创建日志对象，参数为当前的class

   ```java
   static Logger logger = Logger.getLogger(UserMapperTest.class);
   ```

   

3. 日志级别

   ```java
   logger.info("info:进入log4jTest");
   logger.debug("debug:进入了log4jTEST");
   logger.error("error:进入了log4jTEST");
   ```




# 8. 分页

不一次性把数据库里的数据全部都出来，一部分一部分地把数据读出来，或者只读一部分数据。



## 8.1 使用LIMIT分页

**使用Limit的分页**：

```sql
SELECT * 
FROM users
LIMIT startIndex,pageSize;
LIMIT 0,2;
//从第0行开始，查询两行，即第0，1行
```



**使用Mybatis分页**：

1. 接口

   定义分页查询方法，**可以使用定制化传参**，传入方法传入Map，可以实现传入特定的参数

2. Mapper.xml

   编写带有LIMIT的SQL语句，使用LIMIT实现分页

   ```xml
   <select id="getUserLimit" parameterType="map" resultMap="UserMap">
       select * from users limit #{startIndex},#{pageSize};
   </select>
   ```

3. 测试



## 8.2 分页插件

<img src="E:\typora\笔记图片\Mybatis笔记\image-20200711172833019.png" alt="image-20200711172833019" style="zoom:50%;" />

了解即可，知道有这样一个东西。



# 9. 使用注解开发

## 9.1 面向接口编程

在开发中很多时候会选择面向接口编程。

**原因**：**解耦（最重要），可扩展，提高复用；分层开发中，上层不用管具体实现，大家都遵守共同的标准，使得开发变得容易，规范性好**



**关于接口的理解**：

* 接口从更深层次的理解，是定义（规范、约束）与实现（名实分离的原则）的分离
* 接口反映了系统设计人员对系统的抽象理解
* 接口应有两类：
  * 第一类是对一个个体的抽象，它可对应为一个抽象体（abstract class）
  * 第二类是对一个个体某方面的抽象，即形成一个抽象面（interface）
* 一个个体可能有多个抽象面。



## 9.2 使用注解开发

1. 注解在接口上实现

   ```java
   //获取全部用户
   @Select("select * from users")
   List<User> getAllUsers();
   ```



2. 在核心配置文件中绑定接口

   ```xml
   <mappers>
       <mapper class="com.maowei.dao.UserMapper"/>
   </mappers>
   ```

   这种绑定方法使用xml映射也可以，但是要注意映射文件和接口文件需要在同一个包下

   （见5.6节，映射器的配置）

   

3. 测试



本质：反射机制实现

底层：动态代理

![image-20200711192307051](E:\typora\笔记图片\Mybatis笔记\image-20200711192307051.png)



## 9.3 CRUD



### 9.3.1 设置自动提交事务

可以在工具类创建的时候实现自动提交事务，在openSession函数中设置autocommit为true即可（参考源码）

```java
public static SqlSession getSqlSession() {
    return sqlSessionFactory.openSession(true);
}
```



### 9.3.2 使用注解的CRUD

在接口中编写注解（sql语句）

```java
//通过id查找用户
@Select("select * from users where id=#{uid}")
User getUserById(@Param("uid") int id);

//插入用户
@Insert("insert into users(id,name,pwd) values(#{id},#{name,},#{password})")
int addUser(User user);


//更新用户数据
@Update("update users set pwd=#{password},name=#{name} where id=#{id}")
int updateUser(User user);

//删除用户
@Delete("delete from users where id=#{uid}")
int deleteUser(@Param("uid") int id);
```

**关于@Param( )注解：**

* 当方法参数是基本数据类型或者是String类型时，**每个参数前都要加上@Param( )注解**
* 当参数是引用类型时，不用加注解
* 如果只有一个基本数据类型时，可以不加，但是建议加上
* 在sql中#{uid}中引用的就是@Param("uid")中设定的属性名，而不是形参名



## 9.4 #{ } 和 ${ }的区别：

* #{ }是预编译处理，${ }是字符串替换
* MyBatis在处理#{ }时，会将SQL中的#{ }替换为?号，使用PreparedStatement的set方法来赋值；MyBatis在处理 ${ } 时，就是把${ } 替换成变量的值
* 使用 #{ }可以有效的防止SQL注入，提高系统安全性



# 10. Mybatis详细的执行流程

![image-20200711195941673](E:\typora\笔记图片\Mybatis笔记\image-20200711195941673.png)



# 11. 多对一处理(实体类中有复杂类型)

* 多个学生，对应一个老师
* 对于学生而言，多个学生**关联**一个老师（多对一）（**association**）
* 对于老师而言，一个老师**集合**多个学生（一对多）（**collection**）



## 11.1 测试环境搭建：

* 配置核心配置文件
* 新建实体类
* 新建Mapper接口
* 新建Mapper.xml文件
* 在核心配置文件中注册mapper.xml文件
* 测试（构建工具类）



实体类：

**学生：**

```java
public class Student {
    private int id;
    private String name;
    private Teacher teacher;
    }
//构造器，getter，setter和toString方法省略，下同
```



**老师：**

```javascript
public class Teacher {
    private int id;
    private String name;
    }
```



## 11.2 关联（association）

### 11.2.1 嵌套select查询

**通过执行另外一个SQL映射语句来加载期望的复杂类型**

**本质上是SQL子查询，通过查询一个表得到的值，再去查询另一个表**

```xml
<!--
1、查找出所有的student
2、然后通过查找出来的tid去查找teacher，子查询
-->
<select id="getStudentAndTeacher" resultMap="StudentTeacher">
    select * from student;
</select>
<resultMap id="StudentTeacher" type="Student">
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <!--复杂的属性，需要单独的处理，对象：association 集合：collection-->
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
</resultMap>

<select id="getTeacher" resultType="Teacher">
    select * from teacher where id=#{tid};
</select>
```



### 11.2.2 嵌套结果映射

**使用嵌套的结果映射来处理连接结果的重复子集。**

**本质上是SQL联结，把想要的结果一次性查出来，再映射**

```xml
<select id="getStudentAndTeacher2" resultMap="getStudent2">
    select student.ID as sid,student.NAME as sname,teacher.NAME as tname
    from student,teacher
    where student.tid=teacher.id;
</select>
<resultMap id="getStudent2" type="Student">
    <id property="id" column="sid"/>
    <id property="name" column="sname"/>
    <association property="teacher" javaType="Teacher">
        <id property="name" column="tname"/>
    </association>
</resultMap>
```



# 12. 一对多处理(实体类中有集合)

比如：一个老师拥有多个学生

对老师来说，就是一对多关系

## 12.1 搭建测试环境

步骤与11.1相同，**实体类稍有变化**

**学生：**

```java
public class Student {
    private int id;
    private String name;
    private int tid;
    }
```



**老师：**

```java
public class Teacher {
    private int id;
    private String name;
    //一个老师拥有多个学生
    private List<Student> students;
    }
```



## 12.2 集合（collection）

当映射属性是一个集合的时候，使用collection

这里的例子中，Teacher实体类中的students属性是一个`List<Student>`，所以这里需要使用collection

### 12.2.1 集合的嵌套结果映射

其中collection里的ofType元素是实体类中集合的类型参数，即泛型中的约束类型

```xml
<!--集合的嵌套结果映射-->
<select id="getTeacherById" resultMap="TeacherStudent">
    select student.id as sid,
           student.name as sname,
           teacher.id as tid,
           teacher.name as tname
    from student,teacher
    where student.tid=teacher.id and tid=#{tid};
</select>
<resultMap id="TeacherStudent" type="Teacher">
    <id property="id" column="tid"/>
    <result property="name" column="tname"/>
    <collection property="students" ofType="Student">
        <id property="id" column="sid"/>
        <result property="name" column="sname"/>
        <result property="tid" column="tid"/>
    </collection>
</resultMap>
```



### 12.2.2 集合的嵌套select查询

collection中**javaType表示实体类中属性的类型**，**ofType是集合（泛型）的类型参数**，column用来传递给子查询。

这里Teacher类中的students属性，是一个`List<Student>`，所以javaType="ArrayList"，ofType="Student"

```xml
<!--集合嵌套的子查询-->
<select id="getTeacherById2" resultMap="TeacherStudent2">
    select * from teacher where id=#{tid};
</select>
<resultMap id="TeacherStudent2" type="Teacher">
    <id property="id" column="id"/>
    <collection property="students" javaType="ArrayList" ofType="Student" select="getStudent" column="id"/>
</resultMap>
<select id="getStudent" resultType="Student">
    select * from student where id=#{id}
</select>
```



## 12.3 总结

1. 关联 - association，用来将结果映射到一个复杂类型【多对一】
2. 集合 - collection，将结果映射到一个复杂类型的集合【一对多】
3. **javaType和ofType的区别**
   * javaType：是想要映射的实体类中的**属性的类型**
   * ofType：是想要映射的实体类中的集合的**类型参数**

面试高频：

* Mysql引擎
* InnoDB底层
* 索引
* 索引优化



# 13. 动态SQL

什么是动态SQL：**动态SQL是根据不同的条件生成不同的SQL语句，实现SQL复用。**

Mybatis中的元素种类：

- if
- choose (when, otherwise)
- trim (where, set)
- foreach



## 13.1 搭建环境

数据库表单创建

```sql
CREATE TABLE blog (
    id varchar(50) NOT NULL COMMENT '博客id',
    title varchar(100) NOT NULL COMMENT '博客标题',
    author varchar(30) NOT NULL COMMENT '博客作者',
    create_time datetime NOT NULL COMMENT '博客创建时间',
    views INT(30) NOT NULL COMMENT '博客浏览量'
)ENGINE=INNODB DEFAULT CHARSET = utf8;
```



1. 导包
2. 编写核心配置文件
3. 编写实体类
4. 编写实体类对应的Mapper文件和Mapper.xml文件



## 13.2 IF

使用IF可以实现在不同的条件下使用不同的SQL

**其中test元素是条件**

**接口**：

```java
List<Blog> queryBlog(Map map);
```



**xml**：

```xml
<select id="queryBlog" parameterType="map" resultType="Blog">
    select * from blog where 1=1
    <if test="title != null">
        and title=#{title}
    </if>
    <if test="author != null">
        and author=#{author}
    </if>
</select>
```



## 13.3 choose、when、otherwise

从多个条件中**选择一个条件**，比如传入了多个参数，只取一个参数作为条件查询

使用Choose元素可以实现上述操作，和Java中的Switch语句相似（when相当于关键字case，otherwise相当于关键字default）

**注意：**使用Choose元素最终只会选择一个条件。从上到下when中的条件是否满足，如果一旦满足了一种情况，即使下面的也有条件满足，也不会采取下面的条件。**即先前面的when优先级更高**。

如果所有条件都不满足，则使用otherwise中的条件。



**接口**：

```java
List<Blog> queryBlogChoose(Map map);
```

**xml**：

where元素使用，见13.4

```xml
<select id="queryBlogChoose" parameterType="map" resultType="Blog">
    select * from blog
    <where>
        <choose>
            <when test="title != null">
                title = #{title}
            </when>
            <when test="author != null">
                author = #{author}
            </when>
            <otherwise>

            </otherwise>
        </choose>
    </where>
</select>
```



## 13.4 trim、where、set

* 当多个条件存在时，SQL语句拼接，and元素有可能出现在奇怪的地方（第一个条件不满足，而后面的条件满足），比如：

	```sql
SELECT * from blog where and author = #{}author
	```
	
	用**where元素**代替SQL中的where可以避免这种问题的发生：
	
	* where 元素只会在**子元素返回任何内容的情况下才插入 “WHERE” 子句**。而且，若子句的开头为 “AND” 或 “OR”，where 元素也会将它们去除。
	
	```xml
	<select id="findActiveBlogLike" resultType="Blog">
	  SELECT * FROM BLOG
	  <where>
	    <if test="state != null">
	         state = #{state}
	    </if>
	    <if test="title != null">
	        AND title like #{title}
	    </if>
	    <if test="author != null and author.name != null">
	        AND author_name like #{author.name}
	    </if>
	  </where>
	</select>
	```



* set元素可以用于动态包含需要更新的列，忽略其他不需要更新的列

  **set 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号**（这些逗号是在使用条件语句给列赋值时引入的,**实际上是最后一句句尾的逗号**）

  ```xml
  <update id="updateBlog" parameterType="map">
      update blog
      <set>
          <if test="title != null">title=#{title},</if>
          <if test="author != null">author=#{author},</if>
          <if test="views != null">views=#{views},</if>
      </set>
      where id=#{id}
  </update>
  ```



* 也可以使用**trim元素来自定义想要的where和set元素的功能**

  prefix：前缀

  prefixOverrides：前缀覆盖

  suffix：后缀

  suffixOverrides：后缀覆盖

  使用trim元素，会**移除**所有Overrides（前缀后缀）里的指定内容，并插入prefix和suffix中指定的内容。

  ```xml
  <trim prefix="" prefixOverrides="" suffix="" suffixOverrides="">
  	...
  </trim>
  ```

  

  **与where等价的自定义trim元素：**

  ```xml
  <trim prefix="WHERE" prefixOverrides="AND |OR">
  	...
  </trim>
  ```

  

  **与set元素等价的自定义trim元素：**

  移除了后缀中的逗号，前缀设置为SET

  ```xml
  <trim prefix="SET" suffixOverrides=",">
  	...
  </trim>
  ```

  

## 13.5 SQL片段

将某个功能的SQL语句抽取出来，方便复用

1. 使用SQL标签抽取公共部分

   ```xml
   <sql id="if-title-author">
       <if test="title != null">
           and title=#{title}
       </if>
       <if test="author != null">
           and author=#{author}
       </if>
   </sql>
   ```

   

2. 在需要使用的地方，用include标签引用即可

   ```xml
   <select id="queryBlog" parameterType="map" resultType="Blog">
       select * from blog
       <where>
           <include refid="if-title-author"></include>
       </where>
   </select>
   ```

   

**注意点：**

* 最好基于单表来定义SQL片段
* SQL片段中不要存在WHERE或者SET标签，只存放判断部分，标签放在具体的SQL语句中。



## 13.6 foreach

动态 SQL 的另一个常见使用场景是**对集合进行遍历（尤其是在构建 IN 条件语句的时候）**。

foreach允许指定一个集合，声明可以在元素体内使用的**集合项（item）**和**索引（index）**变量。它也允许你指定**开头与结尾的字符串以及集合项迭代之间的分隔符**。

**实际上foreach是遍历集合中的元素，并将他们拼接成SQL语句。**

比如：查询特定几个id的blog（**传入一个集合**）。

**接口：**

```java
List<Blog> queryBlogForEach(Map map);
//其中map存入的是集合，map中的键就是foreach中collection元素的值
```



**xml：**

```xml
<select id="queryBlogForEach" parameterType="map" resultType="Blog">
    select * from blog
    <where>
        <foreach collection="ids" item="id" open="(" close=")" separator="OR">
            id=#{id}
        </foreach>
    </where>
</select>
```



**测试类：**

```java
@Test
public void queryBlogForEachTest() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper blogMapper = sqlSession.getMapper(BlogMapper.class);
    Map map = new HashMap();
    List<String> ids = new ArrayList<String>();
    ids.add("2fd85f1361e34f5eac526f7d0eb44c24");
    ids.add("ba0ff73b090744e88de6200f0b35d949");

    map.put("ids",ids);
    List<Blog> blogs = blogMapper.queryBlogForEach(map);

    for (Blog blog : blogs) {
        System.out.println(blog);
    }

    sqlSession.close();
}
```

从日志中可以看到最后拼接出的SQL语句，以`(`开头，`)`结尾，`OR`为分隔符

![image-20200717151404215](E:\typora\笔记图片\Mybatis笔记\image-20200717151404215.png)



### 13.7 总结

动态SQL实际就是在拼接SQL语句，只要保证SQL的正确性，按照SQL的格式，去组合即可

建议：

* 先在MySQL中写出完整的SQL语句，保证正确性后，再对应的修改成动态SQL实现通用即可。



# 14. 缓存

1. 什么是缓存[Cache]?
   * 存在内存中的临时数据
   * 将经常查询的数据放在缓存中，用户去查询数据就不用从磁盘上（关系型数据库数据文件）查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能。
2. 为什么使用缓存？
   * 减少和数据库交互的次数，减少系统开销，提高系统效率
3. 什么样的数据可以使用缓存？
   * 经常查询且不经常改变的数据
   

## 14.1 Mybatis缓存
* MyBatis 内置了一个强大的事务性查询缓存机制，它可以非常方便地配置和定制，缓存可以极大提升查询的效率
* Mybatis系统中默认定义了两种缓存：**一级缓存**和**二级缓存**
	* 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称本地缓存）
	
	* 二级缓存需要手动开启和配置，他是基于namespace级别的缓存
	
	* 为了提高扩展性，Mybatis定义了**缓存接口Cache**，可以通过实现Cache接口来自定义二级缓存。
	
	  


## 14.2 一级缓存
* 一级缓存也叫本地缓存，默认开启
   	* 与数据库同一次会话期间查询到的数据会放在本地缓存中
    * 以后如果需要获取相同的数据，直接从缓存中拿，不用再去查询数据库

  - 映射语句文件中的所有 select 语句的结果将会被缓存。
  - 映射语句文件中的**所有 insert、update 和 delete 语句会刷新缓存**。
  - 缓存会使用最近最少使用**算法（LRU, Least Recently Used）算法来清除不需要的缓存**。
  - 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
  - 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
  - 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而**不干扰其他调用者或线程所做的潜在修改**
  - 也可以手动清空一级缓存`sqlSession.clearCache( )`



## 14.2 二级缓存

* 二级缓存也叫全局缓存，一级缓存的作用域比较低，所以诞生了二级缓存

* **基于namespace级别的缓存**，一个命名空间，对应一个二级缓存，即Mapper.xml中的，一个namespace中的所有SQL语句都启用了缓存
	
	```xml
	<mapper namespace="com.maowei.dao.UserMapper">
	    <!--整个namespace中的SQL都启用缓存，即这个接口的方法都启用缓存-->
	</mapper>
	```
	
* 工作机制：

  * 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
  * 如果当前会话关闭了，这个会话对应的一级缓存就没了；但我们想要的是，**会话关闭了，一级缓存中的数据被保存到二级缓存中**
  * **新的会话查询信息，就可以从二级缓存中获取内容**
  * 不同的mapper查出的数据会放在自己对应的的缓存（map）中



步骤：

1. 在核心配置文件中开启全局缓存(虽然全局缓存默认是开启的，但是显示地打开增强了可读性)

   ```xml
   <settings>
       <setting name="cacheEnabled" value="true"/>
   </settings>
   ```

   ![image-20200719142645898](E:\typora\笔记图片\Mybatis笔记\image-20200719142645898.png)

2. 在想要使用二级缓存的SQL映射文件（Mpper.xml）中添加cache标签

   ```xml
   <!--开启二级缓存-->
   <cache/>
   
   <!--也可以自定义缓存参数-->
   <cache
          eviction="FIFO"
          flushInterval="60000"
          size="512"
          readOnly="true"/>
   ```

3. 需要将实体类实体化，不然可能会报错

   ```
   Caused by: java.io.NoSeriealizableExeption：com.maowei.User
   ```



**总结：**

* 只要开启了二级缓存，在同一个Mapper下就有效
* 所有的数据会先放在一级缓存中
* 只有当会话提交（`sqlSession.commit( )`），或者关闭的时候（`sqlSession.close()`），才会提交到二级缓存中



### 14.3 缓存原理

执行SQL时：

1. 先看二级缓存里有没有想要的数据
2. 如果没有，再看一级缓存里有没有
3. 如果一级缓存里也没有，查询数据库，再把数据放在一级缓存中
4. 如果会话（sqlSession）关闭或者提交，则把一级缓存的数据存到二级缓存中

![image-20200719145132777](E:\typora\笔记图片\Mybatis笔记\image-20200719145132777.png)



## 14.3 自定义缓存ehcache

Ehcache是一种广泛使用的开源Java分布式缓存。主要面向通用缓存

使用ehcache前，先导包

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.2.1</version>
</dependency>
```

一般不会在Mybatis'中使用自定义缓存。

目前使用使用Redis做缓存处理。



# 15. Mybatis Generator

Mybatis Generator执行指令：

**mvn mybatis-generator:generate**

在pom文件中配置mybatis generator插件的时候，做额外的配置，每次生成Mapper.xml的时候重写原来的文件（默认是追加到原来的xml文件中）。

如果要生成新的mapper接口，mapper.xml，pojo，可以每次只保留一条想要生成的表的table标签，避免重写已经生成的表。



```xml
<table tableName="mall_order_item" domainObjectName="OrderItem" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>
```



MybatisGenerator配置文件示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!-- windows下路径, D:\downloads\xxx.jar -->
    <classPathEntry location="H:/java_project/mysql-connector-java-5.1.6.jar" />

    <context id="DB2Tables" targetRuntime="MyBatis3">

        <!-- 不再追加xml内容 -->
        <plugin type="org.mybatis.generator.plugins.UnmergeableXmlMappersPlugin" />

        <commentGenerator>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://127.0.0.1:3306/mall?characterEncoding=utf-8&amp;serverTimezone=UTC"
                        userId="root"
                        password="3.1415926">
        </jdbcConnection>

        <javaTypeResolver >
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>

		<!-- 生成实体类 -->
        <javaModelGenerator targetPackage="com.maowei.mall.pojo" targetProject="src/main/java">
            <property name="enableSubPackages" value="true" />
<!--            <property name="trimStrings" value="true" />-->
        </javaModelGenerator>

		<!-- 生成SQL map的XML文件生成器-->
        <sqlMapGenerator targetPackage="mappers"  targetProject="src/main/resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>

		<!-- 生成dao接口，即Mapper接口 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.maowei.mall.dao"  targetProject="src/main/java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>

        <!--        <table tableName="mall_order" domainObjectName="Order" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>-->
        <!--        <table tableName="mall_order_item" domainObjectName="OrderItem" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>-->
        <!--        <table tableName="mall_user" domainObjectName="User" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>-->
        <!--        <table tableName="mall_category" domainObjectName="Category" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>-->
        <!--        <table tableName="mall_product" domainObjectName="Product" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false">-->
        <!--            <columnOverride column="detail" jdbcType="VARCHAR" />-->
        <!--            <columnOverride column="sub_images" jdbcType="VARCHAR" />-->
        <!--        </table>-->
<!--        <table tableName="mall_order" domainObjectName="Order" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>-->
<!--        <table tableName="mall_order_item" domainObjectName="OrderItem" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>-->
<!--        <table tableName="mall_pay_info" domainObjectName="PayInfo" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>-->
<!--        <table tableName="mall_user" domainObjectName="User" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>-->
<!--        <table tableName="mall_category" domainObjectName="Category" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>-->
<!--        <table tableName="mall_product" domainObjectName="Product" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false">-->
<!--            <columnOverride column="detail" jdbcType="VARCHAR"/>-->
<!--            <columnOverride column="sub_images" jdbcType="VARCHAR"/>-->
<!--        </table>-->
            <table tableName="mall_shipping" domainObjectName="Shipping" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>


    </context>
</generatorConfiguration>
```



**pom.xml配置文件中，将generator更改成重写而不是追加。**

```xml
<plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
    <version>1.3.7</version>

    <!-- 使用配置，每次运行generator的时候重写mapper.xml中的内容，而不是追加 -->
    <configuration>
        <overwrite>true</overwrite>
    </configuration>
</plugin>
```



# 16. 查询结果映射实体类细节
Mybatis查询结果映射到实体类时，Mybatis通过反射动态生成实体类，反射中 `Class.forName("className").newInstance()` 调用的是实体类的默认构造函数（即无参构造函数），如果没有默认构造函数，就会抛出一个异常。

如果类中没有显式定义其他构造函数，虚拟机会自动提供默认构造方法（无参构造器），如果显式定义了其他有参数的构造方法，虚拟机就不再为其提供默认的构造方法。

所以Mybatis查询结果映射到实体类时，如果有其他带有参数的构造方法，就必须显式地定义一个无参的构造方法，否则Mybatis通过反射生成实例的时候会抛出异常。



# 17. insert方法中返回自增的Id

有的时候需要拿到插入对象的自增主键Id，这个时候需要在Mapper.xml文件中配置，这样就不需要再重新去数据库中查询一次了
如：
* Mapper接口中的方法:
	`int insertSelective(Shipping record);`
* Mapper.xml文件中的配置：
	`<insert id="insertSelective" parameterType="com.maowei.mall.pojo.Shipping" useGeneratedKeys="true" keyProperty="id">`
	配置useGeneratedKeys参数和**keyProperty**参数，在插入操作完成后，将数据库中的keyProperty设置的字段值，设置到传入的对象中，即接口方法传入的参数中,上面的操作就是把自增的id设置到传入的Shipping对象中.

