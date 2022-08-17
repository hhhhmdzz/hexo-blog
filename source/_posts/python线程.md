---
title: python线程
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
  - 线程
date: 2022-03-09 17:53:02
img:
coverImg:
password:
summary: python线程相关模块的使用
description:
keyworks:
---

# python线程

[toc]

## 线程的概念

- outline
  - <a href="#procss thread">进程 vs 线程</a>
  - <a href="#calculate IO">计算密集型 vs IO密集型</a>
  - <a href="#char">线程特点</a>
  - <a href="#CPU thread">CPU & 线程</a>
  - <a href="#thread module">python线程模块</a>
  <br/>

- <a id="procss thread"><font color="lightblue">进程 vs 线程</font></a>
  - 进程：稳定性高(优)、开销大(缺)
  1. 进程是资源分配的最小单位(程序由若干进程组成)，一个程序至少有一个进程。
  2. 进程都有自己独立的地址空间，内存，数据栈等，所以进程占用资源多。  由于进程的资源独立，所以通讯不方便，只能使用进程间通讯（IPC）。
  - 线程：稳定性差(缺)、效率高(优)  
  1. 线程是程序执行的最小单位(进程由若干线程组成)，一个进程至少有一个线程。
  2. 线程共享进程中的数据，他们使用相同的地址空间，使用线程创建快捷，创建开销比进程小。  同一进程下的线程共享全局变量、静态变量等数据，所以线程通讯非常方便，但会存在数据同步与互斥的问题，如何处理好同步与互斥是编写多线程程序的难点。

>注：
1.一个进程中可以存在多个线程，在单核CPU中每个进程中同时刻只能运行一个线程，只有在多核CPU中才能存在线程并发的情况。
2.当线程需要运行但没有运行空间时，会对线程的优先级进行判断，高优先级先运行，低优先级进程让行。
3.多任务(无论是多进程还是多线程)一旦多到一个限度，就会消耗掉系统所有的资源，结果效率急剧下，所有任务都做不好。
4.线程间的切换是要快于进程间的切换

————————————————————

- <a id="calculate IO"><font color="lightblue">计算密集型 vs IO密集型</font></a>
  - 计算密集型：主要消耗CPU资源
  
  计算密集型任务的特点是要进行大量的计算，花在任务切换的时间就越多，CPU执行任务的效率就越低，所以，要最高效地利用CPU，计算密集型任务同时进行的数量应当等于CPU的核心数。(脚本语言python运行效率低，完全不适合计算密集型任务，而C语言在这方面效率要高得多)

  - IO密集型：大部分时间都在等待IO操作完成
  
  涉及到网络、磁盘IO的任务都是IO密集型任务，这类任务的特点是CPU消耗很少，任务的大部分时间都在等待IO操作完成（因为IO的速度远远低于CPU和内存的速度）。IO密集型任务执行期间，99%的时间都花在IO上，花在CPU上的时间很少，所以对于IO密集型任务，最合适的语言就是开发效率最高（代码量最少）的语言，脚本语言是首选，C语言最差。

简言之：Python的多线程，只有用于I/O密集型程序时效率才会有明显的提高。

<details>
<summary>原因</summary>

Python代码的执行是由Python虚拟机(也叫解释器主循环)进行控制。它在主循环中同时只能有一个控制线程在执行，意思就是Python解释器中可以运行多个线程，但是在执行的只有一个线程，其他的处于等待状态。这些线程执行是有全局解释器锁（GIL）控制，它来保证同时只有一个线程在运行。在多线程运行环境中，Python虚拟机执行方式如下：

1. 设置GIL
2. 切换进线程
3. 执行下面操作之一
　1. 运行指定数量的字节码指令
　2. 线程主动让出控制权
4. 切换出线程（线程处于睡眠状态）
5. 解锁GIL
6. 再次重复以上所有步骤

>在调用外部代码(如 C/C++扩展函数)的时候，GIL将会被锁定，直到这个函数结束为止(由于在这期间没有Python的字节码被运行，所以不会做线程切换)编写扩展的程序员可以主动解锁GIL。

</details>

————————————————————

- <a id="char"><font color="lightblue">线程特点</font></a>

1. 轻型实体
线程中的实体基本上不拥有系统资源，但是能保证独立运行的资源
2. 独立调度和分派的基本单位
线程是能独立运行的基本单位
3. 共享进程资源
所有线程都具有相同的进程id
4. 可并发执行
允许在一个进程中所有线程都能并发执行

————————————————————

- <a id="CPU thread">CPU & 线程</a>

打开 Mac OS X 的 Activity Monitor 或者 Windows 的 Task Manager，都可以监控某个进程的CPU使用率。

我们可以监控到一个死循环线程会100%占用一个CPU。如果有两个死循环线程，在多核CPU中，可以监控到会占用200%的CPU，也就是占用两个CPU核心。要想把N核CPU的核心全部跑满，就必须启动N个死循环线程。

尝试在python启动与CPU核心数量相同的N个线程：

```python
import threading, multiprocessing

def loop():
    x = 0
    while True:
        x = x ^ 1

for i in range(multiprocessing.cpu_count()):
    t = threading.Thread(target=loop)
    t.start()
```

这个Python程序在4核CPU上可以监控到CPU占用率仅有102%，也就是仅使用了一核。但是用C、C++或Java来改写相同的死循环，直接可以把全部核心跑满，4核就跑到400%，8核就跑到800%。

为什么？因为Python的线程虽然是真正的线程，但解释器执行代码时，有一个GIL锁(Global Interpreter Lock)，任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。

所以，如果一定要通过多线程利用多核，那只能通过C扩展来实现。但是Python可以通过多进程实现多核任务。多个Python进程有各自独立的GIL锁，互不影响。

————————————————————

- <a id="thread module"><font color="lightblue">python线程模块</font></a>

  - _thread：低级模块

  _thread模块不支持守护线程，当主线程退出时，所有的子线程不论它们是否还在工作，都会被强行退出。
  - threading：高级模块，对_thread进行了封装，更先进、完善

  threading模块支持守护线程，守护线程一般是一个等待客户请求的服务器，如果没有客户提出请求它就在那等着，如果设定一个线程为守护线程，就表示这个线程是不重要的，在进程退出的时候，不用等待这个线程退出。

<details>
<summary>threading 模块对象、方法</summary>

|对象|描述|
|:-:|:-:|
|`Thread`|表示一个执行线程的对象|
|`Lock`|锁原语对象|
|`RLock`|可重入锁对象，使单一线程可以（再次）获得已持有的锁（递归锁）|
|`Condition`|条件变量对象，使得一个线程等待另一个线程满足特定的“条件”，比如改变状态或某个数据值|
|`Event`|条件变量的通用版本，任意数量的线程等待某个事件的发生，在该事件发生后所有线程将被激活|
|`Semaphore`|为线程间共享的有限资源提供了一个“计数器”，如果没有可用资源时会被阻塞|
|`BoundedSemaphore`|与`Semaphore`相似，不过它不允许超过初始值|
|`Timer`|与`Thread`相似，不过它要在运行前等待一段时间|
|`Barrier`|创建一个障碍，必须达到指定数量的线程才可以继续|

|方法|描述|
|:-:|:-:|
|`activeCount()`|获取当前活动中的Thread对象个数|
|`currentThread()`|获取当前的Thread对象|
|`enumerate()`|获取当前活动的Thread对象列表|
|`settrace(func)`|为所有线程设置一个跟踪（trace）函数|
|`setprofile(func)`|为所有线程设置配置文件（profile）函数|
|`stack_size(size=None)`|获取新创建线程的栈大小，也可设置线程栈的大小为size|

</details>

---

## 线程的基本操作

### `threading.Thread` 类

- outline
  - **<a href='#Thread intro'>Thread类介绍</a>**
  - **<a href="#Thread examp">Thread类实例</a>**
    - **<a href="#使用Thread创建实例对象">使用Thread创建实例对象</a>**
    - **<a href="#继承Thread创建实例对象">继承Thread创建实例对象</a>**
    - **<a href="#内存数据的共享问题">内存数据的共享问题</a>**
    - **<a href="#多线程实现socket">多线程实现socket</a>**
    - **<a href="#守护线程">守护线程</a>**
    <br/>

<a id='Thread intro'><font color="black"><h4>Thread类介绍</h4></font></a>

```python
class Thread{
    group=None,   # 为了日后扩展 ThreadGroup 类实现而保留
    target=None,  # 用于 run() 方法调用的可调用对象
    name=None,    # 线程名称
    args=(),      # 调用目标函数的参数元组
    kwargs={},    # 调用目标函数的关键字参数字典
    daemon=None,  # 守护模式
}
```

>`daemon = None`：线程将继承当前线程的守护模式属性
`daemon != None`：设置该线程是否为守护模式

<details>
<summary>Thread类实例对象的方法、属性</summary>

```python
# todo 方法
t = Thread()           # 实例化线程
t.start()              # 开始执行该线程
t.run()                # 定义线程功能的方法（通常在子类中被应用开发者重写）
t.join(timeout=None)   # 阻塞，直至启动的线程终止之前一直挂起；除非给出了 timeout（秒），否则会一直阻塞
t.getName()            # 返回线程名
t.setName(name)        # 设定线程名
t.isAlivel()           # 该线程是否还存活
t.is_alive()           # 该线程是否还存活
t.isDaemon()           # 如果是守护线程，则返回 True；否则，返回 False
t.setDaemon(daemonic)  # 把线程的守护标志设定为布尔值 daemonic（必须在线程 start()之前调用）

# todo 属性
t.name       # 线程名
t.ident      # 线程标识符（通过t.get_ident()获取）
t.native_id  # 此线程的原生集成线程 TID，类似于进程 PID（通过t.get_native_id()获取）
t.daemon     # 守护线程
```

</details>

<details>
<summary>Thread类的方法、属性</summary>

```python
threading.currentThread()  # 返回当前的线程变量（对象）。
threading.enumerate()      # 返回一个包含正在运行的线程的list。正在运行指线程启动后、结束前，不包括启动前和终止后的线程。
threading.activeCount()    # 返回正在运行的线程数量，与len(threading.enumerate())有相同的结果。
```

</details>

<a id="Thread examp"><h4><font color="black">Thread类实例</font></h4></a>

- <a id='使用Thread创建实例对象'>使用Thread创建实例对象</a>

同一进程的线程有相同的PID，但是TID不同！

```python
import os, time
import threading
from threading import Thread

def loop():
    time.sleep(0.5)
    loop_thread = threading.current_thread()
    print(f"线程 {loop_thread.name} 开始......")
    print(f"线程 {loop_thread.name} PID：{os.getpid()}")
    print(f"线程 {loop_thread.name} TID：{loop_thread.native_id}")
    print(f"线程 {loop_thread.name} 结束......")

def main():
    main_thread = threading.current_thread()
    print(f"线程 {main_thread.name} 开始......")
    print(f"线程 {main_thread.name} PID：{os.getpid()}")
    print(f"线程 {main_thread.name} TID：{main_thread.native_id}")
    
    t1 = Thread(target=loop, name="Loopthread-1", )
    t2 = Thread(target=loop, name="Loopthread-2", )
    
    t1.start()
    t2.start()

    t1.join()
    t2.join()

    print(f"线程 {main_thread.name} 结束......")

if __name__ == "__main__":
    main()
```

>注：
1.主线程：任何进程默认就会启动一个线程，我们把该线程称为主线程，主线程又可以启动新的线程

- <a id="继承Thread创建实例对象">继承Thread创建实例对象</a>

```python
import time
from threading import Thread

class MyThread(Thread):

    def __init__(self, target, name):
        super().__init__()
        self.target = target
        self.name = name

    def run(self):
        self.target(self.name)
        time.sleep(0.5)

def say_hi(name):
    print(f"hello, this's {name}")

def main():
    mythread = MyThread(target=say_hi, name="my thread")
    mythread.start()
    mythread.join()

if __name__ == "__main__":
    main()
```

>注：
1.继承必须实现`run()`函数！

- <a id="内存数据的共享问题">内存数据的共享问题</a>

```python

import os, time
import threading
from threading import Thread
from multiprocessing import Process

def work():
    global n
    n = 0

if __name__ == '__main__':
    n = 100
    p = Process(target=work)
    p.start()
    p.join()
    print('主进程的 n = ', n)

    n = 1
    t = Thread(target=work)
    t.start()
    t.join()
    print('主线程的 n = ', n)
```

>注：
1.子进程p已经将自己的全局的n改成了0（具体来说是将数据拷贝了一份，然后再修改），但改的仅仅是它自己的，父进程的n仍然为100，所以进程之间数据不共享
2.主线程n的结果为0，同一进程内的线程之间共享进程内的数据

- <a id="多线程实现socket">多线程实现socket</a>

server端

```python
#_*_coding:utf-8_*_
#!/usr/bin/env python
import threading
import multiprocessing
 
import socket
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.bind(('127.0.0.1',8080))
s.listen(5)

def action(conn):
    while True:
        data=conn.recv(1024)
        print(data)
        conn.send(data.upper())
 
if __name__ == '__main__':

    while True:
        conn,addr=s.accept()
        p=threading.Thread(target=action,args=(conn,))
        p.start()
```

client端

```python
#_*_coding:utf-8_*_
#!/usr/bin/env python
import socket
 
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(('127.0.0.1',8080))
 
while True:
    msg = input('>>: ').strip()
    if not msg:continue
 
    s.send(msg.encode('utf-8'))
    data = s.recv(1024)
    print(data)
```

- <a id="守护线程">守护线程</a>

<b>无论是进程还是线程，都遵循：守护xx会等待主xx运行完毕后被销毁。需要强调的是：运行完毕并非终止运行</b>

><b>守护线程运行完毕</b>
1.对主进程来说，运行完毕指的是主进程代码运行完毕
2.对主线程来说，运行完毕指的是主线程所在的进程内所有非守护线程通通运行完毕，主线程才算运行完毕
>><b>详细解释</b>
1.主进程在其代码结束后就已经算运行完毕了（守护进程在此时就被回收）,然后主进程会一直等非守护的子进程都运行完毕后回收子进程的资源(否则会产生僵尸进程)，才会结束。
2.主线程在其他非守护线程运行完毕后才算运行完毕（守护线程在此时就被回收）。因为主线程的结束意味着进程的结束，进程整体的资源都将被回收，而进程必须保证非守护线程都运行完毕后才能结束。

```python
import os, time
import threading
from threading import Thread

def bar():
    time.sleep(1)
    print("thread bar is running......")

def foo():
    time.sleep(1)
    print("thread foo is running......")

if __name__ == "__main__":
    t1 = Thread(target=bar,)
    t2 = Thread(target=foo,)

    # t1.daemon = True
    t1.setDaemon(True)

    t1.start()
    t2.start()

    print(f"thread bar is alive {t1.is_alive()}")
    print(f"thread foo is alive {t2.is_alive()}")

    t1.join()
    t2.join()

    print(f"thread bar is alive {t1.is_alive()}")
    print(f"thread foo is alive {t2.is_alive()}")
```

>守护进程必须设置在`t.start()`之前！

---

#### 结束线程方法

见；[python threading 结束线程](https://blog.csdn.net/fenglepeng/article/details/103186966)

### `threading.Lock` 类

多线程是在同一个进程下运行的，因此在进程中的全局变量所有线程都是共享的，这就造成一个问题，因为线程执行的顺序是无序的，有可能会造成数据错误，所以引入锁机制。

- outline
  - **<a href="#Lock intro">Lock类介绍</a>**
  - **<a href="#Lock examp">Lock类实例</a>**
    - **<a href="#share data prob">多线程数据共享的问题</a>**
    - **<a href="#share data solu">Lock解决多线程数据共享的问题</a>**
    - **<a href="#Lock0">Lock买票问题</a>**
    <br/>

<a id="Lock intro"><font color="b"><h4>Lock类介绍</h4></font></a>

<details>
<summary>Lock类对象方法</summary>

```python
l = Lock()                               # 实例化锁对象
l.acquire(blocking=True, timeout=-1)	 # 获取锁（阻塞或非阻塞）
l.release()	                         # 释放锁
l.locked()                               # 如果获得了锁则返回真值
```

</details>

锁有两种状态：<font color="red">锁定</font>和<font color="red">未锁定</font>。而且也只有两个函数：<font color="red">获取锁</font>和<font color="red">释放锁</font>。当多线程争夺锁的时候，允许第一个获得锁的线程进入临界区，并执行代码，之后所有的线程都被阻塞，当第一个线程执行结束，退出临界区，释放锁，此时，其他等待的线程可以获得锁进入临界区，不过要记住，被阻塞的线程是<font color="red">无序</font>的。

<a id="Lock examp"><font color="b"><h4>Lock类实例</h4></font></a>

- <a id="share data prob">多线程数据共享的问题</a>

```python
from threading import Thread

def add_v():
    global value
    for x in range(1000000):
        value += 1
    print(f"value: {value}")

value = 0
if __name__ == "__main__":
    threads = []
    for i in range(2):
        threads.append(Thread(target=add_v))
    for i in range(2):
        threads[i].start()
```

```shell
# todo 结果
value: 1398771
value: 1555540
```

- <a id="share data solu">Lock解决多线程数据共享的问题</a>

```python
from threading import Lock, Thread

def add_value():
    global value
    lock.acquire()  # 获取锁
    for x in range(1000000):
        value += 1
    lock.release()  # 释放锁
    print(f"value: {value}")

value = 0
lock = Lock()
if __name__ == "__main__":
    threads = []
    for i in range(2):
        threads.append(Thread(target=add_v))
    for i in range(2):
        threads[i].start()
```

```shell
# todo 结果
value: 1000000
value: 2000000
```

- <a id="Lock0">Lock卖票问题</a>

假设有3个卖票窗口，一共10张票，每个窗口排队买票，每人最多购买2张，请使用多线程模拟该场景。

```python
# to be continue
```

---

### `threading.RLock` 类

递归锁RLock和普通锁Lock的差别在于加入了“所属线程”和“递归等级”的概念，释放锁必须由获取锁的线程来进行释放。同时，同一个线程在释放锁之前再次获取锁将不会阻塞当前线程，只是在锁的递归等级上加了1（获得锁时的初始递归等级为1）。

说白了递归锁就是——普通锁的套娃：递归锁RLock可以上很多次锁，上了几次锁就得解几次锁才能解开，但是必须得完全解开之后，其他线程才能使用其中的资源。

- outline
  - **<a href="#RLock intro">RLock类介绍</a>**
  - **<a href="#RLock examp">RLock类实例</a>**
    - **<a href="#deadlock prob">死锁问题</a>**
    - **<a href="#deadlock solu">RLock解决死锁问题</a>**
    <br/>

<a id="RLock intro"><font color="black"><h4>RLock类介绍</h4></font></a>

<details>
<summary>RLock类方法(略)</summary>

```python
rl = RLock()                           # 实例化递归锁对象
rl.acquire(blocking=True, timeout=-1)  # 递归锁的递归等级加1
rl.release()                           # 递归锁的递归等级减1
```

</details>

<details>
<summary>RLock类方法(详)</summary>

```python
rl = RLock()
rl.acquire(blocking=True, timeout=-1)
    """
    与普通锁的不同之处在于：当使用默认值时，如果这个线程已经拥有锁，那么锁的递归等级加1。
    线程获得锁时，该锁的递归等级被初始化为1。
    当多个线程被阻塞时，只有一个线程能在锁被解时获得锁，这种情况下，acquire()是没有返回值的。
    """
rl.release()
    """
    没有返回值，调用一次则递归等级减1，
    递归等级为零时表示这个线程的锁已经被释放掉，其他线程可以获取锁了。
    可能在一个线程中调用了多次acquire()，导致锁的递归等级大于了1，
    那么就需要调用对应次数的release()来完全释放锁，
    并将它的递归等级减到零，其他的线程才能获取锁，不然就会一直被阻塞着。
    """
```

</details>

<a id="RLock examp"><font color="black"><h4>RLock类实例</h4></font></a>

- <a id="deadlock prob">死锁问题</a>

**死锁**：指的是两个或两个以上的线程或进程在请求锁的时候形成了互相等待阻塞的情况，导致这些线程或进程无法继续执行下去，这时候称系统处于死锁状态或者系统产生了死锁，这些线程或进程就称为死锁线程或死锁进程。

**举个例子**：我有一个苹果，你有一个香蕉，我想要你的香蕉，你想要我的苹果。但是，我要求：你得先给我香蕉我才能给你苹果，你也要求：我得给你苹果你才能给我香蕉。所以，双方就无法完成交换，即造成死锁。

>死锁问题实例：
`func1()`：得到苹果->得到香蕉->释放香蕉->释放苹果
`func1()`：得到香蕉->得到苹果->释放苹果->释放香蕉
>>造成死锁流程：
1.`thread-1`在`func1`中，依次得到并释放苹果和香蕉
2.接着，`thread-1`在`func2`中得到香蕉，然后`sleep(0.1)`
3.此时，趁`thread-1`在休眠，`thread-2`开始执行
4.`thread-2`在`func1`中，先得到苹果，但是无法得到香蕉（因为香蕉在`thread-1`里面）
5.这样，就出现了死锁：`thread-1`等`thread-2`释放苹果，`thread-2`等`thread-1`释放香蕉

```python
import time
import threading

lock_apple = threading.Lock()
lock_banana = threading.Lock()

class MyThread(threading.Thread):

    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        self.fun1()
        self.fun2()

    def fun1(self):
        lock_apple.acquire()  # 如果锁被占用,则阻塞在这里,等待锁的释放
        print ("线程 %s , 想拿: %s--%s" %(self.name, "苹果", time.ctime()))
        lock_banana.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "香蕉", time.ctime()))

        lock_banana.release()
        print ("线程 %s , 释放: %s--%s" %(self.name, "香蕉", time.ctime()))
        lock_apple.release()
        print ("线程 %s , 释放: %s--%s" %(self.name, "苹果", time.ctime()))

    def fun2(self):
        lock_banana.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "香蕉", time.ctime()))

        time.sleep(0.1)  # 休眠，让其他线程开启
        lock_apple.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "苹果", time.ctime()))

        lock_apple.release()
        print ("线程 %s , 释放: %s--%s" %(self.name, "苹果", time.ctime()))

        lock_banana.release()
        print ("线程 %s , 释放: %s--%s" %(self.name, "香蕉", time.ctime()))

if __name__ == "__main__":
    for i in range(5):  # 建立5个线程
        my_thread = MyThread()
        my_thread.start()
```

- <a id="deadlock solu">RLock解决死锁问题</a>

```python
import time
import threading

lock = threading.RLock()  #递归锁

class MyThread(threading.Thread):

    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        self.fun1()
        self.fun2()

    def fun1(self):
        lock.acquire()  # 如果锁被占用,则阻塞在这里,等待锁的释放
        print ("线程 %s , 想拿: %s--%s" %(self.name, "苹果",time.ctime()))

        lock.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "香蕉",time.ctime()))

        lock.release()
        print ("线程 %s , 释放: %s--%s" %(self.name, "香蕉",time.ctime()))

        lock.release()
        print ("线程 %s , 释放: %s--%s" %(self.name, "苹果",time.ctime()))

    def fun2(self):
        lock.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "香蕉",time.ctime()))

        time.sleep(0.1)
        lock.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "苹果",time.ctime()))

        lock.release()
        print ("线程 %s , 释放: %s--%s" %(self.name, "苹果",time.ctime()))

        lock.release()
        print ("线程 %s , 释放: %s--%s" %(self.name, "香蕉",time.ctime()))

if __name__ == "__main__":
    for i in range(10):  #建立10个线程
        my_thread = MyThread() 
        my_thread.start()
```

---

### `threading.Condition` 类

Lock锁机制存在一个问题，上锁是一个很耗费CPU资源的行为，这时候就可以考虑使用Condition对象，`threading.Condition()`可以在<font color="orange">没有数据的时候处于阻塞等待状态</font>，<font color="orange">一旦有合适的数据，还可以使用`notify()`相关的函数来通知其他处于等待状态的线程</font>，这样就可以<font color="orange">不用做一些无用的上锁和解锁操作</font>，可以提高程序的性能。

- outline
  - **<a href="#Condition intro">Condition类介绍</a>**
  - **<a href="#Condition examp">Condition类实例</a>**
    - **<a href="#Condition0">Condition示例</a>**

<a id="Condition intro"><font color="black"><h4>Condition类介绍</h4></font></a>

<details>
<summary>Condition类对象方法</summary>

```python
c = Condition()    # 实例化条件对象
c.acquire()        # 获取锁
c.release()        # 释放锁
c.wait()           # 将当前线程处于等待状态，并释放锁。可以被其他线程使用 notify 和 notify_all 函数唤醒，被唤醒后会继续等待上锁，上锁后继续执行下面代码
c.notify()         # 通知正在等待的某个线程，默认是第一个等待的线程
c.nofity_all()     # 通知所有正在等待的线程，notify和nofify_all不会释放锁，并且需要在release（）之前调用
```

</details>

<a id="Condition examp"><font color="black"><h4>Condition类实例</h4></font></a>

- <a id="Condition0">Condition示例</a>

Condition实现生产和消费多对多模型

创建两个类：Producer和Consumer。Producer用于赚钱，规定每个线程只需要工作10次即可；Consumer用于消费。

Consumer每次花钱的时候会看一下金库的总额够不够，如果不够就会调用`gCondition.wait()`将线程处于等待.当Producer赚钱了，就会调用`gCondition.notify_all()`，提醒真正等待的线程，当线程被唤醒之后会继续等待上锁，上锁后继续执行下面的代码。

```python {.line-numbers}
import time, random
from threading import Thread, Lock, RLock, Condition, current_thread

g_money = 1000
g_condition = Condition()
g_times = 0
g_totaltimes = 10

class Producer(Thread):
    def __init__(self, name):
        super().__init__()
        self.name = name

    def run(self):
        global g_money
        global g_times
        while True:
            money = random.randint(100, 1000)
            g_condition.acquire()        # 获得锁
            if g_times >= g_totaltimes:
                g_condition.release()
                break
            g_money += money
            print(f"生产者{current_thread()}生产了{money}元，余额：{g_money}元")
            g_times += 1
            g_condition.notify_all()     # 通知所有正在等待的线程
            g_condition.release()        # 释放锁
            time.sleep(0.5)

class Consumer(Thread):
    def __init__(self, name):
        super().__init__()
        self.name = name

    def run(self):
        global g_money
        while True:
            price = random.randint(500, 1000)
            g_condition.acquire()            # 获得锁
            while g_money < price:
                if g_times >= g_totaltimes:
                    g_condition.release()
                    return
                print(f"消费者{current_thread()}准备消费{price}，余额不足！余额：{g_money}元")
                g_condition.wait()           # 钱不够了，等待并释放锁
            g_money -= price
            print(f"消费者{current_thread()}消费了{price}元，余额：{g_money}元")
            g_condition.release()            # 完成消费，释放锁
            time.sleep(0.5)

if __name__ == "__main__":
    for x in range(3):  # 添加并开启消费线程
        t = Consumer(name=f"consumer{x}")
        t.start()
    for x in range(5):  # 添加并开启生产线程
        t = Producer(name=f"producer{x}")
        t.start()
```

一个线程使用 `g_condition.acquire()` `g_condition.wait()` 获得锁并等待，其子线程不会等待，而是继续运行。

---

### `threading.Semaphore` 类

---

### `threading.Event` 类

---

### `threading.Timer` 类

---

### `threading.Barrier` 类

---

### `threading.loacl` 类

- outline
  - **<a href="#local intro">local类介绍</a>**
  - **<a href="#local examp">local类实例</a>**
    - **<a href="#local0">local示例</a>**

<a id="local intro"><font color="black"><h4>local类介绍</h4></font></a>

- **`ThreadLocal`的由来**

在多线程中，使用局部变量比使用全局变量要好，因此每个线程都有自己的数据，那么如果想要分享线程的数据，就得传参，但是在调用函数传参的时候会有点麻烦，因此就出现了`ThreadLocal`。

- **`ThreadLocal`的理解**

`ThreadLocal`（线程本地变量，或者线程本地存储）在每一个变量中都会创建一个副本，每个线程都可以访问自己内部的副本变量。通俗点说，`ThreadLocal`就像一个字典，字典的<font color="orange">键值对</font>就像<font color="orange">线程的变量及其数据。</font><font color="lightgrey">所以按理来说，只要你想，完全可以使用字典来保存线程的数据，只是`ThreadLocal`更好更有优势。</font>

- **`ThreadLocal`的使用**

`ThreadLocal`是一个<font color="orange">全局变量</font>，但每个线程都<font color="orange">只能读写自己线程的独立副本，互不干扰</font>。`ThreadLocal`解决了参数在一个线程中各个函数之间互相传递的问题，真正做到了线程之间的数据隔离。

<img src="./python线程-ThreadLocal.png" height="600">

<details>
<summary>local类对象方法</summary>

```python
tl = local()  # 实例化ThreadLocal对象
tl.xxx = dat  # 动态绑定
```

</details>

<a id="local examp"><font color="black"><h4>local类实例</h4></font></a>

- <a id="local0">local示例</a>

每一个线程都可以通过`local_school.student`获取自己独有的数据，并且每个线程读取到的`local_school.student`都不同，真正做到线程之间的隔离。

```python
import threading

local_school = threading.local()  # 创建全局ThreadLocal对象

def process_student():  # 获取当前线程关联的student
    std = local_school.student
    print('Hello, %s (in %s)' % (std, threading.current_thread().name))

def process_thread(name):  # 绑定ThreadLocal的student
    local_school.student = name
    process_student()

if __name__ == "__main__":
    t1 = threading.Thread(target=process_thread, args=('Alice',), name='Thread-A')
    t2 = threading.Thread(target=process_thread, args=('Bob',), name='Thread-B')
    t1.start()
    t2.start()
    t1.join()
    t2.join()
```

---

## 参考

|链接|标签|说明|网站|
|:-:|:-:|:-:|:-:|
|[Python内置库：threading（多线程）](https://www.cnblogs.com/guyuyun/p/11185832.html)|`threading`|内容很多很全、讲原理、解释参数、实例多、通俗|博客园|
|[Python 基本功-线程](https://blog.csdn.net/briblue/category_8520204.html)|`threading`|这个专栏系列内容很多很全、讲原理、解释参数、实例多、通俗|CSDN|
|[python基础-线程](https://blog.csdn.net/qq_41922768/category_7767239.html)|`threading`|这个专栏系列内容很多很全、讲原理、解释参数、实例多|CSDN|
|[Python threading实现多线程 基础篇](https://zhuanlan.zhihu.com/p/91601448)|||知乎|
|[Python threading实现多线程 提高篇 线程同步，以及各种锁](https://zhuanlan.zhihu.com/p/94344847)|`Lock` `RLock` `Semaphore` `Condition` `Event` `Barrie` `queue`||知乎|
|[多线程](https://www.liaoxuefeng.com/wiki/1016959663602400/1017629247922688)|||廖雪峰的官方网站|
|[threading模块基本使用](https://blog.csdn.net/l835311324/article/details/86608850)|`Thread` `LOCK` `Condition`|模块对象方法较全|CSDN|
|[threading --- 基于线程的并行](https://docs.python.org/zh-cn/3.8/library/threading.html)|`threading`|教程|python官网|

锁：

|链接|标签|说明|网站|
|:-:|:-:|:-:|:-:|
|[一篇文章理清Python多线程同步锁，死锁和递归锁](https://cloud.tencent.com/developer/article/1419992)|`Lock` `RLock`|详细讲了`RLock`解决死锁的问题|CSDN|

ThreadLocal：

|链接|标签|说明|网站|
|:-:|:-:|:-:|:-:|
|[Python中ThreadLocal的理解与使用](https://www.cnblogs.com/linpd/p/10051945.html)|`ThreadLocal`||博客园|
|[深入理解Python中的ThreadLocal变量（上）](https://www.jianshu.com/p/74640d22a72f)|`ThreadLocal`||简书|
|[深入理解Python中的ThreadLocal变量（中）](https://www.jianshu.com/p/474ecc81d756)|`ThreadLocal`||简书|
|[深入理解Python中的ThreadLocal变量（下）](https://www.jianshu.com/p/0e12c1397bfd)|`ThreadLocal`||简书|
|[ThreadLocal](https://www.liaoxuefeng.com/wiki/1016959663602400/1017630786314240)|`ThreadLocal`||廖雪峰的官方网站|

## 待完成

- [x] 通过with语句使用线程锁
- [ ] Lock卖票问题
- [ ] 信号量对象：threading.Semaphore
- [ ] 事件对象：threading.Event
- [ ] 定时器对象：threading.Timer
- [ ] 栅栏对象：threading.Barrier
