---
title: echo服务实现 - muduo入门项目
date: 2023-12-19 20:29:00
tags: [muduo]
categories: [C++项目,muduo]
mathjax: false
---

### 1. muduo安装

[参考链接](https://blog.csdn.net/PinkFriday/article/details/78523260)

### 2. 编译

参考同上

### 3. 编写echo服务代码

muduo作者写的三个半：

>[https://blog.csdn.net/Solstice/article/details/6171831](https://blog.csdn.net/Solstice/article/details/6171831)
>
>我认为，TCP 网络编程最本质的是处理三个半事件：
>
>1. 连接的建立，包括服务端接受 (accept) 新连接和客户端成功发起 (connect) 连接；
>2. 连接的断开，包括主动断开 (close 或 shutdown) 和被动断开 (read 返回 0)；
>3. 消息到达，文件描述符可读。这是最为重要的一个事件，对它的处理方式决定了网络编程的风格（阻塞还是非阻塞，如何处理分包，应用层的缓冲如何设计等等）；
>4. 消息发送完毕，这算半个。对于低流量的服务，可以不必关心这个事件；另外，这里“发送完毕”是指将数据写入操作系统的缓冲区，将由 TCP 协议栈负责数据的发送与重传，不代表对方已经收到数据。

<!-- more -->

*注：本代码引用库函数为系统库引用方式（详见参考链接），路径方式需修改

muduo采用的是2023/12/15日在github上的版本（两年前更新？）

三个文件：

echo.h:：定义 EchoServer class；

echo.cc：在构造函数里注册回调函数，实现 EchoServer::onConnection() 和 EchoServer::onMessage()；

main.cc：用 EventLoop 让整个程序跑起来。

#### echo.h  

```c++
#ifndef MUDUO_EXAMPLES_SIMPLE_ECHO_ECHO_H
#define MUDUO_EXAMPLES_SIMPLE_ECHO_ECHO_H

#include<muduo/net/TcpServer.h>

//RFC 862
class EchoServer
{
public:
    EchoServer(muduo::net::EventLoop* loop,
               const muduo::net::InetAddress& listenAddr);

    void start(); // calls server_.start();

private:
    void onConnection(const muduo::net::TcpConnectionPtr& conn);

    void onMessage(const muduo::net::TcpConnectionPtr& conn,
                   muduo::net::Buffer* buf,
                   muduo::Timestamp time);

    muduo::net::TcpServer server_;
};

#endif  // MUDUO_EXAMPLES_SIMPLE_ECHO_ECHO_H
```

#### echo.cc

```c++
#include"echo.h"
#include<muduo/base/Logging.h>

using std::placeholders::_1;
using std::placeholders::_2;
using std::placeholders::_3;

EchoServer::EchoServer(muduo::net::EventLoop* loop,
               const muduo::net::InetAddress& listenAddr)
            : server_(loop, listenAddr, "EchoServer")
{
    server_.setConnectionCallback(
        std::bind(&EchoServer::onConnection, this, _1));
    server_.setMessageCallback(
         std::bind(&EchoServer::onMessage, this, _1, _2, _3));
}

void EchoServer::start()
{
    server_.start();
}

void EchoServer::onConnection(const muduo::net::TcpConnectionPtr& conn)
{
    LOG_INFO << "EchoServer - " << conn->peerAddress().toIpPort() << "->"
             << conn->localAddress().toIpPort() << " is "
             << (conn->connected() ? "UP" : "DOWN");
}

void EchoServer::onMessage(const muduo::net::TcpConnectionPtr& conn,
                           muduo::net::Buffer* buf,
                           muduo::Timestamp time)
{
    muduo::string msg(buf->retrieveAllAsString());
    LOG_INFO << conn->name() << " echo " << msg.size() << " bytes, "
             << "data received at " << time.toString();
    conn->send(msg); 
}
```

#### main.cc

```c++
#include"echo.h"

#include<muduo/base/Logging.h>
#include<muduo/net/EventLoop.h>

#include <unistd.h>

int main()
{
    LOG_INFO << "pid = " << getpid();
    muduo::net::EventLoop loop;
    muduo::net::InetAddress listenAddr(2007);
    EchoServer server(&loop, listenAddr);
    server.start();
    loop.loop();
}
```

#### 编译运行

在echo文件路径下编译：

```shell
 g++ -g -o echo.o echo.* main.cc -lmuduo_base -lmuduo_net -lpthread
```

运行

```c++
./echo.o
```

运行结果：(终端)

```shell
zyq@zyq:~/project/server-based-muduo/echo$ ./echo.o
20231219 12:24:00.936033Z 57068 INFO  pid = 57068 - main.cc:10
```

打开浏览器，输入0.0.0.0:2007（网址链接）

终端显示：

```shell
zyq@zyq:~/project/server-based-muduo/echo$ ./echo.o
20231219 12:24:00.936033Z 57068 INFO  pid = 57068 - main.cc:10
20231219 12:27:25.193916Z 57068 INFO  TcpServer::newConnection [EchoServer] - new connection [EchoServer-0.0.0.0:2007#1] from 127.0.0.1:55526 - TcpServer.cc:80
20231219 12:27:25.193976Z 57068 INFO  EchoServer - 127.0.0.1:55526->127.0.0.1:2007 is UP - echo.cc:25
20231219 12:27:25.201158Z 57068 INFO  EchoServer-0.0.0.0:2007#1 echo 389 bytes, data received at 1702988845.201104 - echo.cc:35
```

2007为代码中端口设置值。

请求关闭会显示：

```shell
20231219 12:45:07.644593Z 58008 INFO  EchoServer - 127.0.0.1:46812->127.0.0.1:2007 is DOWN - echo.cc:25
20231219 12:45:07.644640Z 58008 INFO  TcpServer::removeConnectionInLoop [EchoServer] - connection EchoServer-0.0.0.0:2007#1 - TcpServer.cc:109
```

### 坑记录

#### bind()

旧文档中的；

```c++
boost::bind()
```

照抄会找不到bind()函数；

最新的改成了：

```
std::bind()
```

#### retrieveAsString()

旧文档中的：

```
muduo::string msg(buf->retrieveAsString());
```

会提示编译错误：retrieveAsString()缺少参数；

应改为：

```
muduo::string msg(buf->retrieveAllAsString());
```

retrieveAsString方法换成了retrieveAllAsString方法。