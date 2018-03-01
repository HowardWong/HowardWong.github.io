title: Async in Node
author: Howard Wong
date: 2018-03-01 16:22:50
tags:
---

```
它的优秀之处并非原创，它的原创之处并不优秀。
								——— Brendan Eich
```

# 基础
## 异步/同步  &  阻塞/非阻塞

### 阻塞/非阻塞
```
操作系统处理IO的两种方式： 阻塞/非阻塞

阻塞IO： 操作系统等待IO处理完成才返回结果

非阻塞IO： 调用IO后立即返回－轮询IO是否完成
```

### 轮询方式
- read            重复调用，反复轮询
- select          通过判断句柄上的状态
- poll              性能改善select
- epoll            主流，epoll请求后等待休眠，回调唤醒进行一个read

### 异步/同步
```
epoll休眠期间，CPU是闲置的
nodejs主线程为单线程，使用其他线程作为IO线程
IO线程完成任务后，通过线程间通信将数据返回
```
```
-------------------------------
			 Nodejs
-------------------------------
             libuv
-------------------------------
*nix(自定义实现) | windows(IOCP)
-------------------------------
```

# 异步API
- setTimeout
- process.nextTick
- process.setImmediate