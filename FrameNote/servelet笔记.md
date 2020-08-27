[toc]

# servlet笔记

## 服务器概念

服务器其实就是代码编写的一个可以根据用户请求实时地调用对应的逻辑代码的一个容器。在普通用户看来就是一个安装程序。我们只要将服务器在操作系统上进行安装，并将事先编写好的逻辑处理代码根据规则放到服务器的指定位置，启动服务器，那么服务器就自动会根据接收到的请求调用并执行对象的逻辑代码进行处理。

Tomcat就是一个服务器软件，基于JDK运行，需要有Java环境。



## servlet概念

狭义的servlet指Java语言实现的一个就扣，广义的servlet**指任何实现了这个servlet接口的类**。一般情况下，理解为后者。servlet运行于支持Java的应用服务器中。从原理上讲，servlet可以响应任何类型的请求，但绝大多数情况下servlet只是用来扩展基于HTTP协议的Web服务器。



servlet接口规定了相应的方法，开发者根据接口在，自己的实现类中覆写这些方法，实现对不同请求执行不同的逻辑代码。



* 使用：

  1. 创建普通的Java类,**继承HttpServlet类**
  2. 覆写不同的方法(service(),doGet(),doPost(),destory())，编写逻辑代码
  3. 在web.xml中配置servlet

* 运行流程：

  ​	浏览器发送请求到服务器，服务器根据请求URL地址中的URI信息（URL中去掉IP地址和端口号以外的信息为**请求URI**），在webapps目录下找到对应的项目文件夹，然后在web.xml中检索对应的servlet，找到后调用并执行servlet。



**在web.xml文件中配置servlet实现类和访问路径**

```xml
<!--配置servlet-->
    <!--配置servlet类路径-->
    <servlet>
        <servlet-name>MyServlet</servlet-name>
        <servlet-class>com.maowei.MyServlet</servlet-class>
    </servlet>
    <!--配置访问方式-->
    <servlet-mapping>
        <servlet-name>MyServlet</servlet-name>
        <url-pattern>/myServlet</url-pattern>
    </servlet-mapping>
<!--两个servlet-name可以随意填写，但是必须保持一致，
	说明实现这个实现类和这个访问路径是匹配的
-->
```





## servlet生命周期

**servlet的一个生命周期**：

	1. 从第一次调用到服务器关闭
 	2. 如果Servlet在web.xml中配置了`<load-on-startup>1</load-on-startup>`，标签中的数字为服务器中不同的servlet的加载顺序，生命周期为从服务器启动到服务器关闭。



### init()方法

servlet第一次调用（加载进内存的时候）的时候执行一次,初始化操作操作在这里进行，以后都不再执行。



### service()方法

服务器收到请求（GET或POST）之后，如果存在service()方法，优先执行service()方法，service()调用父类的service()方法```super.service()```，把请求转发给对应的方法。

如果没有`super.service()`，则不会调用父类service方法，相应的doGet和doPost方法就不会执行。

不调用父类的service方法也是可以的，通过在service()方法中判断请求的类型来做不同的处理。这样做可以避免servlet中并没有编写doGet或doPost方法而出现的405错误。

比如：

​	将POST请求转发给`doPost()`方法，将GET请求转发给`doGet()`方法



### destroy()方法

在servlet销毁后执行改方法（服务器关闭的时候）。不经常用到。



### doGet()方法

收到GET请求后，服务器完成的动作

**注意**：重写`doGet()`不需要调用`super.doGet(req,resp)`方法，否则会报405错误

### doPost()方法

收到POST请求后，服务器完成的动作。

**注意**：重写`doPost()`不需要调用`super.doPost(req,resp)`方法，否则会报405错误



## servlet的常见错误

### 404错误：资源未找到

* 原因一：在请求地址中servlet的别名书写错误（对应资源的urlpattern）
* 原因二：虚拟项目名称拼写错误



### 500错误：java.lang.ClassNotFoundException:com.maowei.MyServlet

* 原因一：有可能是在web.xml里的servlet配置中的全限定路径拼写出错

`<servlet-class>com.maowei.Myservleet</servlet-class>`

* `Service()`方法体中有代码执行错误，根据控制台提示更改代码



### 405错误：请求方式不支持

* 原因：

  请求方式和servlet中的方法不匹配。如，发送GET请求但是没有doGet方法

* 解决：

  尽量使用service方法进行请求处理，并且不要在service方法中调用父类的service方法



## Request对象

服务器接收到浏览器的请求后，通过HTTP协议，服务器会根据接收到的请求创建一个Request对象，对象中存储了此次请求相关的请求数据。服务器在调用Servlet时会将创建的Request对象作为实参传递给Servlet中的方法，比如service方法。

其中，请求数据包括：

* **获取请求行数据**

  * 获取请求方式

    `String method = req.getMethod();`。返回具体的请求方式，如：GET,POST

  * 获取请求URL和URI

    `StringBuffer url = req.getRequestURL);`

    `String uri = req.getRequestURI();`

  * 获取协议版本

    ·`String scheme = req.getScheme();`

* **获取请求头数据（以键值对的形式存在）**

  * 获取指定键的数据

    `String value = req.getHeader(String key); //key是String类型`，如果不存在这个键，则返回null

  * 获取所有请求头中所有键的枚举

    `Enumeration e = req.getHeaderNames();`

* **获取用户数据**

  不论是GET请求，还是POST请求，数据都经过HTTP协议解析，request中包装的用户数据都是键值对。

  * `String value = req.getParameter(String key);`

    如果返回的值是null，很可能的情况是发送到服务器的请求中并没有这个键。

    注意点：`req.getParameter()`方法在同键多值（一个键对应多个值）的情况下，只能获取到一个值

  * `String[] values = req.getRequestValues(String key)`

    使用req.getRequestValues()方法可以获取一个键中对应的所有值，并返回数组。

  * `Enumeration e = req.getParameterNames()`

    返回请求数据中所有的键构成的枚举，和req.getHeaderNames()用法相同,一般不会用到这个用法，一般来说，开发的时候键都是知道的。

![HTTP协议](E:\typora\笔记图片\servelet笔记\image-20200601150056178.png)



## Response对象

在使用Request对象获取了请求数据并进行处理后，使用Response对象将结果显示到浏览器中（即从服务器到客户端）。

* **设置响应头**

  `resp.setHeader(String key,String value);`。设置HTTP协议响应头中的键值。同键会覆盖。

  `resp.addHeader(String key,String value);`。与setHeader不同的是，addHeader在设置响应头时，同键不会覆盖。

  * 其中设置**响应编码格式**的函数为，两者效果相同

    `resp.setHeader("content-type","text/html;charset=utf-8")`;

    `resp.setContentType(text/html;charset=utf-8");`表示按html格式解析resp.getWriter()返回的文本。

    `resp.setContentType(text/plain;charset=utf-8");`这种方式表示返回的是纯文本。换成xml则会按xml格式解析文本。

    

  `resp.sendError(404,"sorry");`。设置响应状态码。直接向客户端返回404错误，并且附带的信息是sorry。即使资源存在，也可以使用sendError方法强制返回错误，看起来就像资源不存在。

* **设置响应实体**

  ```java
  PrintWriter writer = resp.getWriter();
  writer.wrtie(String msg);
  writer.println(String msg);
  //上面两句都可以在客户端上打印想要返回的消息
  ```

  

## 总结，servlet请求处理流程

* 设置响应编码格式
* 获取请求数据
* 处理请求数据
* 响应处理结果