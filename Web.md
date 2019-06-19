# 1.  Web开发学习路线
1. HTTP
2. 前端开发
   1. HTML & CSS
   2. 脚本语言 & JavaScript & JS框架
   3. React框架 & Vue框架
3. 后端开发
   1. Servlet
   2. Java访问数据库-JDBC
   3. Java访问数据库-ORM
4. 前后端集成
   1. Ajax & JSON
5. 框架运用
   1. Spring 服务
   2. MVC 架构
6. 管理工具
   1. Maven
   2. Webpack
7. NoSQL-MongoDB
8. 微信小程序
9.  Android & iOS

# 2. Web应用的发展历史
1. 什么是Web application?
   - 是一个**计算机程序**，允许用户使用**自己喜欢的浏览器**，通过**互联网**，从**数据库**中，**提交和访问数据**。
   - Static Website
    Browser从Web server(Apache)拿到静态的HTML页面
   - Web 1.0
     - 基于CGI(Common Gateway Interface公共网关接口)与文件系统的交互，使用ASP.NET/JSP开发，实现了动态的web应用
     - 不分前后端
   - Web 2.0
     - Ajax的出现和计算能力的增强，使得浏览器包含了更多的代码；
     - CDN(Content Distribute Network内容分发网络)；
     - MVC模式的应用提高了可维护性
       - 后端：Model-Beans，View-JSP，Controller-Servlet
     - MVC和Ajax联合后，组装页面的工作交给了前端，后端的View消失，前端出现了MVC,前后端使用JSON通信
       - 前端：Model-JSON Data，View-Templates，Controller
   - Full-stack JavaScript & REST API
     - Node.js
     - MongoDB-JSON
   - HTML5+CSS3
   - Apache HTTP Server
   - PHP
     是一种尤其适合网站开发的脚本语言
   - Ruby on Rails
   - Web Frameworks for Python
     - Django
     - TurboGears 2
     - Web2py
   - node.js
     轻量级框架，生态很好，新兴技术
   - Windows .NET
     重量级框架
   - Erlang,Scala,Go

# 1. HTTP协议
- 是一种request-response的协议
- browser是一种UA(user agent用户代理)
- URL(Uniform Resource Locators)
  - URI = URL + URN
  - scheme:[//[user:password@]host[:port]][/]path[?query][#fragment]
  - hash(#)可以用于单页面内的路由和定位
- Request Methods

HTTP Method|Request Has Body|Response Has Body|Safe|Idempotent|Cacheable
:-:|:-:|:-:|:-:|:-:|:-:
GET|N|Y|Y|Y|Y
HEAD|N|N|Y|Y|Y
POST|Y|Y|N|N|Y
PUT|Y|Y|N|Y|N
DELETE|N|Y|N|Y|N
CONNECT|Y|Y|N|N|N
OPTIONS|Optional|Y|Y|Y|N
TRACE|N|Y|Y|Y|N
PATCH|Y|Y|N|N|Y

- HTTP session
    - TCP三次握手(SYN, SYN-ACK, ACK)
      - 第一次握手：客户端发送syn包(seq=x)到服务器，并进入SYN_SEND状态，等待服务器确认;
      - 第二次握手：服务器收到syn包，必须确认客户的SYN(ack=x+1)，同时自己也发送一个SYN包(seq=y)，即SYN+ACK包，此时服务器进入SYN_RECV状态;
      - 第三次握手：客户端收到服务器的SYN+ACK包，向服务器发送确认包ACK(ack=y+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。
    - HTTP是无状态的协议：Server端的HTTPSession存放了相关信息，然后把SessionID作为cookie传回给Client端，之后Client端带着cookie访问Server并识别谁是谁
    
    **Client request**
    ``` 
    GET /index.html HTTP/1.1	
    Host: www.example.com
    ```
    **Server response**
    ```
    HTTP/1.1 200 OK	
    Date: Mon, 23 May 201822:38:34 GMT 
    Content-Type: text/html; charset=UTF-8	
    Content-Encoding: UTF-8	
    Content-Length: 138	
    Last-Modified: Wed, 08 Jan 201823:11:55 GMT 
    Server: Apache/1.3.3.7	(Unix)	(Red-Hat/Linux)	
    ETag: "3f80f-1b6-3e1cb03b"	
    Accept-Ranges:	bytes	
    Connection:	close	
    
    <html>
        <head>
        	<title>An Example Page</title>
        </head>
        <body> Hello World, this is a very simple HTML document.</body>
    </html>
    ```

# 2. HTML
- Hypertext Markup Language
- HTML tags + text = HTML Document => Web Browser => Webpage

# 3.CSS
- Cascading Style Sheet
  
# 4.JavaScript
- script是运行前不需要预处理的程序代码
- 基于DOM树操纵html页面
- JavaScript是ECMAScript中最著名的实现
- 独立于平台的
- 需要考虑各种不同浏览器、不同语言版本的兼容问题。解决方案：
  - 最小公分母的方案
  - 测试特性是否存在
  - 写特定平台下的代码
  - 利用语言特性
  - 显式版本测试
  - 优雅报错
- Closure Compiler: 优化代码，去除冗余
- AngularJS
- jQuery
- BootStrap
- React

# 5. React
- 出现的背景：
  - 新型网页开发模型SPA(单页模型)，只需拉取必要的数据在brower端进行绘制，减少了和服务端的延迟
    - 缺陷：
      1. 如何保持数据和UI同步更新
      2. 如何提高DOM操作的效率
      3. 使用HTML开发UI界面异常复杂
  - 解决方案：
    1. 自动化的UI管理，数据和界面的同步，把数据变化转化为一系列事件，开发者只需根据事件转换界面的状态，实现了数据和UI的解耦
    2. 更高效的DOM操作，内存中的Virtual DOM进行缓存，定期对实际中DOM进行更新
    3. UI的组件化设计，大量可重用的组件
    4. 依赖JS开发UI界面，创新了JSX看起来像HTML，但本质上会解读为一系列DOM操作，简化了代码逻辑
- 本质是MVC中的View，将数据和UI结合
- props和state
  - function and class必须永不修改自己的props；所有的components必须像pure functions一样尊重它们的props
  - State与props相似，但它是私有的，并且完全由component自己控制；local state可以抽象为仅对class可见的特性
  - state的更新是异步的，为了提高性能可能会打包处理
- 生命周期
  - WillMount
  - DidMount
  - WillUnmount
  - WillUpdate
  - DidUpdate
  - ShouldUpdate
  - WillReceiveProps
    ![enter image description here](https://upload-images.jianshu.io/upload_images/5287253-bd799f87556b5ecc.png)
- 组件化的思想
  1. 代码重用
  2. 减少耦合
  3. 降低复杂度

# 6. Vue
- 与React是类似的
- MVVM(Model-View-ViewModel)
  - MV*针对的是：GUI程序
  - MV*的基本模式是：Model异步通知View变更，View告知Model操作逻辑
  - MV*解决的问题是：View如何同步Model的变更
  - MVC中View把控制器交给Controller，Controller操纵Model，Model和View同步消息采用观察者模式![enter image description here](https://camo.githubusercontent.com/b89ac314c2fd554e7bf33ba1553e10dd91be44fc/687474703a2f2f6c69766f7261732e6769746875622e696f2f626c6f672f6d76632f6d76632d63616c6c2e706e67)
  - Web服务端的MVC事实上是JSP Model 2，在这种情况下，观察者模式被迫打破，必须由View主动发出请求![enter image description here](https://camo.githubusercontent.com/72de7a4e8054e95ede1f0d167b603119a82efec1/687474703a2f2f6c69766f7261732e6769746875622e696f2f626c6f672f6d76632f6a73702e706e67)
  - MVP中把Controller换成了Presenter，打破View和Model之间的依赖关系，View只需提供接口，全权由Presenter负责同步和逻辑![enter image description here](https://camo.githubusercontent.com/082052805716330b7c168b8bcd968ffb085b4c21/687474703a2f2f6c69766f7261732e6769746875622e696f2f626c6f672f6d76632f6d76702d63616c6c2e706e67)
  - MVVM是一种特殊的MVP，在ViewModel中新增了Binder(或称数据绑定引擎)，实现了View和Model双向数据绑定的自动化。![enter image description here](https://camo.githubusercontent.com/61ef7578cd46b1d37dd3ea52ce0a3be570e427cc/687474703a2f2f6c69766f7261732e6769746875622e696f2f626c6f672f6d76632f6d76766d2d63616c6c2e706e67)


# 7. Servlet
- 本质是是一个Java类，受控于Container，用于基于request-response的server，HttpServlet是针对Web应用的特殊化。
- Web Server
  - 使用HTTP协议来传输数据
- Servlet Container
  - 本质是一个Java程序，是用来装Servlet的
  - 基本思想是在服务器端使用Java来动态生成网页
  - 控制Servlet对象的生命周期(类加载、实例化...)，调用相应的方法和传递request&response实体，如init(),service(),destroy()
  ![enter image description here](https://images2015.cnblogs.com/blog/1012728/201706/1012728-20170618230610040-1206805475.png)![enter image description here](https://img-blog.csdn.net/20140119021418375?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc25hcmxmdXR1cmU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
- Tomcat
  - Tomcat 的容器等级中，Context 容器是直接管理 Servlet 在容器中的包装类 Wrapper，真正管理 Servlet 的容器是 Context 容器
![enter image description here](https://camo.githubusercontent.com/34f3fd1a4e265dd73b29455acf7ff1dd7898aeb1/687474703a2f2f7777772e69626d2e636f6d2f646576656c6f706572776f726b732f636e2f6a6176612f6a2d6c6f2d736572766c65742f696d6167653030322e6a7067)
- Filter：是一个对象，可以对指定url的requset&response处理其header&content
- HttpSession: 相同Web context下的任何组件都可以访问session内容，并处理同一会话的内容
- 上传文件：MIME type - multipart/form-data

# 8. Access to RDBMS
- JDBC：提供了用Java访问关系数据库的API
- 基本流程
  - 建立Connection
    - DriverManager
    - Datasource：使用JNDI(Java Naming and Directory Interface)
  - 执行SQL Statement并操纵Result
    - DatabaseMetadata
    - Statement(直接写SQL)，PreparedStatement(设置输入参数)，CallableStatement(调用Procedure获得返回值)
    - ResultSet，RowSet
- Context：就是容器，是一个对象，包含一系列Binding(name,object)的集合
- JDBC 的优点
  - 性能好，尤其适合访问大量数据
  - 利用了DBMS提供的各种function
  - 可以使用procedure完成负责逻辑
- JDBC 的缺点
  - 与DBMS紧耦合
  - 与数据结构紧耦合
  - 编程很复杂
- 为解决以上缺点提出了O/R Mapping
- ORM 的优点
  - 独立于DBMS
  - 独立于数据结构
  - OOP(面向对象编程)
- ORM 的缺点
  - 影响性能
  - 不能利用额外的function
  - O和R的映射可能很复杂
  - 不能调用procedure
- JDBC reading v.s. ORM： Requirement Driven

# 9. AJAX
- AJAX = Asynchronous JavaScript and XML
- XMLHttpRequest是Ajax的基础，实现了数据传输
- Ajax的request由JavaScript代码触发，并通过触发回调函数接受response
- GET/POST的选择基于是否改变数据，GET可能被browser缓存，通常以query string的方式发送数据；POST通常不会被缓存，更倾向于以post data的方式发送数据
- jQuery 提供Ajax支持，抽离出浏览器的差异

# 10. JSON
- 是一种基于文本的数据交换格式，由JavaScript而来，被用于web服务与其他应用连接
- 两种数据结构：object(键值对)和array(一组值)
- 六种数据类型：string,number,object,array,true&false and null
- JSON的序列化与反序列化
  - object model：在内存中创建一颗代表JSON数据的树
  - streaming model：使用一种基于事件的解析器，一次只读一个JSON数据

# 11. ORM
- entity是一个由Hibernate持有的常规Java对象(POJO-Plain Old Java Object)
- 继承策略
    1. table per subclass - 每个子类一张表，继承关系通过JOIN实现，需要同时操作子类和所有父类，通过外键标识继承关系
        - 关系模型完全标准化
        - 策略复杂，性能很低，需要大量JOIN
    3. table per concrete class - 每个具体类一张表，每张表包含所有父类字段
        - 有冗余
        - 完全抛弃继承关系，不支持多态
        - 对父类的查询，需要检索所有子类
        - 父类的修改会导致所有子类的修改
        - 除非必要，否则绝不推荐
    4. table per class hierarchy - 单表保存所有基类和子类的字段，用一个专门的列来标识类别，支持了多态
        - 最简单，性能最好
        - 子类的属性在数据库中必须被定义允许为空，失去对Not Null的约束
- Hibernate 对象状态
  - Transient：没有持久化标识OID，没有被Session管理
  - Persistent：有持久化标识OID，已经被Session管理
  - Detached：有持久化标识OID，没有被Session管理
  - 执行save()后的持久态对象只是放到Session中管理，同步到数据库要等到事务提交。提交前会比对一级缓存和snapshot，需要时才更新数据库。
  - ![enter image description here](https://images2018.cnblogs.com/blog/1283465/201711/1283465-20171123210911609-330723443.png)
- flush Session默认发生在：
  - 一些query执行前
  - org.hibernate.Transaction.commit()
  - Session.flush()
- SQL Statement被提交的顺序
  1. entity insert按照Session.save()的顺序
  2. entity update
  3. collection deletions
  4. collection element deletion,update,insertion
  5. collecion insertions
  6. entity delete按照Session.delete的顺序
- 级联持久化