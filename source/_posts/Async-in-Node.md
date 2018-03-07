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

# Event Loop

## Browser

原文：https://zhuanlan.zhihu.com/p/33087629

![upload successful](/images/pasted-3.png)

task 包括 setTimeout、setInterval、setImmediate、I/O、UI交互事件

microtask 包括 Promise、process.nextTick、MutaionObserver

1. 执行同步代码
2. 取出第一个task并执行
3. 清空micortask队列
4. 重复 2 3

```javascript
console.log(1)

setTimeout(() => {
    console.log(2)
    new Promise(resolve => {
        console.log(4)
        resolve()
    }).then(() => {
        console.log(5)
    })
})

new Promise(resolve => {
    console.log(7)
    resolve()
}).then(() => {
    console.log(8)
})

setTimeout(() => {
    console.log(9)
    new Promise(resolve => {
        console.log(11)
        resolve()
    }).then(() => {
        console.log(12)
    })
})
```

- 输出同步代码 1 7
- setTimeout 具有时延 task队列为空
- 清空microtask队列 输出8
- 取第一个task 2 3
- 清空microtask队列 5 
- 取第一个task 9 11
- 清空microtask队列 12

```javascript
console.log(1)

setTimeout(() => {
    console.log(2)
    new Promise(resolve => {
        console.log(4)
        resolve()
    }).then(() => {
        console.log(5)
    })
    process.nextTick(() => {
        console.log(3)
    })
})

new Promise(resolve => {
    console.log(7)
    resolve()
}).then(() => {
    console.log(8)
})

process.nextTick(() => {
    console.log(6)
})
```

- promise.nextTick 优先级高于 Promise
- 1 7
- 6 8
- 2 4
- 3 5

## Node

![upload successful](/images/pasted-5.png)


- expired timers and intervals，即到期的setTimeout/setInterval
- I/O events，包含文件，网络等等
- immediates，通过setImmediate注册的函数
- close handlers，close事件的回调，比如TCP连接断开








