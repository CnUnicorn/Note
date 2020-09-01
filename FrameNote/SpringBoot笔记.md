[toc]

# 1. SpringBoot简介

**回顾Spring：**

Spring是一个开源框架，2003 年兴起的一个轻量级的Java 开发框架，作者：Rod Johnson  。

**Spring是为了解决企业级应用开发的复杂性而创建的，简化开发。**



Spring是如何简化Java开发的：

为了降低Java开发的复杂性，Spring采用了以下4种关键策略：

1、基于POJO的轻量级和最小侵入性编程，所有东西都是bean；

2、通过IOC，依赖注入（DI）和面向接口实现松耦合；

3、基于切面（AOP）和惯例进行声明式编程；

4、通过切面和模版减少样式代码，RedisTemplate，xxxTemplate；



什么是SpringBoot：

SpringBoot是一个javaweb的开发框架，和SpringMVC类似，对比其他javaweb框架的好处，官方说是简化开发，**约定大于配置**，  you can "just run"，能迅速的开发web应用，几行代码开发一个http接口。



Spring Boot 基于 Spring 开发，**Spirng Boot 本身并不提供 Spring 框架的核心特性以及扩展功能**，只是用于快速、敏捷地开发新一代基于 Spring 框架的应用程序。也就是说，**它并不是用来替代 Spring 的解决方案，而是和 Spring 框架紧密结合用于提升 Spring 开发者体验的工具**。Spring Boot 以**约定大于配置的核心思想**，默认帮我们进行了很多设置，**多数 Spring Boot 应用只需要很少的 Spring 配置**。同时它集成了大量常用的第三方库配置（例如 Redis、MongoDB、Jpa、RabbitMQ、Quartz 等等），Spring Boot 应用中这些第三方库几乎可以零配置的开箱即用。



简单来说就是SpringBoot其实不是什么新的框架，它**默认配置了很多框架的使用方式**，就像maven整合了所有的jar包，spring boot整合了所有的框架 。



**Spring Boot的主要优点：**

- 为所有Spring开发者更快的入门
- **开箱即用**，提供各种默认配置来简化项目配置
- 内嵌式容器简化Web项目
- 没有冗余代码生成和XML配置的要求



# 2. 第一个SpringBoot程序

两种方法初始化项目：

首先确定项目环境（我自己的环境）：

* java version "1.8.0_251"
* Apache Maven 3.6.1
* SpringBoot 2.2.9（生成器中如果没有想要的SpringBoot版本可以直接在依赖中修改RELEASE）



**方法一：**使用spring.start.io网页创建项目

1. 打开spring.start.io
2. 填写项目信息，选择初始化组件（依赖）
3. 点击Generate Project生成项目，下载zip压缩包
4. 解压项目，使用IDEA导入该项目，一定要import（New -> Project From Existing Sources,然后选择maven项目）



**方法二：**直接在IDEA中创建SpringBoot项目

1. New -> New Project
2. Spring Initializer
3. 填写项目信息
4. 先择初始化组件，Spring Web
5. 填写项目路径



**项目结构分析：**

1. 一个主启动类，xxxxSpringBootApplication，xxx是工程名。主启动类是程序的入口
2. 一个application.properties配置文件
3. 一个测试类，xxxxSpringBootApplicationTests
4. 一个pom文件,以下是pom文件的结构

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<!--父依赖-->
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.2.9.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.maowei</groupId>
	<artifactId>HelloSpringBoot</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>HelloSpringBoot</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<!--web场景启动器-->
		<!--web依赖：tomcat，DispatcherServlet，web.xml，SpringMVC.xml等等-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<!--spring-boot-starter，springboot所有依赖的名称都以这个开头-->
		<!--springboot单元测试-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<!--剔除依赖-->
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<!--打包插件-->
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>

```



# 3. 原理初探

pom.xml

**开头有一个父依赖：**

父项目的主要作用是管理项目的资源过滤和插件。

```xml
parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.5.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

点进去查看spring-boot-starter-parent,发现还有一个父依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.2.5.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
```

这里才是真正管理SpringBoot应用里面所有依赖版本的地方，SpringBoot的版本控制中心；

**以后我们导入依赖默认是不需要写版本；但是如果导入的包没有在依赖中管理着就需要手动配置版本了**



**启动器：**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**springboot-boot-starter-xxx**：就是spring-boot的场景启动器

**spring-boot-starter-web**：帮我们导入了web模块正常运行所依赖的组件；

SpringBoot将所有的功能场景都抽取出来，做成一个个的starter （启动器），只需要在项目中引入这些starter即可，所有相关的依赖都会导入进来 ， 我们要用什么功能就导入什么样的场景启动器即可 ；我们未来也可以自己自定义 starter；



**主启动类：**

默认的主启动类：

```java
//@SpringBootApplication 来标注一个主程序类
//说明这是一个Spring Boot应用
@SpringBootApplication
public class SpringbootApplication {

   public static void main(String[] args) {
     //以为是启动了一个方法，没想到启动了一个服务
      SpringApplication.run(SpringbootApplication.class, args);
   }

}
```

`@SpringBootApplication` 注解：

详细见：https://mp.weixin.qq.com/s/hzRwZvjYSX-dy-9Drz94aQ

**结论：**

1. SpringBoot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值
2. 将这些值作为自动配置类导入容器 ， 自动配置类就生效 ， 帮我们进行自动配置工作；
3. 整个J2EE的整体解决方案和自动配置都在springboot-autoconfigure的jar包中；
4. 它会给容器中导入非常多的自动配置类 （xxxAutoConfiguration）, 就是给容器中导入这个场景需要的所有组件 ， 并配置好这些组件 ；
5. 有了自动配置类 ， 免去了我们手动编写配置注入功能组件等的工作；



**SpringApplication:**

main方法实际上开启了一个进程（服务）：

```java
@SpringBootApplication
public class SpringbootApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }
}
```

**SpringApplication.run分析**

分析该方法主要分两部分，一部分是SpringApplication的实例化，二是run方法的执行；



**SpringApplication类主要做了以下四件事情：**

1、推断应用的类型是普通的项目还是Web项目

2、查找并加载所有可用初始化器 ， 设置到initializers属性中

3、找出所有的应用程序监听器，设置到listeners属性中

4、推断并设置main方法的定义类，找到运行的主类

对应构造器的方法：

```java
public SpringApplication(ResourceLoader resourceLoader, Class... primarySources) {
    // ......
    this.webApplicationType = WebApplicationType.deduceFromClasspath();
    this.setInitializers(this.getSpringFactoriesInstances();
    this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
    this.mainApplicationClass = this.deduceMainApplicationClass();
}
```



**run方法流程分析：**

![微信图片_20200807202210](D:\Typora\笔记图片\SpringBoot笔记\微信图片_20200807202210.jpg)



# 4. yaml配置注入

## 4.1 SpringBoot配置文件：

SpringBoot使用一个全局配置文件，配置文件的名称是固定的

* application.properties

  语法：key=value

* application.yml

  语法：key:：value （冒号后面有一个空格，yml对空格要求很严格）



配置文件的作用：

**修改SpringBoot自动配置的默认值**。新的工程中，SpringBoot配置文件什么都没有，因为SpringBoot采用的是底层的默认配置。

如：

在application.properties中改变端口号，默认的端口号是8080

`server.port=8081`

在application.yml中改变端口号

```yaml
server：
  prot: 8081
```



## 4.2 yaml概述：

YAML是 "YAML Ain't a Markup Language" （YAML不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）

这种语言以数据作为中心，而不是以标记语言为重点！



## 4.3 yamal基础语法

说明：语法要求严格！

1、空格不能省略

2、以缩进来控制层级关系，只要是左边对齐的一列数据都是同一个层级的。

3、属性和值的大小写都是十分敏感的



**字面量：普通的值  [ 数字，布尔值，字符串  ]**

字面量直接写在后面就可以 ， 字符串默认不用加上双引号或者单引号

如：

```yaml
k: v
```

注意：

- “ ” 双引号，不会转义字符串里面的特殊字符 ， 特殊字符会作为本身想表示的意思；

  比如 ：name: "kuang \n shen"  输出 ：kuang  换行  shen

- ' ' 单引号，会转义特殊字符 ， 特殊字符最终会变成和普通字符一样输出

  比如 ：name: ‘kuang \n shen’  输出 ：kuang  \n  shen



**对象、Map：**

```yaml
Dog:
  name: xiaobai
  age: 3

map:
  key1: val1
  key2: val2
```

**行内写法：**

```yaml
Dog: {name: xiaobai,age: 3}
```



**数组、List、Set：**

用 - 表示组内的一个元素（ - 和元素之间需要一个空格）：

```yaml
list:
  - a
  - b
  - c
```

行内写法：

```yaml
pets: [do, cat, bird]
```



## 4.4 yaml向实体类注入匹配值

yaml注入配置文件：

1. 创建application.yml

2. 编写一个实体类

   ```java
   @Component
   //@ConfigurationProperties(prefix = "dog")
   public class Dog {
   
       private String name;
       private int age;
   }//省略了构造器，getter、setter方法
   ```

3. 原来给Bean注入值的方式

   ```java
   @Component
   //@ConfigurationProperties(prefix = "dog")
   public class Dog {
   	
       @Value("xiaobai")
       private String name;
       @Value("3")
       private int age;
   }//省略了构造器，getter、setter方法
   ```

4. 使用yaml配置注入

   **yml配置文件**：

   ```yaml
   dog:
     name: Alice
     age: 3
   ```

   **在实体类上添加**`@ConfigurationProperties`**注解：**

   prefix参数对应yaml文件中的名字

   ```java
   @Component
   @ConfigurationProperties(prefix = "dog")
   public class Dog {
   
       private String name;
       private int age;
   }//省略了构造器，getter、setter方法
   ```

   5.SpringBoot测试类

   ```java
   @SpringBootTest
   class Springboot02ApplicationTests {
   
       @Autowired
       private Dog dog;
   
       @Test
       public void pojoTest() {
           System.out.println(dog);
       }
   }
   // 测试结果：Dog{name='Alice', age=3}，注入成功
   ```



实体类中会报一个警告，springboot配置注解处理器没有找到

![image-20200820090438025](D:\Typora\笔记图片\SpringBoot笔记\image-20200820090438025.png)

官方文档中建议加上配置文件处理器依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```



## 4.5 使用properties注入值

**@PropertySource ：**加载指定的配置文件（加载properties配置文件）；

**@configurationProperties**：默认从全局配置文件中获取值；

1. 创建person.properties配置文件

   ```properties
   name=maowei
   ```

2. 在Person实体类中注入

   ```java
   @PropertySource(value = "classpath:person.properties") //指定properties文件的路径
   @Component //注册bean
   public class Person {
   
       @Value("${name}") // 占位符替换值
       private String name;
   
       ......  
   }
   ```



## 4.6 yaml配置文件占位符

```yaml
erson:
    name: maowei${random.uuid} # 随机uuid
    age: ${random.int}  # 随机int
    happy: false
    birth: 2000/01/01
    maps: {k1: v1,k2: v2}
    hello: hello
    lists:
      - code
      - girl
      - music
    dog:
      name: ${person.hello:other}_旺财 # 如果person有hello属性，使用hello属性替换；否则使用other替换进行拼接
      age: 1
```



**小结：**

`@Configuration` 注解和 `@Value` 注解的区别

![微信图片_20200820091613](D:\Typora\笔记图片\SpringBoot笔记\微信图片_20200820091613.png)

1、@ConfigurationProperties只需要写一次即可 ， @Value则需要每个字段都添加

2、松散绑定：这个什么意思呢? 比如我的yml中写的last-name，这个和lastName是一样的， - 后面跟着的字母默认是大写的。这就是松散绑定。可以测试一下

3、JSR303数据校验 ， 这个就是我们可以在字段是增加一层过滤器验证 ， 可以保证数据的合法性

4、复杂类型封装，yml中可以封装对象 ， 使用value就不支持



# 5. JSR303校验

SpringBoot中可以使用 `@Validated` 注解来校验数据，如果数据不符合要求，会统一抛出异常，方便异常中心统一处理。

**注意：**使用JSR303校验，一定要在类上加上 `@Validated` 注解，否则即便加上了其他注解，校验也不会生效

如： `@Max` 限制数值最大值，`@Email` 限制字符格式为邮箱地址

```java
@Component
@ConfigurationProperties(prefix = "dog")
@Validated
public class Dog {

    @Email
    private String name;
    @Max(value = 2, message = "年龄不超过2岁")
    private int age;

    public Dog() {
    }

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }
```



**常用的注解：**

```java
@NotNull(message="名字不能为空")
private String userName;

@Max(value=120,message="年龄最大不能查过120")
private int age;

@Email(message="邮箱格式错误")
private String email;

空检查
@Null       验证对象是否为null
@NotNull    验证对象是否不为null, 无法查检长度为0的字符串
@NotBlank   检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格.
@NotEmpty   检查约束元素是否为NULL或者是EMPTY.
    
Booelan检查
@AssertTrue     验证 Boolean 对象是否为 true  
@AssertFalse    验证 Boolean 对象是否为 false  
    
长度检查
@Size(min=, max=) 验证对象（Array,Collection,Map,String）长度是否在给定的范围之内  
@Length(min=, max=) string is between min and max included.

日期检查
@Past       验证 Date 和 Calendar 对象是否在当前时间之前  
@Future     验证 Date 和 Calendar 对象是否在当前时间之后  
@Pattern    验证 String 对象是否符合正则表达式的规则

.......等等
除此以外，我们还可以自定义一些数据校验规则
```

**下面是注解的包路径：**

![image-20200824192406572](D:\Typora\笔记图片\SpringBoot笔记\image-20200824192406572.png)



# 6. 多环境切换及配置文件加载顺序

1. 可以使用多个.properties文件来实现多环境切换（原理：不同目录位置下的properties文件加载顺序不同，先加载的配置文件被后加载的配置文件覆盖）

2. 使用ymal文件，可以实现在一个配置文件中配置多个环境，并选择激活指定环境

## 6.1 ymal多文档块

使用 `---` 符号来分隔不同的环境（不可缺少）。

通过参数 `spring.profiles` 来指定环境名称；使用 `spring.profiles.active` 来激活指定环境

**profiles释义**：配置文件

```yaml
server:
  port: 8080

spring:
  profiles:
    active: dev

---
server:
  port: 8081

spring:
  profiles: dev

---
server:
  port: 8082
spring:
  profiles: test


```



## 6.2 多个properties配置文件

### 6.2.1 在resources目录下编写多个不同名称的properties文件

如：

application-dev.properties：开发调试下的环境

application-test.properties：测试下的环境

**SpringBoot默认加载 applicaion.properties配置文件**。

通过在application.properties主配置文件中，选择激活的环境。

```properties
# 激活开发环境
spring.profiles.active=dev
```



**如果application.yml与application.properties同时配置了环境，比如端口号，并且没有激活指定的环境，那么默认使用properties中配置的环境。**



### 6.2.2 在不同目录下编写多个application.properties

不同目录下的application.properties优先级不同。

优先级顺序，从高到低：

1. `file:./config/`                                         项目路径下的config文件夹中的配置文件

2. `file:./`                                                        项目路径下的配置文件
3. `classpath:./config/`                               类路径下（resources文件夹下）的config文件夹中的配置文件
4. `classpath:./`                                              类路径下（resources文件夹下）的配置文件

**3，4优先级中的类路径（java文件夹或resources文件夹下）实际上是指resources文件夹下，如果配置文件在类路径中，习惯上放在资源目录下。**

![多properties配置文件](D:\Typora\笔记图片\SpringBoot笔记\多properties配置文件.png)



### 6.3 运维技巧

项目打包之后，使用命令行参数 `spring.config.location` ，外部指定新的配置文件。

这个时候，外部指定的配置文件的优先级最高

```
java -jar spring-boot-config.jar --spring.config.location=F:/application.properties
```



# 7. 自动配置原理

1. SpringBoot会自动加载大量的自动配置类（含有默认配置）

2. 由我们自己查看需要的功能有没有在SpringBoot写好的默认配置类中

3. 查看自动配置类中配置了哪些组件：只要我们需要的组件存在其中，就不需要再手动配置了

4. 给容器中自动配置添加组件的时候，会从**properties类**中获取某些属性。我们只需要在配置文件中指定这些属性即可。

5. 个人目前的理解：**自动配置类和properties类**本质上都是JavaConfig类（包含 `@Configuration` 注解）；

   properties类就是自动配置类中组件，自动配置类通过 `@ConditionalOnxxx` 注解判断是否加载某些组件（xxxProperties类）。properties类通过与ymal配置文件绑定向类中注入属性；

   之前是，直接在xml配置中配置所有东西。**现在通过在yamal文件中配置JavaConfig**。

**注：**

* 自动配置类的名称是：xxxxxAutoConfiguration，功能是给容器中添加组件

  **自动配置类总结** ：**根据当前不同的条件判断，决定这个配置类是否生效！**

  - 一但这个配置类生效；这个配置类就会给容器中添加各种组件；
  - 这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的；
  - 所有在yamal配置文件中配置的属性都是在xxxxProperties类中封装着；
  - 配置文件能配置什么就可以参照某个功能对应的这个属性类

* properties类的名称是：xxxxxProperties，封装ymal配置中的属性（如果没有配置，使用默认值）



具体例子：**HttpEncodingAutoConfiguration类（Http编码自动配置）**

见：https://mp.weixin.qq.com/s/1z1fSZs0nA12HdpkLsxqtQ



了解：`@Conditional` 注解（xxxxAutoConfiguration，自动配置类中的条件）

自动配置类根据条件来选择加载哪些组件（`@Conditional` 注解生效的组件）。

| **@Conditional扩展注解**        | **作用（判断是否满足指定条件）**                 |
| ------------------------------- | ------------------------------------------------ |
|                                 |                                                  |
|                                 |                                                  |
| @ConditionalOnJava              | 系统的Java版本是否符合要求                       |
| @ConditionalOnBean              | 容器中存在指定Bean                               |
| @ConditionalOnMissingBean       | 容器中不存在指定Bean                             |
| @ConditionalOnExpression        | 满足SpEL表达式指定                               |
| @ConditionalOnClass             | 系统中有指定的类                                 |
| @ConditionalOnMissingClass      | 系统中没有指定的类                               |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的Bean，或者这个Bean是首选Bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                   |
| @ConditionalOnRsource           | 类路径下是否存在指定资源                         |
| @ConditionalOnWebApplication    | 当前是Web环境                                    |
| @ConditionalOnNotWebApplication | 当前不是Web环境                                  |
| @ConditionalOnJndi              | JNDI存在指定项                                   |



虽然有很多自动配置类，但是不是所有组件都加载到了容器中。

通过在ymal配置文件中配置debug=true属性，来让控制台打印自动配置报告，这样我们就可以很方便就能知道哪些自动配置类生效。

```yamal
# 开启SpringBoot调制类
debug=true
```



**Positive matches：**查看启用的自动配置类

![image-20200824212141488](D:\Typora\笔记图片\SpringBoot笔记\image-20200824212141488.png)



**Negitive matches：**查看没有启用的自动配置类

![image-20200824212258778](D:\Typora\笔记图片\SpringBoot笔记\image-20200824212258778.png)



# 8. SpringBoot Web开发

自动装配：

SpringBoot帮我们配置了什么？我们能不能进行修改？能修改哪些东西？能不能扩展？

* **xxxxAutoConfiguration：**根据条件（`@Conditional`注解），向容器中装配组件
* **xxxxProperties：**配置类（组件），可以向其中注入yaml文件中自定义的内容；由**xxxxAutoConfiguration装配到容器中。

**其中WebMVC配置全部在WebMvcAutoConfiguration中配置**。



## 8.1 静态资源处理

项目中的静态资源，如css，js等文件

**WebMvcAutoConfiguration**的源码分析见：https://mp.weixin.qq.com/s/vYbEYlveseIGWWL_eEM36w



以下四个存放静态资源的目录可以被识别：

classpath为工程目录，但习惯上静态资源目录全部存放在resources目录下

```java
"classpath:/META-INF/resources/"   // webjars包下的文件
"classpath:/resources/"            // resources目录下的名称为resources的文件夹（需要创建）
"classpath:/static/"               // resources目录下的名称为static的文件夹
"classpath:/public/"               // resources目录下的名称为public的文件夹（需要创建）
```



也可以在ymal配置文件中自定义静态文件的存放目录，此时，上面默认的静态文件存放目录全部失效：

```java
spring.resources.static-locations=classpath:/coding/,classpath:/maowei/
```

一般不会重新配置静态文件存放的目录



## 8.2 首页和图标定制

在static文件夹中新建index.html文件

此时，会自动映射到静态文件目录下的index.html文件

进入 localhost:8080/ 会自动显示index.html的内容

![image-20200831210541534](D:\Typora\笔记图片\SpringBoot笔记\image-20200831210541534.png)



## 8.3 Thymeleaf

![image-20200831214828467](D:\Typora\笔记图片\SpringBoot笔记\image-20200831214828467.png)