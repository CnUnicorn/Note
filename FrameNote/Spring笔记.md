[toc]



# 1. Spring简介

下载地址：

Github：https://github.com/spring-projects/spring-framework/releases



maven依赖：

导入springmvc包，会自动把其他相关的包也一起导进来，所以导springmvc是最方便的，否则需要一个一个把所有的包都写在依赖里面。

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>

```



## 1.1 优点

* Spring是一个开源的免费框架（容易）
* Spring是一个轻量级、非入侵的框架
* **控制反转（IOC）**，**面向切面编程（AOP）**
* 支持事务的处理，对框架整合的支持

总结：**Spring是一个轻量级的控制反转（IOC）和面向切面编程（AOP）的框架。**



## 1.2 组成

![image-20200720141226129](E:\typora\笔记图片\Spring笔记\image-20200720141226129.png)

Spring 框架是一个分层架构，由 7 个定义良好的模块组成。Spring 模块构建在核心容器之上，核心容器定义了创建、配置和管理 bean 的方式 .

<img src="E:\typora\笔记图片\Spring笔记\1219227-20170930225010356-45057485.gif" alt="1219227-20170930225010356-45057485" style="zoom:150%;" />

组成 Spring 框架的每个模块（或组件）都可以单独存在，或者与其他一个或多个模块联合实现。每个模块的功能如下：

- **核心容器（Spring Core）**：核心容器提供 Spring 框架的基本功能。核心容器的主要组件是BeanFactory，它是工厂模式的实现。BeanFactory 使用*控制反转*（IOC） 模式将应用程序的配置和依赖性规范与实际的应用程序代码分开。

- **Spring 上下文（Spring Context）**：Spring 上下文是一个配置文件，向 Spring 框架提供上下文信息。Spring 上下文包括企业服务，例如 JNDI、EJB、电子邮件、国际化、校验和调度功能。

- **Spring AOP**：通过配置管理特性，Spring AOP 模块直接将面向切面的编程功能 , 集成到了 Spring 框架中。所以，可以很容易地使 Spring 框架管理任何支持 AOP的对象。Spring AOP 模块为基于 Spring 的应用程序中的对象提供了事务管理服务。通过使用 Spring AOP，不用依赖组件，就可以将声明性事务管理集成到应用程序中。

- **Spring DAO**：JDBC DAO 抽象层提供了有意义的异常层次结构，可用该结构来管理异常处理和不同数据库供应商抛出的错误消息。异常层次结构简化了错误处理，并且极大地降低了需要编写的异常代码数量（例如打开和关闭连接）。Spring DAO 的面向 JDBC 的异常遵从通用的 DAO 异常层次结构。

- **Spring ORM**：Spring 框架插入了若干个 ORM 框架，从而提供了 ORM 的对象关系工具，其中包括 JDO、Hibernate 和 iBatis SQL Map。所有这些都遵从 Spring 的通用事务和 DAO 异常层次结构。

- **Spring Web 模块**：Web 上下文模块建立在应用程序上下文模块之上，为基于 Web 的应用程序提供了上下文。所以，Spring 框架支持与 Jakarta Struts 的集成。Web 模块还简化了处理多部分请求以及将请求参数绑定到域对象的工作。

- **Spring MVC 框架**：MVC 框架是一个全功能的构建 Web 应用程序的 MVC 实现。通过策略接口，MVC 框架变成为高度可配置的，MVC 容纳了大量视图技术，其中包括 JSP、Velocity、Tiles、iText 和 POI。

  

## 1.3 拓展

![image-20200720095442132](E:\typora\笔记图片\Spring笔记\image-20200720095442132.png)

* Spring Boot
  * 一个快速开发的脚手架
  * Spring Boot专注于快速、方便集成的**单个微服务个体**
  * 约定大于配置
* Spring Cloud
  * Spring Cloud基于Sprng Boot实现
  * Spring Cloud关注**全局的服务治理框架**
* Spring Boot使用了约束优于配置的理念，很多集成方案已经帮你选择好了，能不配置就不配置 , Spring Cloud很大的一部分是基于Spring Boot来实现，Spring Boot可以离开Spring Cloud独立使用开发项目，但是Spring Cloud离不开Spring Boot，属于依赖的关系



弊端：Spring发展太久之后，Spring的配置十分繁琐。



# 2. IoC理论推导

之前的业务：

1. UserDao接口
2. UserDaoImpl实现类
3. UserService业务接口
4. UserServiceImpl业务实现类



在之前的业务中，用户的需求可能会影响原来的代码，需要根据用户的需求去求改原来的代码

如果代码量很大，修改一次的成本会非常昂贵。



在UserServiceImpl中，使用一个set方法实现动态值的注入

```java
private UserDao userDao;

//利用set对象对象实现动态值的注入,注入实现同一个接口的不同的实现类，从而调用不同的getUser方法
pubic void setUserDao(UserDao userDao) {
    this.UserDao = userDao;
}

public void getUser() {
    userDao.getUser();
}
```

* 之前，程序是主动创建对象，控制权在程序员手上
* 使用set注入之后，程序不再具有主动性，而是变成了被动接收对象

这种思想从本质上解决了问题，程序员不用再去管理对象创建了。降低了耦合性， 可以更加专注在业务的是线上。这就是IOC的原型。

附相同的解释：https://www.zhihu.com/collection/544926149



## 2.1 IoC的本质

**控制反转IoC(Inversion of Control)**，是一种**设计思想**，**DI(依赖注入)是实现IoC的一种方法**。没有IoC的程序中 , 我们使用面向对象编程 , 对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，个人认为所谓控制反转就是：获得依赖对象的方式反转了。

<img src="E:\typora\笔记图片\Spring笔记\image-20200720142128442.png" alt="image-20200720142128442" style="zoom:90%;" />

**IoC是Spring框架的核心内容**，使用多种方式完美的实现了IoC，可以使用XML配置，也可以使用注解，新版本的Spring也可以零配置实现IoC。

Spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入容器中，程序使用时再从Ioc容器中取出需要的对象。

<img src="E:\typora\笔记图片\Spring笔记\image-20200720142248167.png" alt="image-20200720142248167" style="zoom:70%;" />

总结：**控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection,DI）**



# 3. HelloSpring

新建一个简单的Spring程序

1. 导入jar包

   在Maven中配置Spring依赖

   注 : spring 需要导入commons-logging进行日志记录 。利用maven , 他会自动下载对应的依赖项 .

   ```xml
   <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-webmvc</artifactId>
       <version>5.2.0.RELEASE</version>
   </dependency>
   ```

2. 编写代码

   先编写一个实体类：

   ```java
   public class Hello {
   
       private String name;
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       @Override
       public String toString() {
           return "Hello{" +
                   "str='" + name + '\'' +
                   '}';
       }
   }
   ```

   然后编写Spring配置文件（这里使用基于XML的元数据配置文件，XML-based configuration metadata）

   xml配置文件的基本结构可以在官网文档找到。

   这里的Bean就是java对象，由Spring创建管理

   其中：

   * **id就对象的名称（相当于自定义变量名），**
   * **class是对应实体类的全路径名，**
   * **property元素是类中的属性名，value是一个具体的值，原始数据类型**
   * **当属性是一个复杂类的时候，可以使用ref元素，引用当前容器中已经定义的Bean来进行赋值**

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <!--使用Spring来创建对象，在Spring中，这些都成为Bean-->
       <bean id="hello" class="com.maowei.Hello">
           <property name="name" value="Hello Spring!"/>
       </bean>
       
       <bean id="hello2" class="com.maowei.Hello2">
           
       <bean id="oracleImpl" class="com.maowei.dao.UserDaoOracleImpl"/>
       <bean id="userServiceImpl" class="com.maowei.service.UserServiceImpl">
           <property name="userDao" ref="oracleImpl"/>
       </bean> 
           
       </bean>
   </beans>
   ```

3. 测试

   ```java
   @Test
   public void Test() {
       ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
       Hello hello = (Hello) context.getBean("hello");
       Helli hello = context.getBean("hello",Hello.class);
       //第二个参数是class类，通过反射，就不用强转了
      
    System.out.println(hello.toString());
   }
   ```
   
   
* Hello对象由Spring创建
* Hello对象的属性值由Spring容器设置
  
   这个过程就叫控制反转（IoC）

   * 控制：传统应用程序，对象创建由程序本身控制；使用Spring之后，对象创建是由Spring完成的
* 反转：程序本身不创建对象，而变成被动地接收对象（**在程序中，使用ApplicationContext.getBean()来获取Spring创建的对象**）
  

注意点：**ApplicationContext.getBean()返回的是一个Object对象，所以需要强制转换成自己想要的对象；或者通过getBean的反射参数，就不用强转了**

   

   现在，彻底不用改动java程序中的代码了 , 要实现不同的操作 , 只需要在xml配置文件中进行修改 , 所谓的IoC,一句话搞定 : **对象由Spring 来创建 , 管理 , 装配**



# 4. IoC创建对象的方式

1. 如果**没有其他构造器**，默认使用**无参构造器创建对象**

   通过property元素，直接给指定的属性赋值

   ```xml
   <bean id="user" class="com.maowei.pojo.User">
       <property name="name" value="xmw"/>
   </bean>
   ```

   

2. 如果**有使用带参数的构造器**，创建对象的方式有以下几种

   1. 按下标给属性赋值

      下标=0，表示构造器的第一个参数

      ```xml
      <!--使用下标匹配属性名，构造对象-->
      <bean id="user" class="com.maowei.pojo.User">
          <constructor-arg index="0" value="xmw"/>
      </bean>
      ```

   2. 按类型匹配

      这种方式适合一个类**有多个只含一个参数的构造器**，**通过参数的类型来区别构造器的重载**

      ```xml
      <!--使用类型匹配来构造对象-->
      <bean id="user" class="com.maowei.pojo.User">
       <constructor-arg type="java.lang.String" value="xmw"/>
      </bean>
      ```

    3. 按参数名匹配
   
       这种方式比较常用，好理解
   
       ```xml
       <!--使用参数名来构造对象-->
       <bean id="user" class="com.maowei.pojo.User">
           <constructor-arg name="name" value="xmw"/>
       </bean>
       ```


总结：**在配置文件加载的时候，容器（ApplicationContext）中管理的对象就已经初始化了**，当从容器中取出多个名称相同的Bean时，是引用同一个Bean。



# 5. spring配置



## 5.1 别名

```xml
<!--使用别名，可以通过别名从容器中取出这个Bean(对象)-->
<alias name="user" alias="user2"/>
```



## 5.2 Bean的配置

```xml
<!--
id：Bean的唯一标识符，相当于变量名
class：Bean对应的类型的全限定名，包名+类名
name：别名，相比alias标签更强大，可以取多个别名，可以通过空格，逗号，分号分隔
-->
<bean id="user" class="com.maowei.pojo.User" name="user2 user3,user4;user5">
    <constructor-arg name="name" value="xmw"/>
</bean>
```



## 5.3 import

一般用于团队开发，将多个配置文件，导入合并成一个

假设现在有多个人开发，每个人负责不同的类开发，不同的类需要注册到不同的bean中，最后可以使用import标签，将所有人的配置文件合并为一个总的配置文件，使用时使用总的配置文件即可，如：

* 张三，beans.xml

* 李四，beans2.xml

* 王五，beans3.xml

* 总配置文件，applicationContext.xml

  ```xml
  <import resource="beans.xml"/>
  <import resource="beans2.xml"/>
  <import resource="beans3.xml"/>
  ```



# 6. 依赖注入（DI，Dependency Injection）



## 6.1 基于构造器的注入

基于构造器的注入（**Constructor-based DI**）

**在有多个构造器的情况下，需要构造器注入**，使用`<constructor-arg />`标签。

前面已经讲过了，通过**下标，类型匹配，参数名匹配**创建对象

具体见 **4. IoC创建对象的方式**



## 6.2 基于setter的注入【重点】

基于setter的注入（**setter-based DI**）

**除了默认无参构造器外，没有定义其他构造器时，使用Set方式注入。**

**在无参构造器的情况下，容器通过调用setter方法来初始化Bean（对象），这种方式就是基于setter的注入,所以这种方式下，实体类中必须有setter方法**

* 依赖注入：本质是Set注入，如果类中没有setter方法，会报错
  * 依赖：Bean对象的创建依赖于容器
  * 注入：Bean对象中的所有属性，由容器注入 



环境搭建：

1. 复杂对象

   ```java
   package com.maowei.pojo;
   
   public class Address {
   
       private String address;
   }
   //为了简洁，这里省去了所有的get，set和toString方法，只保留了属性
   //实际操作下一定不能忘掉这些方法
   ```

   

2. 真实测试对象

   ```java
   package com.maowei.pojo;
   
   import java.util.*;
   
   public class Student {
   
       private String name;
       private Address address;
       private String[] books;
       private List<String> hobbies;
       private Map<String,String> card;
       private Set<String> games;
       private String couple;
       private Properties info;
   }
   //为了简洁，这里省去了所有的get，set和toString方法，只保留了属性
   //实际操作下一定不要忘记这些方法
   ```

   

3. applicationContext.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <bean id="address" class="com.maowei.pojo.Address">
           <property name="address" value="宁波"/>
       </bean>
   
       <bean id="student" class="com.maowei.pojo.Student">
   
           <!--第一种，普通值注入，value-->
           <property name="name" value="xmw"/>
   
           <!--第二种，内联Bean(Inner Bean)注入,ref-->
           <property name="address" ref="address"/>
   
           <!--数组-->
           <property name="books">
               <array>
                   <value>西游记</value>
                   <value>红楼梦</value>
                   <value>水浒传</value>
                   <value>三国演义</value>
               </array>
           </property>
   
           <!--List-->
           <property name="hobbies">
               <list>
                   <value>唱歌</value>
                   <value>游泳</value>
                   <value>看书</value>
               </list>
           </property>
   
           <!--map-->
           <property name="card">
               <map>
                   <entry key="身份证" value="12345678"/>
                   <entry key="银行卡" value="587954856"/>
               </map>
           </property>
   
           <!--set-->
           <property name="games">
               <set>
                   <value>LOL</value>
                   <value>DNF</value>
                   <value>三国志</value>
               </set>
           </property>
   
           <!--null-->
           <property name="couple">
               <null/>
           </property>
   
           <!--properties-->
           <property name="info">
               <props>
                   <prop key="driver">驱动</prop>
                   <prop key="url">数据库表</prop>
                   <prop key="username">root</prop>
                   <prop key="password">3.1415926</prop>
               </props>
           </property>
       </bean>
   
   
   </beans>
   ```

   

4. 测试类

   ```java
   @Test
   public void test() {
       ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
       Student student = (Student) context.getBean("student");
       System.out.println(student.toString());
   }
   ```

   

## 6.3 拓展方式注入

p命名空间和c命名空间都不能直接使用，**需要导入xml约束**

```xml
xmlns:p="http://www.springframeworkc.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```



* XML Shortcut with the p-namespace（p命名空间）

  * 和基于setter的注入一样。使用p命名空间方法注入，可以省去`<property/>`标签，直接使用属性

    ```xml
    <bean id="userp" class="com.maowei.pojo.User" p:name="xmw" p:age="18"/>
    ```

    

* XML Shortcut with the c-namespace（c命名空间)

  * 和基于构造的注入一样。使用c命名空间，省去了`<construct-arg/>`标签

    ```xml
    <bean id="userc" class="com.maowei.pojo.User" c:age="18" c:name="xmw"/>
    ```



测试：

```java
public void test2() {
        ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");

        User userp = context.getBean("userp",User.class);
        System.out.println(userp.getName());

        User userc = context.getBean("userc",User.class);
        System.out.println(userc.getName());
    }
```



## 6.4 Bean的作用域（Bean Scope）

| Scope                                                        | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [singleton](https://docs.spring.io/spring/docs/5.2.0.RELEASE/spring-framework-reference/core.html#beans-factory-scopes-singleton) | (Default) Scopes a single bean definition to a single object instance for each Spring IoC container. |
| [prototype](https://docs.spring.io/spring/docs/5.2.0.RELEASE/spring-framework-reference/core.html#beans-factory-scopes-prototype) | Scopes a single bean definition to any number of object instances. |
| [request](https://docs.spring.io/spring/docs/5.2.0.RELEASE/spring-framework-reference/core.html#beans-factory-scopes-request) | Scopes a single bean definition to the lifecycle of a single HTTP request. That is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [session](https://docs.spring.io/spring/docs/5.2.0.RELEASE/spring-framework-reference/core.html#beans-factory-scopes-session) | Scopes a single bean definition to the lifecycle of an HTTP `Session`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [application](https://docs.spring.io/spring/docs/5.2.0.RELEASE/spring-framework-reference/core.html#beans-factory-scopes-application) | Scopes a single bean definition to the lifecycle of a `ServletContext`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [websocket](https://docs.spring.io/spring/docs/5.2.0.RELEASE/spring-framework-reference/web.html#websocket-stomp-websocket-scope) | Scopes a single bean definition to the lifecycle of a `WebSocket`. Only valid in the context of a web-aware Spring `ApplicationContext`. |



### 6.4.1 单例模式-singleton（Spring默认模式）

```xml
<bean id="userc" class="com.maowei.pojo.User" scope="singleton"/>
```

![singleton](E:\typora\笔记图片\Spring笔记\singleton.png)

在容器中，定义了一个Bean之后，之后所有对这个id的Bean的引用和请求，都会返回同一个实例。



### 6.4.2 原型模式-prototype

```xml
<bean id="userc" class="com.maowei.pojo.User" scope="prototype"/>
```

![prototype](E:\typora\笔记图片\Spring笔记\prototype.png)

每次从容器中取出某个Id的Bean，每次都创建一个新的对象。



### 6.4.3 其他模式

其他模式，request、session、application只有在web开发中才会使用（Only valid in the context of a web-aware Spring `ApplicationContext`）



# 7. Bean的自动装配

* 自动装配是Spring满足Bean依赖的一种方式
* Spring会在上下文中自动寻找，并自动给Bean装配属性



在Spring中有三种装配方式

1. 在xml中显式地配置
2. 在Java中显式地配置
3. 隐式的自动装配



## 7.1 测试环境

环境搭建：一个人有两只宠物

```java
public class People {

    private Dog dog;
    private Cat cat;
    private String name;
}
```



## 7.2 在XML中配置自动装配

在`<bean/>`标签中，配置autowire元素指定装配方式

省去了`<property/>`标签对属性的显式配置。



### 7.2.1 ByName自动装配

**被注入的Bean的id，要和自动注入的属性值名称相同**

比如这里：id="dog"，和注入的Bean中的dog属性名称相同

```xml
<bean id="dog" class="com.maowei.pojo.Dog"/>
<bean id="cat" class="com.maowei.pojo.Cat"/>

<bean id="people" class="com.maowei.pojo.People" autowire="byName">
    <property name="name" value="xmw"/>
    <!--
    <property name="dog" ref="dog"/>
    <property name="cat" ref="cat"/>
	-->
</bean>
<!--
如果这里的id改成dog233，那么就无法自动注入，
因为dog233和People中setDog方法里的Dog不匹配
-->
```



### 7.2.2 ByType自动装配

```xml
<bean id="dog233" class="com.maowei.pojo.Dog"/>
<bean id="cat" class="com.maowei.pojo.Cat"/>

<bean id="people" class="com.maowei.pojo.People" autowire="byType">
    <property name="name" value="xmw"/>
</bean>
<!--
使用ByType时，即使Bean的id和set方法名不一致也没关系，
但是要保证Bean的类型和注入类的属性类型相同，
且保证Bean的类型唯一，如果出现多个相同类型的Bean，也无法自动注入
-->
```



小结：

* 使用ByName时，需要保证被注入的Bean的id，和需要自动注入的Bean中的属性名称相同
* 使用ByType时，需要保证所有的Bean的类型唯一，并且这个Bean需要和自动注入的属性的类型一致



## 7.3 使用注解实现自动装配

JDK1.5开始支持注解，Spring2.5开始支持注解自动装配

官方文档中的notice：**Annotation injection is performed before XML injection. Thus, the XML configuration overrides the annotations for properties wired through both approaches.**

> **注解注入在XML配置之前执行，如果同时使用注解和XML配置，XML中的配置会覆盖注解的属性注入**



使用注解：

1. 在xml中导入Context约束

   ```xml
   <beans=xmlns:context="http://www.springframework.org/schema/context"
   	xsi:schemaLocation="http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd">
   ```

2. 并配置注解支持

   `<context:annotation-config/>`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
	
    <!--开启注解的支持-->
    <context:annotation-config/>

</beans>
```



### 7.3.1 @Autowired和 @Qualifier

**@Autowired注解**

**直接在需要自动装配的属性上加上这个注解即可**，也可以在属性对应的set方法上加这个注解

使用Autowired注解，可以不用编写set方法，前提是**这个自动装配的属性在IoC容器中存在**，**默认使用byType进行注入，如果存在相同类型，则使用byName注入**



**如果将AutoWired注解中的required注解设为false，表示这个属性可以为null，否则表示不为空**

使用**@Nullable注解**和上面的效果相同

```java
public @interface Autowired {
    boolean required() default true;
}
```

```java
@Nullable //同样的，如果在属性前加上了@Nullable注解，表示这个属性可以为Null，和上面的效果相同
```



在容器中有多个同类型但不同命bean的情况下，

`@Autowired`注解可以配合`@Qulifier`注解一起使用，**为这个属性装配容器中指定id的bean**。指定的id为`@Qualifier`注解中的value值。如：

容器中的beans配置

```xml
<bean id="dog222" class="com.maowei.pojo.Dog"/>
<bean id="dog111" class="com.maowei.pojo.Dog"/>
<bean id="cat111" class="com.maowei.pojo.Cat"/>
<bean id="cat222" class="com.maowei.pojo.Cat"/>
<bean id="people" class="com.maowei.pojo.People">
    <property name="name" value="xmw"/>
</bean>
```

使用`@Autowired`搭配`@Qualifier`，装配指定id的bean

```java
public class People {

    //Autowired中的required属性，设为false，表示这个属性可以为null
    @Autowired(required = false)
    @Qualifier(value = "dog111") //指定的id，为注解中的value值
    private Dog dog;
    
    @Autowired
    @Qualifier(value = "cat222")
    private Cat cat;
    private String name;
}//这里为了简洁，省去了getter，setter和toString方法
```



### 7.3.2 @Resourece

xml中的bean配置：

```xml
<bean id="dog222" class="com.maowei.pojo.Dog"/>
<bean id="dog111" class="com.maowei.pojo.Dog"/>
<bean id="cat" class="com.maowei.pojo.Cat"/>
<bean id="cat222" class="com.maowei.pojo.Cat"/>
<bean id="people" class="com.maowei.pojo.People">
    <property name="name" value="xmw"/>
</bean>
```

实体类中的注解：

`@Resource`注解，**先通过byName方式实现自动装配，如果找不到匹配的名字，再通过byType实现**，如果两者都不行，会报错。如：

> 这里有两个cat类型的bean，一个叫"cat","cat222"，@Resource会先通过名字查找，把id为"cat"的bean注入到属性中

>  而dog类型的两个bean，名字都不为dog，通过byName查找失败，再通过byType发现有两个Dog类型，也失败，这个时候将@Resource注解中的**name设为指定的id**，即可在多个同类不同命的bean存在的情况实现注入。

```java
public class People {

    @Resource(name = "dog111")
    private Dog dog;

    @Resource
    private Cat cat;
    private String name;
}
```



小结：

`@Resource`和`@Autowired`的区别：

* 都是用来自动装配的，都可以放在属性字段上
* `@Autowired`默认通过byType的方式实现，如果有相同的类型，再通过byName实现，而且必须要求这个自动装配的属性在容器中存在【常用】
* `@Resource`默认通过byName的方式实现，如果找不到名字，则通过byType的方式实现。如果两者都找不到的情况下，就报错【常用】。
* 当一个接口存在多个实现类的情况下，`@Autowired` 和`@Resource`都需要通过名称才能正确匹配到对应的 Bean。`Autowired` 可以通过 `@Qualifier` 注解来显示指定名称，`@Resource`可以通过 `name` 属性来显示指定名称。



# 8.使用注解开发

在Spring4之后，使用注解开发，必须要导入aop包

![image-20200723100823325](E:\typora\笔记图片\Spring笔记\image-20200723100823325.png)



使用注解需要导入context约束，增加注解支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
    
    <!--指定要扫描的包，这个包下的注解会生效-->
    <context:component-scan base-package="com.maowei.pojo"/>
    <context:annotation-config/>
    
</beans>
```



`<context:component-scan base-package="com.maowei.pojo"/>`标签说明：

通常情况下我们在创建spring项目的时候在xml配置文件中都会配置这个标签，配置完这个标签后，spring就会去自动扫描base-package对应的路径或者该路径的子包下面的java文件，**如果扫描到文件中带有**`@Service`，`@Component`，`@Repository`，`@Controller ` **等这些注解的类**，**则把这些类注册为bean** 
注：在注解后加上例如@Component(value=”abc”)时，注册的这个类的bean的id就是adc.



1. **bean**

   `<context:component-scan base-package="com.maowei.pojo"/>`，在xml中配置完要使用注解的包之后，**在想要使用注解的类上添加 **`@Component` 、`@Service`、`@Controller` 或 `@Repository` **注解**。

   

2. **属性如何注入**

   **在想要注入的属性或者对应的set方法上添加**`@Value`**注解,里面的值就是注入的值**

   ```java
   //相当于<bean id="user" class="com.maowei.pojo.User"/>
   //组件，使用这个注解，对象交给Spring管理
   @Component
   public class User {
       //相当于<property name="name" value="xmw"/>
       @Value("xmw")
       private String name;
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   ```

   

3. **衍生的注解**

   @Component有几个衍生的注解，在web开发中，按照mvc三层架构分层，不同的层使用不同的注解，但是这四个注解的功能都是一样的，**都表示将某个类注册到Spring容器中托管，装配bean**。

   * dao层【`@Repository`】
   * service层【`@Service`】
   * controller层【`@Controller`】

4. **自动装配**

   ```markdown
   * @Autowired:自动装配，先通过类型注入，再通过名字注入
   * @Resource：自动装配，先通过名字注入，再通过类型注入
   * @Nullable：如果属性添加了这个注解，说明这个属性可以为null
   ```

5. **作用域**

   ```java
   @Component
   @Scope("singleton")
   public class User {
       //相当于<property name="name" value="xmw"/>
       @Value("xmw")
       private String name;
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   ```

   

6. 小结

   xml与注解：

   * xml更加通用，适用于任何场合，维护简单方便
   * 注解，不是自己的类是用不了，**不能被其他bean引用**，维护相对复杂

   **xml与注解最佳实践**：

   * xml用来管理bean

   * 注解只负责完成属性的注入

   * 在使用的过程中，要注意：**一定要开启注解的支持，才能让注解生效**

     ```xml
     <!--指定要扫描的包，这个包下的注解会生效-->
     <context:component-scan base-package="com.maowei"/>
     <context:annotation-config/>
     ```



# 9. 使用Java的方式配置Spring

完全不使用Spring的xml配置，全权交给Java来做

JavaConfig是Spring 的一个子项目，在Spring4后，它成为了一个核心功能。



**实体类：**

```java
package com.maowei.pojo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

//@Component表示，这个类被注册到Spring容器中，被托管
@Component
public class User {

    @Value("xmw")
    private String name;
	
    private Dog dog;
    
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```



**配置类：**

`@Bean`注解，表明这个方法**实例化、配置并初始化一个对象给Spring IoC容器托管**。

```java
package com.maowei.config;

import com.maowei.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

// @Configuration代表一个配置类，相当于之前的applicationContext.xml配置文件
// 这个配置类也会交给Spring托管，注册到容器中，它本质还是一个@Component
@Configuration
@ComponentScan("com.maowei.pojo")
//import配置类，和xml中的<import/>标签相同
@Import(UserConfig2.class)
public class UserConfig {

    // @Bean注解，注册一个bean，相当于xml配置文件中的一个<bean/>标签
    // 这个方法的名字，相当于<bean/>标签中的id属性
    // 方法的返回类型，相当于<bean/>标签中的class属性
    @Bean
    public User user(Dog dog) {
        User user = new User();
        user.setDog(dog);
        return user //返回需要注入到bean中的对象
    }
    
    @Bean
    public Dog dog() {
        return new Dog("Little white");
    }
}

```



**测试类：**

```java
import com.maowei.config.UserConfig;
import com.maowei.pojo.User;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MyTest {

    @Test
    public void test1() {
        //如果完全使用配置类的方法实现依赖注入，只能通过AnnotationConfig上下文来获取容器，通过配置类的class对象加载
        ApplicationContext context = new AnnotationConfigApplicationContext(UserConfig.class);
        User user = context.getBean("user",User.class);
        System.out.println(user.getName());
    }
}

```



# 10. 代理模式

AOP的底层机制就是动态代理

代理模式的分类：

* 静态代理
* 动态代理

<img src="E:\typora\笔记图片\Spring笔记\image-20200723135343449.png" alt="image-20200723135343449" style="zoom:80%;" />

## 10.1 静态代理

角色分析：

* **抽象角色**：一般会使用接口或者抽象类来解决（租房）
* **真实角色**：被代理的角色（房东）
* **代理角色**：代理真实角色，代理真实角色之后，我们一般会做一些附属操作（中介）
* **客户**：访问代理对象的人（租房的人）



代码步骤：

1. 接口（抽象角色）

   ```java
   public interface Rent {
       void rent();
   }
   ```

   

2. 真实角色

   ```java
   public class Landlord implements Rent {
   
       public void rent() {
           System.out.println("房东要租房");
       }
   }
   ```

   

3. 代理角色

   ```java
   public class Proxy {
   
       private Landlord landlord;
   
       public Proxy() {
       }
   
       public Proxy(Landlord landlord) {
           this.landlord = landlord;
       }
   
       public void seeHouse() {
           landlord.rent(); //真实角色的业务
           System.out.println("中介带客户看房"); //公共业务
       }
       
       //公共业务
       public void signContract() {
           System.out.println("签订租赁合同");
       }
       
       //公共业务
       public void collectRents() {
           System.out.println("中介收取租金");
       }
   }
   ```

   

4. 客户访问代理角色

   ```java
   public class Client {
   
       public static void main(String[] args) {
           // 真实对象
           Landlord landlord = new Landlord();
           // 交给代理
           Proxy proxy = new Proxy(landlord);
           proxy.seeHouse();
           proxy.signContract();
           proxy.collectRents();
       }
   }
   ```

   

**代理模式的好处：**

* 可以使真实角色的操作更加纯粹，不同去关注公共业务
* 公共业务交给代理角色，实现业务分工
* 公共业务发生扩展的时候，方便集中管理

**缺点：**

* 一个真实角色就会产生一个代理对象；代码量会翻倍，开发效率低



## 10.2 加深理解

**在不改变原来的代码的情况下，实现了对原有功能的增强，这是AOP中最核心的思想**

AOP：

<img src="E:\typora\笔记图片\Spring笔记\image-20200723143445262.png" alt="image-20200723143445262" style="zoom:67%;" />



## 10.3 动态代理

* 动态代理和静态代理角色一样
* 动态代理的代理类是动态生成的，不是我们直接写好的
* 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  * 基于接口：JDK动态代理【在这里使用】
  * 基于类：cglib
  * java字节码：Javassist



**动态代理的是基于反射实现的**

需要了解两个类：Proxy，InvocationHandler（接口）

**动态代理的好处**：

* 可以使真实角色的操作更加纯粹，不同去关注公共业务

* 公共业务交给代理角色，实现业务分工

* 公共业务发生扩展的时候，方便集中管理

* 一个动态代理类代理的是一个接口，一般就是对应的一类业务

* 一个动态代理类**可以代理多个类**，只要是实现了同一个接口即可

  **ProxyInvocationHandler（动态代理类**）：

  ```java
  //使用这个类自动生成代理类
  public class ProxyInvocationHandler implements InvocationHandler {
  
      private Object target;
  
      public void setRent(Rent rent) {
          this.target = rent;
      }
  
      //生成得到代理类
      public Object getProxy() {
          return Proxy.newProxyInstance(target.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
      }
  
      //处理代理实例，并返回结果
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
          //动态代理的本质，是使用反射机制实现
          Object result = method.invoke(target, args);
  
          return result;
      }
  }
  
  ```

  Client使用：

  ```java
  public class Client {
  
      public static void main(String[] args) {
          Landlord landlord = new Landlord();
          ProxyInvocationHandler pih = new ProxyInvocationHandler();
          pih.setRent(landlord);
          Rent proxy = (Rent) pih.getProxy(); //动态生成代理类
          proxy.rent();
      }
  }
  ```



# 11. AOP

## 11.1 什么是AOP

**AOP（Aspect Oriented Programming）**意为：**面向切面编程**，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。<img src="E:\typora\笔记图片\Spring笔记\微信图片_20200724142701.png" alt="微信图片_20200724142701" style="zoom:100%;" />



## 11.2 AOP在Spring中的作用

- 横切关注点：跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志 , 安全 , 缓存 , 事务等等 ....

- 切面（ASPECT）：横切关注点 被模块化 的特殊对象。即，**它是一个类**。

- 通知（Advice）：切面必须要完成的工作。即，**它是类中的一个方法**。

- 目标（Target）：被通知对象。

- 代理（Proxy）：向目标对象应用通知之后创建的对象。

- 切入点（PointCut）：切面通知 执行的 “地点”的定义。

- 连接点（JointPoint）：与切入点匹配的执行点。

  ![微信图片_20200724142833](E:\typora\笔记图片\Spring笔记\微信图片_20200724142833 (3).png)

SpringAOP中，通过Advice定义横切逻辑，Spring中支持5种类型的Advice:

![微信图片_20200724144055](E:\typora\笔记图片\Spring笔记\微信图片_20200724144055.png)

即 Aop 在 不改变原有代码的情况下 , 去增加新的功能 .



## 11.3 使用Spring实现AOP

【重点】使用AOP织入，需要导入一个依赖包！

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
   <groupId>org.aspectj</groupId>
   <artifactId>aspectjweaver</artifactId>
   <version>1.9.4</version>
</dependency>
```



### 11.3.1 通过Spring API实现

首先编写业务接口和实现类

接口：

```java
public interface UserService {

    void add();
    void delete();
    void update();
    void select();
}
```

实现类：

```java
public class UserServiceImpl implements UserService {
    public void add() {
        System.out.println("增加了一个用户");
    }

    public void delete() {
        System.out.println("删除了一个用户");
    }

    public void update() {
        System.out.println("更新了一个用户");
    }

    public void select() {
        System.out.println("查询一个用户");
    }
}
```

然后编写增强类，一个前置增强，一个后置增强

```java
public class BeforeLog implements MethodBeforeAdvice {

    //method：要执行目标对象的方法
    //args：方法的参数
    //target:目标对象
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName() + "的" + method.getName() + "被执行了");
    }
}
```

```java
public class AfterLog implements AfterReturningAdvice {

    //returnValue:返回值
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了" + method.getName() + "方法" + ",返回值：" + returnValue);
    }
}
```

最后去spring的文件中注册 , 并实现aop切入实现 , 注意导入约束

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop 
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="afterLog" class="com.maowei.Log.AfterLog"/>
    <bean id="beforeLog" class="com.maowei.Log.BeforeLog"/>
    <bean id="userService" class="com.maowei.service.UserServiceImpl"/>

    <!--方式一:使用原生Spring API接口-->

    <aop:config>
        <!--切入点: expression:表达式,execution(要执行的位置 * * * * *)-->
        <aop:pointcut id="pointcut" expression="execution(* com.maowei.service.*.*(..))"/>

        <!--执行环绕增加-->
        <aop:advisor advice-ref="beforeLog" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```

测试类；

```java
@Test
public void test1() {
    ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    //注意，动态代理代理的是接口
    UserService userService = (UserService) context.getBean("userService");
    userService.delete();
}
```

* **Spring的AOP就是将公共的业务 (日志 , 安全等) 和领域业务结合起来 , 当执行领域业务时 , 将会把公共业务加进来 . 实现公共业务的重复利用 . 领域业务更纯粹 , 程序员专注领域业务 , 其本质还是动态代理 。**
* 或者说，在不修改原来业务的情况下，实现动态的增强



### 11.3.2 通过自定义类实现

目标业务仍然不变，是UserServiceImpl

先自定义一个切入类：

```java
public class DiyPointCut {

    public void before() {
        System.out.println("===========方法执行前===========");
    }

    public void after() {
        System.out.println("===========方法执行后===========");
    }
}
```

在Spring中配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop 
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="afterLog" class="com.maowei.Log.AfterLog"/>
    <bean id="beforeLog" class="com.maowei.Log.BeforeLog"/>
    <bean id="userService" class="com.maowei.service.UserServiceImpl"/>

    <!--方式二，自定义类实现AOP-->
    <bean id="diy" class="com.maowei.diy.DiyPointCut"/>
    <aop:config>
        <!--使用AOP，aspect标签实现-->
        <aop:aspect ref="diy">
            <aop:pointcut id="pointcut" expression="execution(* com.maowei.service.*.*(..))"/>
            <aop:before method="before" pointcut-ref="pointcut"/>
            <aop:after method="after" pointcut-ref="pointcut"/>
        </aop:aspect>
    </aop:config>
</beans>
```

测试与原来相同



### 11.3.3 通过注解实现

先编写一个用注解实现的增强类

```java
@Aspect
public class AnnotationPointCut {

    @Before("execution(* com.maowei.service.*.*(..))")
    public void before() {
        System.out.println("=========方法执行前=========");
    }

    @After("execution(* com.maowei.service.*.*(..))")
    public void after() {
        System.out.println("=========方法执行后=========");
    }
}
```

然后在xml中注册bean，并增加支持注解的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop 
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="afterLog" class="com.maowei.Log.AfterLog"/>
    <bean id="beforeLog" class="com.maowei.Log.BeforeLog"/>
    <bean id="userService" class="com.maowei.service.UserServiceImpl"/>

    <!--第三种，使用注解实现AOP-->
    <bean id="annotationPointCut" class="com.maowei.diy.AnnotationPointCut"/>
    <aop:aspectj-autoproxy/>
</beans>
```

`<aop:aspectj-autoproxy/>`说明：

通过aop命名空间的`<aop:aspectj-autoproxy />`声明自动为spring容器中那些配置`@aspectJ`切面的bean创建代理，织入切面。当然，spring 在内部依旧采用AnnotationAwareAspectJAutoProxyCreator进行自动代理的创建工作，但具体实现的细节已经被`<aop:aspectj-autoproxy />`隐藏起来了

`<aop:aspectj-autoproxy />`有一个proxy-target-class属性，默认为false，表示使用jdk动态代理织入增强，当配为`<aop:aspectj-autoproxy  poxy-target-class="true"/>`时，表示使用CGLib动态代理技术织入增强。不过即使proxy-target-class设置为false，如果目标类没有声明接口，则spring将自动使用CGLib动态代理。



# 12. 整合Mybatis

步骤：

1. 导入相关jar包

	* Junit
	* Mybatis
	* Mysql Connector
	* Spring相关
	* aop织入
	* mybatis-spring
2. 编写配置文件
3. 测试



## 12.1 Mybatis-Spring

Mybatis-Spring官方文档：http://www.mybatis.org/spring/zh/index.html

官方介绍：

MyBatis-Spring 会帮助你将 MyBatis 代码无缝地整合到 Spring 中。

使用Mybatis-Spring首先需要导包，添加maven依赖：

```xml
<dependency>
   <groupId>org.mybatis</groupId>
   <artifactId>mybatis-spring</artifactId>
   <version>2.0.2</version>
</dependency>
```



### 12.1.1 整合实现方式一

1. 创建spring配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/aop
           https://www.springframework.org/schema/aop/spring-aop.xsd">
   </beans>
   ```

2. 配置Mybatis数据源

   Mybatis-Spring利用SqlSessionFactoryBean创建一个SqlSessionFactory需要一个数据源（DataSource）

   ```xml
   <!--
       DataSource:使用Spring的数据源替换Mybatis的配置， c3p0 dbcp druid
       这里使用Spring提供的jdbc（在pom.xml中导入了spring-jdbc依赖的原因）
       org.springframework.jdbc.datasource.DriverManagerDataSource
       -->
   
   <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
       <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
       <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
       <property name="username" value="root"/>
       <property name="password" value="3.1415926"/>
   </bean>
   ```

   

3. 配置SqlSessionFactory，关联Mybatis

   ```xml
   <!--Mybatis配置-->
   <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
       <property name="dataSource" ref="dataSource"/>
       <!--绑定Mybatis配置文件-->
       <!--配置Mybatis全局配置文件myabtis-config.xml-->
       <property name="configLocation" value="classpath:mybatis-config.xml"/>
       <!--相当于mybatis配置中的<mapper/>标签，注册mapper-->
       <property name="mapperLocations" value="classpath:com/maowei/mappers/*.xml"/>
   </bean>
   ```

   官方文档对SqlSessionFactoryBean的属性的解释：

   * SqlSessionFactory有一个**唯一的必要属性**：用于 JDBC 的 **DataSource**。这可以是任意的 DataSource 对象，它的配置方法和其它 Spring 数据库连接是一样的。

     

   * 一个常用的属性是 **configLocation，它用来指定 MyBatis 的 XML 配置文件路径**。它在需要修改 MyBatis 的基础配置非常有用。通常，基础配置指的是 < settings> 或 < typeAliases>元素。

   

   * 需要注意的是，这个配置文件并不需要是一个完整的 MyBatis 配置。确切地说，任何环境配置（<environments>），数据源（<DataSource>）和 MyBatis 的事务管理器（<transactionManager>）都会被忽略。SqlSessionFactoryBean 会创建它自有的 MyBatis 环境配置（Environment），并按要求设置自定义环境的值。

     

4. 注册sqlSessionTemplate，关联sqlSessionFactory

   ```xml
   <!--SqlsessionTemplate：就是Mybatis中的SqlSession-->
   <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
       
       <!--只能使用构造器注入，因为SqlSessionTemplate没有setter方法-->
       <constructor-arg index="0" ref="sqlSessionFactory"/>
   </bean>
   ```

   * **sqlSessionTemplate是SqlSession 的一个实现**，这意味着可以使用它无缝代替你代码中已经在使用的 SqlSession。

   * sqlSessionTemplate可以参与到 Spring 的事务管理中，并且由于其是线程安全的，可以供多个映射器类使用

     

5. 增加Dao接口的实现类，私有化sqlSessionTemplate

   ```java
   public class UserMapperImpl implements UserMapper {
   	
       //sqlSession由Spring容器管理并注入，不需要自己创建
       private SqlSessionTemplate sqlSessionTemplate;
   
       public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate) {
           this.sqlSessionTemplate = sqlSessionTemplate;
       }
   
   
       public List<User> getAllUsers() {
           UserMapper userMapper = sqlSessionTemplate.getMapper(UserMapper.class);
           List<User> allUsers = userMapper.getAllUsers();
           return allUsers;
       }
   }
   ```

   

6. 实现类注册bean

   ```xml
   <bean id="userMapper" class="com.maowei.mappers.UserMapperImpl">
           <property name="sqlSessionTemplate" ref="sqlSession"/>
       </bean>
   ```

   Spring IoC思想：所有对象都由容器托管，即一个Bean对象。

   这样可以直接使用Spring容器获取实现类。

   

7. 测试

   ```java
   @Test
   public void SpringMybatisTest() {
       ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
       UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
       List<User> allUsers = userMapper.getAllUsers();
       for (User user : allUsers) {
           System.out.println(user);
       }
   }
   ```

   

   **整合后的Mybatis配置文件的内容**：

   全部可以被spring整合，为了清楚，也可以把别名（typeAliases）和设置（Settings）放在Mybatis配置文件中。

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   
   <configuration>
   
       <typeAliases>
           <typeAlias alias="User" type="com.maowei.pojo.User"/>
       </typeAliases>
   
       <!--设置-->
   <!--    <settings>-->
   <!--        <setting name="" value=""/>-->
   <!--    </settings>-->
   </configuration>
   ```



### 12.1.2 整合实现方式二

dao继承SqlSessionSupport类，直接通过父类的getSession()方法获得SqlSession，然后直接注入SqlSessionFactory。

对比方式1 , 不需要管理SqlSessionTemplate , 而且对事务的支持更加友好 . 可跟踪源码查看



测试：

1. 使用新的实现类

   ```java
   public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper{
       public List<User> getAllUsers() {
           return getSqlSession().getMapper(UserMapper.class).getAllUsers();
       }
   }
   ```



2. 注册Bean

   ```xml
   <bean id="userMapper2" class="com.maowei.mappers.UserMapperImpl2">
       <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
   </bean>
   ```

   相比方式一，不需要在xml中，手动配置SqlSession，直接注入SqlSessionFactory



3. 测试

   ```java
   @Test
   public void SpringMybatisTest() {
       ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
       UserMapper userMapper = context.getBean("userMapper2", UserMapper.class);
       List<User> allUsers = userMapper.getAllUsers();
       for (User user : allUsers) {
           System.out.println(user);
       }
   }
   ```




# 13. 声明式事务

事务：

* 把一组业务当成一个业务来做，一组操作要么都成功，要么都失败
* 保证数据的一致性和完整性



**事务ACID原则**：

* 原子性（atomicity）
  * 事务是原子性操作，由一系列动作组成，事务的原子性确保动作要么全部完成，要么完全不起作用
* 一致性（consistency）
  * 一旦所有事务动作完成，事务就要被提交。数据和资源处于一种满足业务规则的一致性状态中
* 隔离性（isolation）
  * 可能多个事务会同时处理相同的数据，因此每个事务都应该与其他事务隔离开来，防止数据损坏
* 持久性（durability）
  * 事务一旦完成，无论系统发生什么错误，结果都不会受到影响。通常情况下，事务的结果被写到持久化存储器中



## 13.1 Spring中的事务管理

* 声明式事务
  * 一般情况下比编程式事务好用。
  * 将事务管理代码从业务方法中分离出来，以声明的方式来实现事务管理。
  * **将事务管理作为横切关注点，通过aop方法模块化**。Spring中通过Spring AOP框架支持声明式事务管理。
* 编程式事务
  * 将事务管理代码嵌到业务方法中来控制事务的提交和回滚
  * 缺点：必须在每个事务操作业务逻辑中包含额外的事务管理代码



## 13.2 声明式事务管理的使用

在接口中添加删除，添加用户和测试方法

```java
public interface UserMapper {

    //查询所有用户
    List<User> getAllUsers();

    //通过ID删除一个用户
    int deleteUserById(int id);

    //添加一个用户
    int addUser(User user);

    //事务测试方法，包含删除、添加用户和查询所有用户三个操作
    //由于这里式测试方法，不需要sql映射，所以mapper.xml中不需要配置这个映射方法
    int transactionTest();
}
```



故意在UserMapper.xml中把delete方法的SQL语句写错

```xml
<delete id="deleteUserById">
    <!--多加一个s，为了事务的测试-->
    deletes from users where id=#{id};
</delete>

<insert id="addUser" parameterType="User">
    insert into users(id,name,pwd)
    values(#{id},#{name},#{pwd});
</insert>
```



在接口实现类中实现所有方法：

```java
public int deleteUserById(int id) {
    UserMapper userMapper = sqlSessionTemplate.getMapper(UserMapper.class);
    return userMapper.deleteUserById(id);
}

public int addUser(User user) {
    UserMapper mapper = sqlSessionTemplate.getMapper(UserMapper.class);
    return mapper.addUser(user);
}

public int transactionTest() {
    User user = new User(7,"小七","123456");
    UserMapper mapper = sqlSessionTemplate.getMapper(UserMapper.class);
    mapper.addUser(user); //添加用户
    mapper.deleteUserById(7); //删除用户
    List<User> allUsers = mapper.getAllUsers(); //查询并打印所有用户信息
    for (User allUser : allUsers) {
        System.out.println(allUser);
    }
    return 1;
}
```



测试类：

```java
@Test
public void transactionTest() {
    User user = new User(7,"小七","123456");
    ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
    userMapper.transactionTest();
}
```

因为故意把delete的SQL语句写错了，这个时候会报一个SQL语法错误，由于没有使用事务，transactionTest()方法里插入用户执行成功，到删除用户这里停止，然而我们希望这个方法里的sql操作要么全部成功，要么全部失败，所以需要使用事务。



### 13.2.1 使用声明式事务的配置

使用Spring事务管理，配置文件文件头需要先导入约束

```xml
xmlns:tx="http://www.springframework.org/schema/tx"

http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx.xsd">
```



**事务管理器**

- 无论使用Spring的哪种事务管理策略（编程式或者声明式）事务管理器都是必须的。
- 就是 Spring的核心事务管理抽象，管理封装了一组独立于技术的方法。

```xml
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
       <property name="dataSource" ref="dataSource" />
</bean>
```



**配置好事务管理器后我们需要去配置事务的通知**:

其中**子标签中的name属性就是实现类中需要使用事务的方法**，这里声明transactionTest这个方法使用事务

```xml
<!--结合AOP实现事务的织入-->
<!--配置事务通知-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!--给哪些方法配置事务-->
    <!--配置事务的传播特性-->
    <tx:attributes>
        <tx:method name="addUser" propagation="REQUIRED"/>
        <tx:method name="deleteUserById" propagation="REQUIRED"/>
        <tx:method name="transactionTest" propagation="REQUIRED"/>
        <tx:method name="getAllUsers" read-only="true"/>
        <tx:method name="*" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>
```

**spring事务传播特性：**

事务传播行为就是多个事务方法相互调用时，事务如何在这些方法间传播。spring支持7种事务传播行为：

- propagation_requierd：**如果当前没有事务，就新建一个事务，如果已存在一个事务中，加入到这个事务中，这是最常见的选择**。
- propagation_supports：支持当前事务，如果没有当前事务，就以非事务方法执行。
- propagation_mandatory：使用当前事务，如果没有当前事务，就抛出异常。
- propagation_required_new：新建事务，如果当前存在事务，把当前事务挂起。
- propagation_not_supported：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
- propagation_never：以非事务方式执行操作，如果当前事务存在则抛出异常。
- propagation_nested：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与propagation_required类似的操作

Spring 默认的事务传播行为是 PROPAGATION_REQUIRED，它适合于绝大多数的情况。

假设 ServiveX#methodX() 都工作在事务环境下（即都被 Spring 事务增强了），假设程序中存在如下的调用链：Service1#method1()->Service2#method2()->Service3#method3()，那么这 3 个服务类的 3 个方法通过 Spring 的事务传播机制都工作在同一个事务中。

就好比，我们**刚才的几个方法存在调用，所以会被放在一组事务当中！**



**配置AOP**：

```xml
<!--配置aop织入事务-->
<aop:config>
   <aop:pointcut id="txPointcut" expression="execution(* com.kuang.dao.*.*(..))"/>
   <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"/>
</aop:config>
```



**测试：**

```java
@Test
public void transactionTest() {
    User user = new User(7,"小七","123456");
    ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
    userMapper.transactionTest();
}
```

配置事务之后，添加用户、删除用户和查询所有用户要么都成功，要么都失败，保证了数据的一致性和完整性。

附完整的xml配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd">

    <import resource="spring-dao.xml"/>

    <bean id="userMapper" class="com.maowei.mappers.UserMapperImpl">
        <property name="sqlSessionTemplate" ref="sqlSession"/>
    </bean>

    <!--配置声明式事务-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <constructor-arg name="dataSource" ref="dataSource" />
    </bean>

    <!--结合AOP实现事务的织入-->
    <!--配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--给哪些方法配置事务-->
        <!--配置事务的传播特性-->
        <tx:attributes>
            <tx:method name="addUser" propagation="REQUIRED"/>
            <tx:method name="deleteUserById" propagation="REQUIRED"/>
            <tx:method name="transactionTest" propagation="REQUIRED"/>
            <tx:method name="getAllUsers" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <!--配置事务切入,编织事务-->
    <aop:config>
        <aop:pointcut id="txPointCut" expression="execution(* com.maowei.mappers.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
    </aop:config>

</beans>
```

