---
layout: post
title: CGI、FastCGI、PHP-FPM、Reactor、I/O复用模型
category: "CGI"
tags: [CGI, PHP-FPM]
---
CGI、FastCGI、PHP-FPM、Reactor、I/O复用模型

#### CGI `common gateway interface (公共网关接口)`

> 请求模式:
        Web Brower(浏览器) ----(通过http协议传输)----> Http Server(服务器nginx/apache) -----> CGI Program -----> Db
>
> Server 与 CGI 通过 STDIN/STDOUT(标准的输入/输出)进行数据传递
> 
> nginx(动态加载模块) apache(指定加载模块)

#### CGI工作原理
>每当客户请求CGI的时候，WEB服务器就请求操作系统生成一个新的CGI解释器进程(如php-cgi.exe)，
> 
>CGI 的一个进程则处理完一个请求后退出，下一个请求来时再创建新进程。
> 
>当然，这样在访问量很少没有并发的情况也行。可是当访问量增大，并发存在，这种方式就不 适合了。于是就有了fastcgi。
	
	        单个请求,多个请求重复操作
                    (web)
                      ↓
                   (server)
                      ↓
                (CGI解释器进程)              
                      ↓
                 (处理并返回)

#### FastCGI

>	像是一个常驻(long-live)型的CGI，它可以一直执行着，只要激活后，
	不会每次都要花费时间去fork一次（这是CGI最为人诟病的fork-and-execute 模式）。
    
> 一般情况下，FastCGI的整个工作流程是这样的：
> 
> 1.Web Server启动时载入FastCGI进程管理器（IIS ISAPI或Apache Module)
> 
> 2.FastCGI进程管理器自身初始化，启动多个CGI解释器进程(可见多个php-cgi)并等待来自Web Server的连接。
> 
> 3.当客户端请求到达Web Server时，FastCGI进程管理器选择并连接到一个CGI解释器。 Web server将CGI环境变量和标准输入发送到FastCGI子进程php-cgi。
> 
> 4.FastCGI 子进程完成处理后将标准输出和错误信息从同一连接返回Web Server。
> 
> 当FastCGI子进程关闭连接时， 请求便告处理完成。
> 
> FastCGI子进程接着等待并处理来自FastCGI进程管理器(运行在Web Server中)的下一个连接。
> 
> 在CGI模式中，php-cgi在此便退出了。
         
        |           server
        |---------------------------------  
        |  (server start)                |
        |         ↓                      |
        | (FastCGI进程管理器)          ↙   ←  (请求来了)   ←      (web)
        |         ↓  等待请求  → (接活了) → (php-cgi1接客)           
        | (php-cgi1,php-cgi2,php-cgi3,..)|    ↓
        |--------------------------------|(处理完成返回)
        |         ↑ 其它羡慕了                  ↓
        |         ←   (干活真开心:)  ←     (php-cgi1回来了)    

#### php-fpm(PHP内置的一种fast-cgi)
    php-fpm即php-Fastcgi Process Manager.
    php-fpm是 FastCGI 的实现，并提供了进程管理的功能。
    进程包含 master 进程和 worker 进程两种进程。
    master 进程只有一个，负责监听端口，接收来自 Web Server 的请求，
    而 worker 进程则一般有多个(具体数量根据实际需要配置)，
    每个进程内部都嵌入了一个 PHP 解释器，是 PHP 代码真正执行的地方。
    Nginx和PHP-FPM的进程间通信有两种方式: 一种是TCP、一种是UNIX Domain Socket。
    其中TCP是IP加端口，可以跨服务器。而UNIX Domain Socket不经过网络，只能用于Nginx跟PHP-FPM都在同一服务器的场景。

#### PHP生命周期

     PHP程序的启动
                  前置初始化(Apache或Nginx相关操作)
                  模块初始化       对应扩展 php.dll
                  请求初始化       $_SERVER等参数      I
          frame   执行php脚本      code               I   I可以重复执行(一般为框架内容)
                  请求处理完成     request            I
                  关闭模块        close
    
     Apache:
           A: php作为Apache的一个模块的启动和终止.
              这次php会初始化一些必要的数据(PHP_MINIT_FUNCTION),比如和Apache有关的,这些数据时常驻内存的!终止与之对应.
           B: Apache分配一个页面请求过来的时候,php会有一次启动和终止
    
     PHP扩展周期:
          http://www.cunmou.com/phpbook/1.md
          Module init、Request init、Request Shutdown、Module shutdown 四个过程
          具体的执行顺序如下

![PHP_TIME](/assets/image/php_time.png)

#### 请求步骤
    Web Brower(浏览器访问) www.example.com
    |
            |
       通过http协议传输  
    |
            |
        http server
     (服务器nginx/apache)            
    |
            |
         配置解析    
    路由到 www.example.com/index.php(根据配置加载,如果是静态文件index.html不会走这里)
    |
            |
    加载 nginx 的 fast-cgi 模块 (ngx_http_fastcgi_module),配置基于(nginx.conf)
    |
            |
    fast-cgi 监听 127.0.0.1:9000 地址
    通过 fast-cgi 协议将请求转发给 php-fpm 处理,配置基于(php-fpm.conf)
    |
            |
    请求到达 127.0.0.1:9000 (接收到请求转发到php-fpm)
    |
            |
    php-fpm 监听 127.0.0.1:9000
    可通过 php-fpm.conf 进行修改
    (php-fpm 是一个多进程的 fast-cgi 管理程序)
    |
            |
    php-fpm 接收到请求，启用 worker 进程处理请求
    (worker进程 会抢占式的获得 cgi 请求进行处理)
    |
            |
        PHP解释器 处理请求
    |       
            |
         处理详解
            |
    |处理过程:等待 php 脚本的解析,等待业务处理的结果返回,
    |       完成后回收子进程,这整个的过程是阻塞等待的.
    |处理弊端:也就意味着 php-fpm 的进程数有多少能处理的请求也就是多少,
    |       假设 php-fpm 有 200 个 worker进程,一个请求将耗费 1 秒的时间,
    |       那么简单的来说整个服务器理论上最多可以处理的请求也就是 200 个,QPS 即为 200/s.
    |       在高并发的场景下，这样的性能往往是不够的，尽管可以利用 nginx 作为负载均衡配合多台 php-fpm 服务器来提供服务，
    |       但由于 php-fpm 的阻塞等待的工作模型，一个请求会占用至少一个 MySQL 连接，
    |       多节点高并发下会产生大量的 MySQL 连接，而 MySQL 的最大连接数默认值为 100，尽管可以修改，
    |       但显而易见该模式没法很好的应对高并发的场景
            |
    |   PHP生命周期(宏都是在walu.c中)           
    |              前置初始化(Apache或Nginx相关操作)
    |              模块初始化       对应扩展 php.dll
    |              请求初始化       $_SERVER等参数    [  
    |      frame   执行php脚本      code                可以重复执行(一般为框架内容)
    |              请求处理完成      request          ]
    |              关闭模块         close
    |
           |
        其它内容   
    |
           |         
    nginx 将结果通过 http 返回给浏览器

#### I/O多路复用机制

    I/O多路复用 : 每个进程/线程同时处理 多个连接(I/O多路复用)

    利用epoll来实现IO多路复用,将连接信息和事件放到队列中,依次放到文件事件分派器,事件分派器将事件分发给事件处理器.
    
    c10k：https://www.jianshu.com/p/ba7fa25d3590
    
    用户A-Z -> I/O多路复用(s0,s1,s2,s3) -依次放到-> 文件事件分派器 -> 事件处理器(连接应答处理器1...)

* c10k(一瞬间1w请求)
~~~
早期的一个TCP请求,就会创建一个进程(或线程),而进程属于操作系统,是非常昂贵的资源,并且有数量限制.
创建的进程线程多了,数据拷贝频繁（缓存I/O、内核将数据拷贝到用户进程空间、阻塞）,
进程/线程上下文切换消耗大, 导致操作系统崩溃,这就是C10K问题的本质！
~~~  

* 简单说下epoll(基于Linux)
~~~
支持一个进程打开大数目的socket描述符(fd)
只对发生变化的文件句柄感兴趣,工作机制类似于"事件"
通过 epoll_create 创建 epoll对象,
linux内核会创建一个eventpoll结构体(
 重要的两个成员:
    红黑树的根节点: 这棵树中存储着所有添加到epoll中的事件，也就是这个epoll监控的事件。
    双向链表rdllist: 保存着将要通过epoll_wait返回给用户的、满足条件的事件。  
)
通过 epoll_ctl 注册文件描述符fd,一旦该fd就绪,
内核就会采用类似 callback 的回调机制来激活该fd, 
epoll_wait 便可以收到通知, 并通知应用程序.
~~~


* 简单示例
    
      I/O多路复用(又被称为“事件驱动”)
      one, two, ..., six 和 ser 进行聊天
      1.逐个询问是否有聊天信息,如果 one 有,那 ser 处理 one 的信息,其它的就卡住.(阻塞)
      2.ser 创建多个分身,即为每个用户创建个进程或者线程.(消耗过大)
      3.one, two 主动告诉 ser 有信息, ser 依次进行处理, 然后等待其它人的通知.(非阻塞模式)
    
                                                tcp(ser)
    
      one(fd)(client socket)  - 注册入epoll ->  建立callback event    ->  有变化(fd) -> epoll收到通知 -> 告诉tcp进行处理
    
      two(fd)(client socket)  - 注册入epoll ->  建立callback event    ->  非阻塞 
    
      six(fd)(client socket)  - 注册入epoll ->  建立callback event    ->  非阻塞

