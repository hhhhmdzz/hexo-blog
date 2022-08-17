---
title: python协程
author: hhhhmdzz
language: zh-CN
timezone: Asia/Shanghai
top: false
cover: false
toc: true
mathjax: true
comments: true
categories:
  - python
tags:
  - python
  - 协程
date: 2022-03-09 17:53:02
img:
coverImg:
password:
summary: python协程相关模块的使用
description:
keyworks:
---

# python协程

[toc]

## 协程的概念

协程 (Coroutine)，又称为微线程，是一种用户态的**轻量级线程**。

协程拥有自己的寄存器上下文和栈。协程调度切换时，将寄存器上下文和栈保存到其他地方，在切回来的时候，恢复之前保存的寄存器上下文和栈。因此，协程能够保留上次一调用时的状态，能够进入上一次离开时所处的逻辑流的位置。

- 协程的好处
  1. 无需线程上下文切换的开销
  2. 无需原子操作(不会被线程调度机制打断的操作)锁定以及同步的开销
  3. 方便切换控制流，简化编程模型
  4. 高并发 + 高扩展性 + 低成文：一个 CPU 支持上完的协程都不是问题，所以很适合高并发处理
- 协程的缺点
  1. 无法利用多核资源：协程的本质是单线程，需要和进程配合才能运行在多 CPU 上
  2. 进行阻塞 (Blocking) 操 作(如 IO 时) 会阻塞掉整个程序
- 协程的条件
  1. 必须在**只有一个单线程**里实现并发
  2. 修改**共享数据不需加锁**
  3. 用户程序里自己保存多个控制流的上下文栈
  4. 一个协程遇到 IO 操作自动切换到其它协程

python 中的 `yield` 支持基本的协程，但是不完全，以下**使用 `yield` 实现协程：**

```python {.line-numbers}
def consumer(name):
    print(f"{name} is consuming.")
    while True:
        money = yield
        print("[%s] consumed %d ￥." % (name, money))

def producer(times=5):
    next(con1)
    next(con2)
    for n in range(1, times):
        con1.send(n)
        con2.send(n)
        print("\033[32;1m[producer]\033[0m is producing %s ￥." % (n))
 
if __name__ == '__main__':
    con1 = consumer("customer1")
    con2 = consumer("customer2")
    p = producer(5)
```

## Greenlet模块

## Asyncio模块

## Gevent模块

## 协程的实例
