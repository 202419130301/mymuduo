# mymuduo

> 基于 Muduo 网络库思想的学习型实现，采用 `epoll + Reactor + One Loop Per Thread` 构建事件驱动的高性能 TCP 网络库。

## 项目简介

这个项目是对 Muduo 核心设计的拆解与复现，目标是通过“自己实现一遍”的方式理解高并发网络编程中的关键抽象。

当前实现聚焦于：

- Reactor 事件驱动模型
- `epoll` I/O 多路复用
- 非阻塞 I/O
- 线程封装与线程池协作
- TCP 连接生命周期管理

## 当前实现的核心模块

- `EventLoop`：事件循环与任务调度
- `Channel`：fd 与事件回调绑定
- `Poller` / `EPollPoller`：`epoll` 封装
- `Buffer`：收发缓冲区
- `TcpConnection`：连接对象与读写流程
- `Acceptor`：监听与接收新连接
- `TcpServer`：服务端主抽象
- `EventLoopThread` / `EventLoopThreadPool`：One Loop Per Thread 支撑
- `Thread` / `CurrentThread`：线程工具封装
- `Socket` / `InetAddress`：网络地址与套接字封装
- `Logger` / `Timestamp`：日志与时间戳工具

## 架构简图

```text
            +-----------------------+
            |       TcpServer       |
            +----------+------------+
                       |
                       v
               +-------+--------+
               |    Acceptor    |
               +-------+--------+
                       |
                       v
+----------------------+----------------------+
|                  EventLoop                  |
|     +---------------+------------------+    |
|     |              Poller              |    |
|     |     (EPollPoller / epoll_wait)   |    |
|     +---------------+------------------+    |
|                     |                       |
|                     v                       |
|                  Channel                    |
+---------------------------------------------+
                       |
                       v
                TcpConnection
```

## 构建方式

> 以 Linux 环境为例。

```bash
mkdir -p build
cd build
cmake ..
make -j
```

构建完成后会生成动态库：

- `lib/libmymuduo.so`

## 运行示例

项目内置了一个最小可运行的 Echo 服务器示例：`examples/echo_server.cpp`。

```bash
cd build
./echo_server 8888
```

另开一个终端测试：

```bash
nc 127.0.0.1 8888
```

输入任意文本后，服务端会原样回显。

## 目录说明

- 根目录：核心源码与头文件
- `examples/`：示例程序
- `build/`：CMake 构建产物
- `lib/`：动态库输出目录

## 说明与后续计划

这是一个学习型实现，重点在于理解 Muduo 的设计思想，而不是完整工业级复刻。后续可以继续补充：

- 示例 Echo/Chat 服务器
- 单元测试与压力测试
- 定时器（TimerQueue）与跨线程任务投递细节优化
- 更完善的错误处理与日志等级

## 致谢

- 陈硕《Linux 多线程服务端编程：使用 muduo C++ 网络库》
- [Muduo 网络库](https://github.com/chenshuo/muduo)
