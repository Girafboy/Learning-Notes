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