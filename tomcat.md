 Servlet 容器
## Servlet 是干什么的？
### 产生的原因是什么？
用户已经不满足于仅浏览静态页面。用户需要一些交互操作，获取一些动态结果
CGI响应时间较长
所以sun推出Servlet
### 怎么实现的？
Java Servlet（Java服务器小程序）是一个`基于Java技术的Web组件`
运行在服务器端，它由Servlet容器所管理
 Servlet是平台独立的Java类，编写一个Servlet，实际上就是按照Servlet规范编写一个Java类
 被动态地加载到支持Java技术的Web服务器中运行
### 做什么的？
生成动态的内容

### 特点
Servlet`没有main方法`，不能独立运行，它必须被`部署到Servlet容器中`，由`容器来实例化和调用` Servlet的方法（如doGet()和doPost()），Servlet容器在Servlet的生命周期内`包容和管理`Servlet

### Servlet容器
是Web服务器或应用程序服务器的一部分
容器来实例化和调用` Servlet的方法（如doGet()和doPost()
Servlet容器在Servlet的生命周期内`包容和管理`Servlet
### 功能：
用于在发送的请求和响应之上提供网络服务

### servlet 怎么用的？
1. `用户`通过单击某个链接或者直接在浏览器的地址栏中输入URL来`访问Servlet`
2. `Web服务器 `接收到该请求后，并不是将 请求直接交给Servlet，而是`交给Servlet容器`
3. Servlet容器`实例化`Servlet，`调用`Servlet的一个特定方法对`请求进行处理`， 并产生一个`响应`。
4. 这个响应由`Servlet容器返回`给Web服务器，Web服务器包装这个响应，以HTTP响应的形式发送给Web浏览器。

参考：https://www.cnblogs.com/widget90/p/5640359.html

# tomcat
是一种Servlet容器
### Tomcat服务器接受客户请求并做出响应的过程如下：【】也就是servlet荣去的过程

1. 客户端（通常都是浏览器）访问Web服务器，发送HTTP请求。
2. Web服务器接收到请求后，传递给Servlet容器【tomcat】。
3. Servlet容器加载Servlet，产生Servlet实例后，向其传递表示请求和响应的对象
4. Servlet实例使用请求对象得到客户端的请求信息，然后进行相应的处理。
5. Servlet实例将处理结果通过响应对象发送回客户端，容器负责确保响应正确送出，同时将控制返回给Web服务器。

