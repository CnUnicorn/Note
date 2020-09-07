[toc]

# 1. MVC

- MVC是模型(Model)、视图(View)、控制器(Controller)的简写，是一种软件设计规范。
- 是将业务逻辑、数据、显示分离的方法来组织代码。
- MVC主要作用是**降低了视图与业务逻辑间的双向偶合**。
- MVC不是一种设计模式，**MVC是一种架构模式**。当然不同的MVC存在差异。

**Model（模型）：**数据模型，提供要展示的数据，因此**包含数据和行为**，可以认为是领域模型或JavaBean组件（包含数据和行为），不过现在一般都分离开来：Value Object（数据**Dao**） 和 服务层（行为**Service**）。也就是模型提供了模型数据查询和模型数据的状态更新等功能，**包括数据和业务**。

**View（视图）：**负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。

**Controller（控制器）：**接收用户请求，**委托给模型进行处理**（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。也就是说控制器做了个调度员的工作。

<img src="E:\typora\笔记图片\SpringMVC笔记\image-20200729151639992.png" alt="image-20200729151639992" style="zoom:80%;" />



**MVC框架要做哪些事情**

1. 将url映射到java类或java类的方法 .
2. 封装用户提交的数据 .
3. 处理请求--调用相关的业务处理--封装响应数据 .
4. 将响应的数据进行渲染 . jsp / html 等表示层数据 .

**说明：**

​	常见的服务器端MVC框架有：Struts、Spring MVC、ASP.NET MVC、Zend Framework、JSF；常见前端MVC框架：vue、angularjs、react、backbone；由MVC演化出了另外一些模式如：MVP、MVVM 等等....



# 2. Spring MVC

Spring MVC是Spring Framework的一部分，是**基于Java实现MVC的轻量级Web框架**。

官方文档：

* 新版文档：https://docs.spring.io/spring/docs/5.2.0.RELEASE/spring-framework-reference/web.html#spring-web
* 旧版文档：https://docs.spring.io/spring/docs/4.3.24.RELEASE/spring-framework-reference/html/mvc.html



Spring MVC的特点：

1. 轻量级，简单易学
2. 高效 , 基于请求响应的MVC框架
3. 与Spring兼容性好，无缝结合
4. 约定优于配置
5. 功能强大：RESTful、数据验证、格式化、本地化、主题等
6. 简洁灵活

Spring的web框架围绕**DispatcherServlet** [ 调度Servlet ] 设计。



## 2.1 中心控制器（DispatcherServlet）

Spring的web框架围绕DispatcherServlet设计。DispatcherServlet的作用是将请求分发到不同的处理器。从Spring 2.5开始，使用Java 5或者以上版本的用户可以采用基于注解的controller声明方式。

​	Spring MVC框架像许多其他MVC框架一样, **以请求为驱动** , **围绕一个中心Servlet分派请求及提供其他功能**，**DispatcherServlet是一个实际的Servlet (它继承自HttpServlet 基类)**。

<img src="E:\typora\笔记图片\SpringMVC笔记\微信图片_20200730103214.png" alt="微信图片_20200730103214" style="zoom:80%;" />



## 2.2 执行原理

SpringMVC的原理如下图所示：

​	当发起请求时被前置的控制器拦截到请求，根据请求参数生成代理请求，找到请求对应的实际控制器，控制器处理请求，创建数据模型，访问数据库，将模型响应给中心控制器，控制器使用模型与视图渲染视图结果，将结果返回给中心控制器，再将结果返回给请求者。

<img src="E:\typora\笔记图片\SpringMVC笔记\微信图片_20200730103318.png" alt="微信图片_20200730103318" style="zoom:80%;" />

对应官方文档的图片：

<img src="E:\typora\笔记图片\SpringMVC笔记\mvc.png" alt="mvc" style="zoom:80%;" />



更为详细的Spring执行流程图：

<img src="E:\typora\笔记图片\SpringMVC笔记\微信图片_20200730103750.png" alt="微信图片_20200730103750" style="zoom:90%;" />

上图中，**实线表示SpringMVC框架提供的技术，不需要开发者实现，虚线表示需要开发者实现**。

**简要分析执行流程**

1. DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求。

   我们假设请求的url为 : http://localhost:8080/SpringMVC/hello

   

   **如上url拆分成三部分：**

   http://localhost:8080服务器域名

   SpringMVC部署在服务器上的web站点

   hello表示控制器

   通过分析，如上url表示为：请求位于服务器localhost:8080上的SpringMVC站点的hello控制器。

2. HandlerMapping为处理器映射。DispatcherServlet调用HandlerMapping,HandlerMapping根据请求url查找Handler。

3. HandlerExecution表示具体的Handler,其主要作用是根据url查找控制器，如上url被查找控制器为：hello。

4. HandlerExecution将解析后的信息传递给DispatcherServlet,如解析控制器映射等。

5. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler。

6. Handler让具体的Controller执行。

7. Controller将具体的执行信息返回给HandlerAdapter,如ModelAndView。

8. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet。

9. DispatcherServlet调用视图解析器(ViewResolver)来解析HandlerAdapter传递的逻辑视图名。

10. 视图解析器将解析的逻辑视图名传给DispatcherServlet。

11. DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图。

12. 最终视图呈现给用户。



## 2.3 对应上述流程的一个例子（使用配置）

**使用配置的SpringMVC程序**，可以用来理解上面的过程

1. 新建module，添加web支持（右键module --> add framework support）

2. 确认导入了Spring MVC的依赖

3. 配置web.xml，注册DispatcherServlet

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee 		
                                http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
   
       <!--1.注册DispatcherServlet-->
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <!--关联一个springmvc的配置文件:【servlet-name】-servlet.xml-->
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springmvc-servlet.xml</param-value>
           </init-param>
           <!--启动级别-1-->
           <load-on-startup>1</load-on-startup>
       </servlet>
   
       <!--/ 匹配所有的请求；（不包括.jsp）-->
       <!--/* 匹配所有的请求；（包括.jsp）-->
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   
   </web-app>
   ```

4. 编写关联的SpringMCV配置文件

   **名称：springmvc-servlet.xml  : [servletname]-servlet.xml**，按照官方要求

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">
   </beans>
   ```

5. 添加 处理映射器(HandlerMapping)

   ```xml
   <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
   ```

6. 添加 处理器适配器(HandlerAdapter)

   ```xml
   <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
   ```

7. 添加 视图解析器(ViewResolver)

   ```xml
   <!--视图解析器:DispatcherServlet给他的ModelAndView-->
   <!--1.获取了ModelAndView数据
           2.解析ModelAndView的视图名字
           3.拼接视图名字，找到对应视图
           -->
   <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
       <!--前缀-->
       <property name="prefix" value="/WEB-INF/jsp/"/>
       <!--后缀-->
       <property name="suffix" value=".jsp"/>
   </bean>
   ```

8. 编写我们要操作业务Controller ，**要么实现Controller接口，要么增加注解**；**需要返回一个ModelAndView**，**装数据，封视图**

   ```java
   public class HelloController implements Controller {
       public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
           //ModelAndView 模型和视图
           ModelAndView mv = new ModelAndView();
   
           //调用业务层代码
   
           //封装对象，放在ModelAndView中。Model
           mv.addObject("msg","Hello SpringMVC!");
           //封装要跳转的视图，放在ModelAndView中
           mv.setViewName("hello"); //: /WEB-INF/jsp/hello.jsp
           return mv;
       }
   ```

9. 将控制类交给Spring IoC容器托管(SpringMVC 配置文件)

   ```xml
   <!--Handler-->
   <bean id="/hello" class="com.maowei.controller.HelloController"/>
   ```

10. 编写要跳转的jsp页面，显示ModelandView存放的数据，以及我们的正常页面

    ```jsp
    <%--
      Created by IntelliJ IDEA.
      User: xmw17
      Date: 2020/7/29
      Time: 17:00
      To change this template use File | Settings | File Templates.
    --%>
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Hello</title>
    </head>
    <body>
    
    ${msg}
    
    </body>
    </html>
    
    ```

11. 配置tomcat，启动测试

    可能会出现的问题：**可能会报404错误**

    解决方案：

    1. 查看控制台输出，看一下是不是缺少了什么jar包。
    2. 如果jar包存在，显示无法输出，就在IDEA的项目发布中，添加lib依赖！
    3. 重启Tomcat 即可解决！

    如下图，Project stucture --> Artifacts --> 选择对应的module

    会发现，一开始IDEA中并没有lib文件夹，也就是没有把依赖添加到项目中，需要自己新建一个lib文件夹并添加依赖

![image-20200730105451676](E:\typora\笔记图片\SpringMVC笔记\image-20200730105451676.png)



## 2.4 使用注解的SpringMVC

将上面的例子使用注解完成要简单许多

1. 在pom.xml文件引入相关的依赖：主要有Spring框架核心库、Spring MVC、servlet , JSTL等

   

2. 配置web.xml

   web.xml中**配置DispatcherServlet，并关联spring配置文件**

   设置启动顺序

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
   
       <!--配置DisPatcherServlet-->
       <servlet>
           <servlet-name>SpringMVC</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <!--通过初始化参数，指定SpringMVC配置文件的位置，进行关联-->
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springmvc-servlet.xml</param-value>
           </init-param>
           <!--启动顺序，数字越小，启动越早-->
           <load-on-startup>1</load-on-startup>
       </servlet>
   
       <!--所有请求会被SpringMVC拦截,包括静态资源-->
       <servlet-mapping>
           <servlet-name>SpringMVC</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   </web-app>
   ```

   注意点：**/ 和 /* 的区别**

   < url-pattern > / </ url-pattern > 不会匹配到.jsp， 只针对我们编写的请求；即：.jsp 不会进入spring的 DispatcherServlet类 。< url-pattern > /* </ url-pattern > 会匹配 *.jsp，会出现返回 jsp视图 时再次进入spring的DispatcherServlet 类，导致找不到对应的controller所以报404错。

   

3. 添加spring配置文件

   在resources目录下，添加springmvc-servlet.xml配置文件

   配置形式与Spring容器配置类似，为了支持基于注解的IoC，设置了自动扫包的功能

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/mvc
          https://www.springframework.org/schema/mvc/spring-mvc.xsd">
   
       <!--自动扫描包，让指定包下的注解生效，由IoC容器同意管理-->
       <context:component-scan base-package="com.maowei.controller"/>
       <!--让SpringMVC不处理静态资源-->
       <mvc:default-servlet-handler/>
       <!--支持mvc注解驱动
           在spring中一般采用@RequestMapping注解来完成映射关系，
           要让@RequestMapping注解生效，必须向上下文中注册DefaultAnnotationMapping
           和一个AnnotationMethodHandlerAdapter实例
           这两个实例类级别和方法级别处理。
           而annotation-driven配置帮助我们自动完成上面两个实例的注入-->
       <mvc:annotation-driven/>
   
       <!--视图解析器-->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
           <!--前缀-->
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <!--后缀-->
           <property name="suffix" value=".jsp"/>
       </bean>
   
   </beans>
   ```

   其中，在视图解析器中，把所有视图都放在/WEB-INF/jsp/目录下，这样可以保证视图的安全，因为这个目录下的文件，客户端不能直接访问

   * 让IoC的注解生效`<context:component-scan base-package="com.maowei.controller"/>`

   * 过滤静态资源：HTML、JS、CSS、图片、视频`<mvc:default-servlet-handler/>`

   * 开启MVC注解驱动：`<mvc:annotation-driven/>`

   * 配置视图解析器

     

4. 创建Controller

   在整个类前的`@RequestMapping()`注解里的地址对类中的所有方法生效

   在单个方法前的`@RequestMapping()`注解只对这一个方法（某个业务操作）生效

   ```java
   package com.maowei.controller;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   @Controller
   @RequestMapping("/HelloController")
   public class HelloController {
   
       // 真实访问地址: 项目名/localhost:8080/HelloController/hello
       @RequestMapping("/hello")
       public String hello(Model model) {
           //在模型中添加msg值，可在JSP页面中取出并渲染
           model.addAttribute("msg","Hello,SpringMVC!");
           // 视图名称:WEB-INF/jsp/test.jsp
           // 返回的字符串与spring配置文件的视图解析器前后缀进行拼接，得到视图路径
           return "test";
       }
   }
   
   ```

   * `@Controller`注解是为了让Spring IoC容器初始化时能够自动扫描到

   * `@RequestMapping()`注解是为了**映射请求路径**（url），这里是因为类与方法上都有映射，所以访问url应该是：/HelloController/hello

   * 方法中的Model类型参数，是为了把Action中的数据带到视图中

   * **方法返回的字符串是视图的名称**，springmvc-servlet.xml中的视图映射器的前后缀，变成

     /WEB-INF/jsp/**test**.jsp

     

5. 创建视图层

   在 /WEB-INF/jsp 目录下创建视图文件

   ```jsp
   <%--
     Created by IntelliJ IDEA.
     User: xmw17
     Date: 2020/7/30
     Time: 14:09
     To change this template use File | Settings | File Templates.
   --%>
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   ${msg}
   </body>
   </html>
   
   ```



## 2.5 小结

创建SpringMVC项目的过程：

1. 新建一个web项目
2. 导入相关jar包（保证子项目也导入了相关jar包）
3. 编写web.xml，注册DispatcherServlet
4. 编写springmvc-servlet.xml文件
5. 创建控制类（Controller）
6. 最后完整前端视图与Controller的对应
7. 测试运行



**使用SprinMVC的必须配置的三大件：**

**处理器映射器（HandlerMapping），处理器适配器（HandlerAdapter），视图解析器（ViewResolver）**

**使用注解，只需要手动配置视图解析器；在开启MVC注解驱动后，处理器映射器和处理器适配器就不用再手动配置了**，从而省去了大段地xml配置

SpringMVC运行流程回顾：

<img src="E:\typora\笔记图片\SpringMVC笔记\微信图片_20200730103750.png" alt="微信图片_20200730103750" style="zoom:90%;" />



# 3. @Controller注解说明

**控制器Contorller：**

- 控制器复杂提供访问应用程序的行为，通常通过接口定义或注解定义两种方法实现。
- 控制器负责解析用户的请求并将其转换为一个模型。
- 在Spring MVC中一个控制器类可以包含多个方法
- 在Spring MVC中，对于Controller的配置方式有很多种



## 3.1 实现Contorller接口定义控制器

Controller是一个接口，在org.springframework.web.servlet.mvc包下，接口中只有一个方法：

```java
//实现该接口的类获得控制器功能
public interface Controller {
   //处理请求且返回一个模型与视图对象
   ModelAndView handleRequest(HttpServletRequest var1, HttpServletResponse var2) throws Exception;
}
```



控制器类：

```java
//定义控制器
//注意点：不要导错包，实现Controller接口，重写方法；
public class ControllerTest1 implements Controller {

   public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
       //返回一个模型视图对象
       ModelAndView mv = new ModelAndView();
       mv.addObject("msg","Test1Controller");
       mv.setViewName("test");
       return mv;
  }
}
```

使用实现接口的方法定义控制器还需要在spring配置文件中注册bean，还需要注册控制器映射器、控制器适配器（**完整例子见2.3**）

```xml
<bean name="/t1" class="com.maowei.controller.ControllerTest1"/>
```

**说明：**

- 实现接口Controller定义控制器是较老的办法
- **缺点是：一个控制器中只有一个方法，如果要多个方法则需要定义多个Controller；定义的方式比较麻烦；**
- 使用实现接口的方式一般不会用，比较麻烦



## 3.2 使用注解定义控制器

* 使用注解定义控制器是最常用的方式

* `@Controller`注解用来声明Spring类的实例是一个控制器

* Spring可以通过扫描机制来找到应用中所有基于注解的控制器，为了保证Spring可以找到自己定义的控制器，**需要在spring配置文件中声明组件扫描**（component-scan）

  ```xml
  <!--自动扫描包，让指定包下的注解生效，包下所有注解类交给IoC容器管理-->
  <context:component-scan base-package="com.maowei.controller"/>
  ```

  除了声明扫描外，还需要**支持MVC注解支持驱动**和**过滤静态资源**（完整例子见**2.4**）



使用注解定义的控制器的例子：

```java
//@Controller注解的类会自动添加到Spring上下文中
@Controller
public class ControllerTest2{

    //映射访问路径
    @RequestMapping("/t2")
    public String index(Model model){
        //Spring MVC会自动实例化一个Model对象用于向视图中传值
        model.addAttribute("msg", "ControllerTest2");
        //返回视图名称
        return "test"; //通过和视图解析器的前后缀拼接，视图路径：/WEB-INF/jsp/test.jsp
    }
}
```



**使用注解定义控制器的优点：**

* 一个控制器中可以有多个方法，通过`@RequestMapping`注解来区分访问路径

* **控制器和视图是弱耦合关系**，多个控制器或一个控制器的多个方法，可以重用同一个视图，实现相同页面不同内容

  

# 4. @RequetMapping注解说明

- `@RequestMapping`注解用于**映射url到控制器类或一个特定的处理程序方法**。可用于类或方法上。**用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。**

- 使用 `@RequestMapping` 注解，可以响应任何方式的请求（GET和POST）

  只注解在方法上面

  ```java
  @Controller
  public class TestController {
     @RequestMapping("/h1")
     public String test(){
         return "test";
    }
  }
  ```

  访问路径：http://localhost:8080 / 项目名 / h1

- 同时注解类与方法

  ```java
  @Controller
  @RequestMapping("/admin")
  public class TestController {
     @RequestMapping("/h1")
     public String test(){
         return "test";
    }
  }
  ```

  访问路径：http://localhost:8080 / 项目名/ admin /h1  , 需要先指定类的路径再指定方法的路径；



# 5. RestFul风格

**概念：**

Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，**只是一种风格**。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。



**功能：**

资源：互联网所有的事物都可以被抽象为资源

资源操作：使用POST、DELETE、PUT、GET，使用不同方法对资源进行操作。

分别对应 添加、 删除、修改、查询。



**传统方式操作资源**  ：

通过不同的参数来实现不同的效果，**url中问号后面是参数，多个参数之间通过&连接**。方法单一，post 和 get

​	http://127.0.0.1/item/queryItem.action?id=1 查询,GET

​	http://127.0.0.1/item/saveItem.action 新增,POST

​	http://127.0.0.1/item/updateItem.action 更新,POST

​	http://127.0.0.1/item/deleteItem.action?id=1 删除,GET或POST



**使用RESTful操作资源** ：

* **相同的请求地址，可以通过不同的请求方式来实现不同的效果**！

* **参数之间使用/分隔，而不是&和？，并且不会暴露参数名**

如下：请求地址一样，但是功能可以不同！

​	http://127.0.0.1/item/1 查询,GET

​	http://127.0.0.1/item 新增,POST

​	http://127.0.0.1/item 更新,PUT

​	http://127.0.0.1/item/1 删除,DELETE



使用方法：

1. 新建测试类

   ```java
   @Controller
   public class RestFulController {
   }
   ```

   

2. 在Spring MVC中可以使用`@PathVariable` 注解，**让方法参数的值对应绑定到一个URI模板变量上。**

   **通过**`@RequestMapping()`**注解映射url地址，指定http请求方式**

   value和path都是请求地址的别名，**method用于约束请求类型**，指定请求谓词的类型如GET, POST, HEAD, OPTIONS, PUT, PATCH, DELETE, TRACE等

   ```java
   @RequestMapping(value = "/add/{a}/{b}", method = RequestMethod.GET)
   String restFulTest(@PathVariable int a, @PathVariable int b, Model model) {
   
       int result = a + b;
       model.addAttribute("msg","" + result);
   
       return "test";
   }
   
   @RequestMapping(path = "/add/{a}/{b}", method = RequestMethod.POST)
   String restFulTest2(@PathVariable int a, @PathVariable int b, Model model) {
       int result = a + b;
       model.addAttribute("msg", "结果为:" + result);
   
       return "test";
   }
   ```

   如上，两个方法虽然映射到同一个url地址，但是分别响应GET和POST请求，传统方法则做不到这一点。

   

3. 也可以**使用注解指定http请求**，此时可以省略指定请求方式的代码

   方法中的参数仍然需要`@PathVariable`注解，将它绑定到URI模板变量上

   ```java
   
   @GetMapping("/add/{a}/{b}")
   String restFulTest(@PathVariable int a, @PathVariable int b, Model model) {
   
       int result = a + b;
       model.addAttribute("msg","" + result);
   
       return "test";
   }
   
   
   @PostMapping("/add/{a}/{b}")
   String restFulTest2(@PathVariable int a, @PathVariable int b, Model model) {
       int result = a + b;
       model.addAttribute("msg", "结果为:" + result);
   
       return "test";
   }
   ```



小结：

**使用路径变量的好处：**

- 使路径变得更加简洁；
- 获得参数更加方便，框架会自动进行类型转换。
- **通过路径变量的类型可以约束访问参数，如果类型不一样，则访问不到对应的请求方法**，如这里访问是的路径是/commit/1/a，则路径与方法不匹配，而不会是参数转换失败



Spring MVC 的 `@RequestMapping` 注解能够处理 HTTP 请求的方法, 比如 GET, PUT, POST, DELETE 以及 PATCH。**通过method参数约束HTTP请求方式**。

**所有的地址栏请求默认都会是 HTTP GET 类型的。**



方法级别的注解变体有如下几个：

使用以下注解可以替换 `@ReuqestMapping` 注解，不再需要指定HTTP方法，只需要url地址

是 `@RequestMapping(value = "/user/1", method = RequestedMethod.GET)`的快捷方式

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```



# 6. 转发和重定向

转发和重定向的区别：

1、请求次数：重定向是浏览器向服务器发送一个请求并收到响应后再次向一个新地址发出请求，转发是服务器收到请求后为了完成响应跳转到一个新的地址；重定向至少请求两次，转发请求一次；
2、地址栏不同：重定向地址栏会发生变化，转发地址栏不会发生变化；
3、是否共享数据：重定向两次请求不共享数据，转发一次请求共享数据（在request级别使用信息共享，使用重定向必然出错）；
4、跳转限制：重定向可以跳转到任意URL，转发只能跳转本站点资源；
5、发生行为不同：重定向是客户端行为，转发是服务器端行为；



**注意点：**

重定向语法中，冒号和重定向的地址之间不能有空格，否则路径拼接会出现问题。

```java
@GetMapping("/test")
public String test() {
    return "redirect:/user/login"; // 正确用法
    return "redirect: /user/login"; // 错误写法，拼接的时候空格会被当成路径的一部分
}

```





跳转的页面：**{视图解析器前缀} + viewName(使用注解时，是控制器中方法返回的名称) + {视图解析器后缀}**

Sping配置文件中视图解析器的配置：

```xml
<!--视图解析器-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
    <!--前缀-->
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <!--后缀-->
    <property name="suffix" value=".jsp"/>
</bean>
```



**SpringMVC实现重定向和转发：**

1. 无需视图解析器的情况

   **转发：**在返回的页面路径前加上**forward**；如果不加forward，则默认也是转发。

   **重定向：**在返回的页面路径上加上**redirect**。

   注意点：由于没有配置视图解析器，返回的名称必须是完整的路径名

   ```java
   @Controller
   public class ResultSpringMVC {
      @RequestMapping("/rsm/t1")
      public String test1(){
          //转发
          //return "/index.jsp";
          return "/WEB-INF/jsp/test.jsp";
     }
   
      @RequestMapping("/rsm/t2")
      public String test2(){
          //转发二
          return "forward:/index.jsp";
     }
   
      @RequestMapping("/rsm/t3")
      public String test3(){
          //重定向
          return "redirect:/index.jsp";
     }
   }
   ```

   

2. 使用视图解析器

   如果返回名称不添加任何关键字，默认为转发

   如果返回的字符串为：**redirect：完整的页面路径**，则为重定向；注意重定向必须是完整的页面路径

   ```java
   @Controller
   public class ModelTest1 {
   
       @RequestMapping("/m1/t1")
       public String modelTest(Model model) {
           model.addAttribute("msg","Hello!");
   		//重定向
           return "redirect:/index.jsp";
       }
   
       @RequestMapping("m1/t2")
       public  String modelTest2(Model model) {
           model.addAttribute("msg","HELLO!");
           //转发
           return "test";
       }
   }
   ```



# 7. 接收请求参数及数据回显

## 7.1 处理提交数据

1. 提交的域名称和处理方法的参数名一致

   提交数据：

   `http://localhost:8080/springmvc_04_controller_war_exploded/user/t1?name=xumaowei`

   ```java
   @Controller
   @RequestMapping("/user")
   public class Test1 {
   
       @GetMapping("/t1")
       public String test1(String name) {
           System.out.println(name);
           return "test";
       }
   }
   ```

   后台输出：xumaowei

   

2. 提交的域名称和处理方法参数名不一致

   使用 `@RequestParam` 注解，指定接收url中名为username的值

   提交数据：

   `http://localhost:8080/springmvc_04_controller_war_exploded/user/t2?username=xumaowei`

   ```java
   @Controller
   @RequestMapping("/user")
   public class Test1 {
       
       @GetMapping("/t2")
       public String test2(@RequestParam("username") String name) {
           System.out.println(name);
           return "test";
       }
   }
   ```

   后台输出：xumaowei

   

3. 提交是一个对象

   实体类：

   ```java
   public class User {
      private int id;
      private String name;
      private int age;
      //构造
      //get/set
      //tostring()
   }
   ```

   SpringMVC会自动匹配，类的属性名和表单域；如果属性名称没有匹配到，则这个属性为null

   原先函数需要接收三个参数，然后在方法中初始化对象，现在框架会匹配提交域的名称和类属性的名称

   ```java
   @Controller
   @RequestMapping("/user")
   public class Test1 {
       
       @GetMapping("/t3")
       public String t3(User user) {
           System.out.println(user);
   
           return "test";
       }
   }
   ```

   提交数据：

   `http://localhost:8080/springmvc_04_controller_war_exploded/user/t3?id=1&name=xumaowei&age=18`

   后台输出：User{name='xumaowei', age=12, id=1}
   如果url中没有名为name的值，则显示：User{name='null', age=12, id=1}



## 7.2 数据显示到前端

1. 通过ModelAndView

   这种方式是实现Controller接口时使用的

   

2. 通过ModelMap

   ```java
   @RequestMapping("/hello")
   public String hello(@RequestParam("username") String name, ModelMap model){
      //封装要显示到视图中的数据
      //相当于req.setAttribute("name",name);
      model.addAttribute("name",name);
      System.out.println(name);
      return "hello";
   }
   ```

   

3. 通过Model

   ```java
   @RequestMapping("/ct2/hello")
   public String hello(@RequestParam("username") String name, Model model){
      //封装要显示到视图中的数据
      //相当于req.setAttribute("name",name);
      model.addAttribute("msg",name);
      System.out.println(name);
      return "test";
   }
   ```



**对比：**

* Model 只有寥寥几个方法只适合用于储存数据，简化了新手对于Model对象的操作和理解；

* ModelMap 继承了 LinkedMap ，除了实现了自身的一些方法，同样的继承 LinkedMap 的方法和特性；

* ModelAndView 可以在储存数据的同时，可以进行设置返回的逻辑视图，进行控制展示层的跳转。



# 8. 乱码问题

**使用SpringMVC提供的过滤器：**

在**web.xml**配置文件中配置过滤器

```xml
<filter>
   <filter-name>encoding</filter-name>
   <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
   <init-param>
       <param-name>encoding</param-name>
       <param-value>utf-8</param-value>
   </init-param>
</filter>
<filter-mapping>
   <filter-name>encoding</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```

在一些极端的情况下，自带的过滤器对get请求支持不太友好



解决方案：

1. 修改tomcat配置文件,设置编码

   ```xml
   <Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
             connectionTimeout="20000"
             redirectPort="8443" />
   ```

2. 使用自定义过滤器

   先创建过滤器类，然后在**web.xml**中配置自定义过滤器

   ```java
   package com.kuang.filter;
   
   import javax.servlet.*;
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletRequestWrapper;
   import javax.servlet.http.HttpServletResponse;
   import java.io.IOException;
   import java.io.UnsupportedEncodingException;
   import java.util.Map;
   
   /**
   * 解决get和post请求 全部乱码的过滤器
   */
   public class GenericEncodingFilter implements Filter {
   
      @Override
      public void destroy() {
     }
   
      @Override
      public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
          //处理response的字符编码
          HttpServletResponse myResponse=(HttpServletResponse) response;
          myResponse.setContentType("text/html;charset=UTF-8");
   
          // 转型为与协议相关对象
          HttpServletRequest httpServletRequest = (HttpServletRequest) request;
          // 对request包装增强
          HttpServletRequest myrequest = new MyRequest(httpServletRequest);
          chain.doFilter(myrequest, response);
     }
   
      @Override
      public void init(FilterConfig filterConfig) throws ServletException {
     }
   
   }
   
   //自定义request对象，HttpServletRequest的包装类
   class MyRequest extends HttpServletRequestWrapper {
   
      private HttpServletRequest request;
      //是否编码的标记
      private boolean hasEncode;
      //定义一个可以传入HttpServletRequest对象的构造函数，以便对其进行装饰
      public MyRequest(HttpServletRequest request) {
          super(request);// super必须写
          this.request = request;
     }
   
      // 对需要增强方法 进行覆盖
      @Override
      public Map getParameterMap() {
          // 先获得请求方式
          String method = request.getMethod();
          if (method.equalsIgnoreCase("post")) {
              // post请求
              try {
                  // 处理post乱码
                  request.setCharacterEncoding("utf-8");
                  return request.getParameterMap();
             } catch (UnsupportedEncodingException e) {
                  e.printStackTrace();
             }
         } else if (method.equalsIgnoreCase("get")) {
              // get请求
              Map<String, String[]> parameterMap = request.getParameterMap();
              if (!hasEncode) { // 确保get手动编码逻辑只运行一次
                  for (String parameterName : parameterMap.keySet()) {
                      String[] values = parameterMap.get(parameterName);
                      if (values != null) {
                          for (int i = 0; i < values.length; i++) {
                              try {
                                  // 处理get乱码
                                  values[i] = new String(values[i]
                                         .getBytes("ISO-8859-1"), "utf-8");
                             } catch (UnsupportedEncodingException e) {
                                  e.printStackTrace();
                             }
                         }
                     }
                 }
                  hasEncode = true;
             }
              return parameterMap;
         }
          return super.getParameterMap();
     }
   
      //取一个值
      @Override
      public String getParameter(String name) {
          Map<String, String[]> parameterMap = getParameterMap();
          String[] values = parameterMap.get(name);
          if (values == null) {
              return null;
         }
          return values[0]; // 取回参数的第一个值
     }
   
      //取所有值
      @Override
      public String[] getParameterValues(String name) {
          Map<String, String[]> parameterMap = getParameterMap();
          String[] values = parameterMap.get(name);
          return values;
     }
   }
   ```

   web.xml中配置自定义过滤器

   ```xml
   <filter>
       <filter-name>encoding</filter-name>
       <filter-class>com.maowei.filter.Filter</filter-class>
   </filter>
   <filter-mapping>
       <filter-name>encoding</filter-name>
       <url-pattern>/*</url-pattern>
   </filter-mapping>
   ```



# 9. JSON交互处理

什么是JSON:

* JSON（Java Script Object Notation，JS对象标记）是一种轻量级的**数据交换格式**，目前使用非常广泛。

* 采用完全独立于编程语言的**文本格式**来存储和表示数据
* 简洁和清晰的层次结构使得JSON成为理想的数据交换语言
* 易于人阅读和编写，也易于机器解析和生成，并能有效地提升传输效率



在 JavaScript 语言中，一切都是对象。因此，任何JavaScript 支持的类型都可以通过 JSON 来表示，例如字符串、数字、对象、数组等。看看他的要求和语法格式：

- 对象表示为键值对，数据由逗号分隔
- 花括号保存对象
- 方括号保存数组

**JSON 键值对**是用来保存 JavaScript 对象的一种方式，和 JavaScript 对象的写法也大同小异，键/值对组合中的键名写在前面并用双引号 "" 包裹，使用冒号 : 分隔，然后紧接着值：

```jsp
{"name": "QinJiang"}
{"age": "3"}
{"sex": "男"}
```



 JSON 和 JavaScript 对象的关系，可以这么理解：

**JSON 是 JavaScript 对象的字符串表示法，它使用文本表示一个 JS 对象的信息，本质是一个字符串**。

**实质上JSON是一种特定格式的字符串**

```
var obj = {a: 'Hello', b: 'World'}; //这是一个对象，注意键名也是可以使用引号包裹的
var json = '{"a": "Hello", "b": "World"}'; //这是一个 JSON 字符串，本质是一个字符串
```



JSP中，**JSON 和 JavaScript 对象互转**

要实现从JSON字符串转换为JavaScript 对象，使用 JSON.parse() 方法：

```jsp
var obj = JSON.parse('{"a": "Hello", "b": "World"}');
//结果是 {a: 'Hello', b: 'World'}
```

要实现从JavaScript 对象转换为JSON字符串，使用 JSON.stringify() 方法：

```jsp
var json = JSON.stringify({a: 'Hello', b: 'World'});
//结果是 '{"a": "Hello", "b": "World"}'
```



# 9.1 从Controller返回JSON数据

在控制类的方法上使用 `@ResponseBody` 注解或者在控制类上用 `@RestController` 注解替换 `@Controller` 注解，**可以跳过视图解析器，即直接向前端返回JSON字符串或者普通的字符串，而不转发或者重定向到新的页面。**

这样后端只需编写接口向前端传数据，前后端分离的实现思想。



常用的JSON解析工具：

* Jackson
* 阿里巴巴的fastjson



使用Jackson：

1. 首先导入maven依赖

   ```xml
   <dependencies>
       <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
       <dependency>
           <groupId>com.fasterxml.jackson.core</groupId>
           <artifactId>jackson-databind</artifactId>
           <version>2.11.0</version>
       </dependency>
   ```

2. 配置SpringMVC需要的配置

   * web.xml配置
   * springmvc-servlet.xml配置

3. 创建实体类

   ```java
   public class User {
   
       private String name;
       private int age;
       private int id;
   }//构造器，getter，setter，toString方法省去
   ```

4. 编写Controller

   这里使用 `@ResponseBody` 注解或者用 `@RestController` 注解替换 `@Controller` 注解，可以跳过视图解析器，即直接向前端返回JSON字符串，而不转发或者重定向到新的页面。

   创建一个**ObjectMapper**对象，通过**ObjectMapper.writeValueAsString(Object object)**将对象转换成JSON字符串

   ```java
   package com.maowei.controller;
   
   import com.fasterxml.jackson.core.JsonProcessingException;
   import com.fasterxml.jackson.databind.ObjectMapper;
   import com.maowei.MyUtils.JsonUtils;
   import com.maowei.pojo.User;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   
   import java.util.ArrayList;
   import java.util.Date;
   import java.util.List;
   
   //@Controller
   @RestController
   public class JSONTest {
   
       @RequestMapping("/j1")
   //    @ResponseBody
       public String jsonTest() throws JsonProcessingException {
   
           ObjectMapper objectMapper = new ObjectMapper();
           User user = new User("徐茂蔚", 18, 1);
           //将Java对象解析成json字符串
           String json = objectMapper.writeValueAsString(user);
   
           return json;
       }
   
       @RequestMapping("/j2")
   //    @ResponseBody
       public String test2() throws JsonProcessingException {
           ObjectMapper mapper = new ObjectMapper();
           // 自定义日期格式对象
           SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
           // 自定义日期格式
           mapper.setDateFormat(sdf);
           Date date = new Date();
           return mapper.writeValueAsString(date);
       }
   
       @RequestMapping("/j3")
   //    @ResponseBody
       public String test3() throws JsonProcessingException {
           ObjectMapper mapper = new ObjectMapper();
           List<User> userList = new ArrayList<User>();
           User user1 = new User("1号", 18, 1);
           User user2 = new User("2号", 18, 2);
           User user3 = new User("3号", 18, 3);
           User user4 = new User("4号", 18, 4);
           userList.add(user1);
           userList.add(user2);
           userList.add(user3);
           userList.add(user4);
           return mapper.writeValueAsString(userList);
       }
   }
   
   ```

   返回的JSON字符串可能会出现乱码，可以通过Spring配置统一制定编码方式，来解决乱码问题。

   ```xml
   <mvc:annotation-driven>
      <mvc:message-converters register-defaults="true">
          <bean class="org.springframework.http.converter.StringHttpMessageConverter">
              <constructor-arg value="UTF-8"/>
          </bean>
          <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
              <property name="objectMapper">
                  <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                      <property name="failOnEmptyBeans" value="false"/>
                  </bean>
              </property>
          </bean>
      </mvc:message-converters>
   </mvc:annotation-driven>
   ```

   **转换日期时可能出现的问题**：

   - 默认日期格式会变成一个数字，是1970年1月1日到当前日期的毫秒数！
   - Jackson 默认是会把时间转成**timestamps**形式（时间戳）

   所以需要自定义日期格式(SImpleDateFormat)，当多个方法都需要转换日期时，重复编写比较麻烦，把公共部分的代码封装到工具类中

   ```java
   package com.maowei.MyUtils;
   
   import com.fasterxml.jackson.core.JsonProcessingException;
   import com.fasterxml.jackson.databind.ObjectMapper;
   import com.fasterxml.jackson.databind.SerializationFeature;
   
   import java.text.SimpleDateFormat;
   
   public class JsonUtils {
   	
       //重载
       public static String getJSON(Object object) {
           return getJson(object, "yyyy-MM-dd HH:mm:ss");
           // 默认的日期格式
       }
   
       public static String getJson(Object object, String dateFormat) {
           ObjectMapper objectMapper = new ObjectMapper();
           // 不使用时间差的方式
           objectMapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
           // 自定义日期格式对象
           SimpleDateFormat simpleDateFormat = new SimpleDateFormat(dateFormat);
           // 指定日期格式
           objectMapper.setDateFormat(simpleDateFormat);
           try {
               return objectMapper.writeValueAsString(object);
           } catch (JsonProcessingException e) {
               e.printStackTrace();
           }
           return null;
       }
   }
   
   ```

   最后调用工具类的方法，Controller中的代码简化为

   ```java
    @RequestMapping("/j2")
   //    @ResponseBody
       public String test2() throws JsonProcessingException {
           Date date = new Date(); //只需要传入日期和日期格式即可，不用再编写重复的代码
           return JsonUtils.getJSON(date);
       }
   ```

   

# 10. SSM整合

   **调用流程：**

   ![image-20200804113426922](D:\Typora\笔记图片\SpringMVC笔记\image-20200804113426922.png)

**SpringMVC层**：Controller调用Service实现类的Bean，并向Service实现类自动注入Dao的的实现类；

**Service层**：Service实现类中有一个Dao接口对象，目的是调用Dao实现类，从而读取数据

**Dao层**：Dao实现类负责从数据库读取数据

具体代码见工程sssmbuild

**注意点：**

需要在Project Structure ---> artifact 目录下新建一个lib文件夹，并把所有jar包添加进去，不然会报ClassNotFound错误，并且网页请求会返回404.



# 11. 需要掌握的前端知识

HTML + CSS：略懂 + JS（熟练）

js：

* 函数：闭包
* Dom
  * id，name，tag
  * create，remove
* Bom
  * window
  * document