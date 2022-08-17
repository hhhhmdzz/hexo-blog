---
title: python进程
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
  - 进程
date: 2022-03-09 17:53:02
img:
coverImg:
password:
summary: python进程相关模块的使用
description:
keyworks:
---

# python进程

<font color="orange">For Windows</font>

[toc]

## 进程的概念

- outline
  - <a href="#def">进程的定义</a>
  - <a href="#char">进程的特征</a>
  - <a href="#disp">进程调度</a>
  - <a href="#parl">进程的并行与并发</a>
  - <a href="#state">进程的状态</a>
  - <a href="#lib">python中的进程库</a>

————————————————————

- <a id='def'><font color="lightblue">进程的定义</font></a>
  - 狭义定义
  进程是正在运行的程序的实例(an instance of a computer program that is being executed)。
  - 广义定义
  进程是一个具有一定独立功能的程序关于某个数据集合的一次运行活动。它是操作系统动态执行的基本单元，在传统的操作系统中，进程既是基本的分配单元，也是基本的执行单元。

简单来说，进程就是程序的基本执行实体。

————————————————————

- <a id='char'><font color="lightblue">进程的特征</font></a>
  - 动态性：进程的实质是程序在多道程序系统中的一次执行过程，进是动态产生，动态消亡的。
  - 并发性：任何进程都可以同其他进程一起并发执行
  - 独立性：进程是一个能独立运行的基本单位，同时也是系统分配资和调度的独立单位；
  - 异步性：由于进程间的相互制约，使进程具有执行的间断性，即进按各自独立的、不可预知的速度向前推进
  - 结构特征：进程由程序、数据和进程控制块三部分组成。
  - 多个不同的进程可以包含相同的程序：一个程序在不同的数据集里就构成不同的进程，能得到不同的结果；但是执行过程中，程序不能发生改变。

————————————————————

- <a id='disp'><font color="lightblue">进程调度</font></a>
  - 先来先服务调度算法：利于长作业（进程），而不利于短作业（进程）
  - 短作业优先调度算法：对短作业或短进程优先调度
  - 时间片轮转法：让每个进程在就绪队列中的等待时间与享受服务的时间成比例
  - 多级反馈队列：目前被公认的一种较好的进程调度算法

————————————————————

- <a id='parl'><font color="lightblue">进程的并行与并发</font></a>
  - 并行：多个进程同时执行
  - 并发：多个进程交替进行

————————————————————

- <a id='state'><font color="lightblue">进程的状态</font></a>：创建、就绪、运行、阻塞、退出

- 同步与异步
  - 同步：可靠的任务序列，进程1依赖于进程2，只有进程2完成后，进程1才算完成，即这两个进程状态保持一致。
  - 异步：不可靠的任务序列，进程1依赖于进程2，不管进程2是否完成，进程1自己完成就算完成，即这两个进程状态不一定保持一致。

- 阻塞与非阻塞：即程序等待消息的状态。

- 同步/异步与阻塞/非阻塞
  - 同步阻塞：效率最低(只排队)
  - 异步阻塞：效率较低(只叫号)
  - 同步非阻塞：效率较低(排队+打电话)
  - 异步非阻塞：效率高(叫号+打电话)

————————————————————

- <a id="lib"><font color="lightblue">python中的进程库</font></a>
  - `subprocess`：适用于与外部进程交互，调用外部进程

  `subprocess`用来执行外部命令，是`os.fork()`和`os.execve()`的封装，即先fork一个子进程，再运行新的外部程序，<font color="orange">子进程不会把父进程的模块加载一遍</font>。
  - `multiprocessing`

  `multiprocessing`的原理是fork，`fork()`调用：调用1次，返回两次——操作系统自动把当前进程（父进程）复制了一份（子进程），然后，分别在父进程和子进程内返回，父进程返回子进程的pid，子进程返回0，即父进程和子进程都在运行。对于外部调用来说，使用multiprocessing太占资源。

---

## 进程的基本操作

### `multiprocessing.Process` 类

- outline
  - **<a href="#Process intro">Process类介绍</a>**
  - **<a href="#Process examp">Process类实例</a>**
    - **<a href="#Process0">使用Process开启一个子进程</a>**
    - **<a href="#Process1">继承Process开启一个子进程</a>**
    - **<a href="#Process2">使用Process守护进程</a>**
    - **<a href="#Process3">多进程socket聊天并发</a>**

<a id="Process intro"><font color="black"><h4>Process类介绍</font></h4></a>

```python
class Process{
    group,   # 参数未使用，值始终为None
    target,  # 调用对象，即子进程要执行的任务
    args,    # 调用对象的位置参数元组
    kwargs,  # 调用对象的字典
    name,    # 子进程的名称
}
```

<details>
<summary>Process类方法、属性(略)</summary>

```python
# todo 方法
p = Process()    # 创建子进程
p.start()        # 启动子进程，并调用该子进程中的 p.run() 
p.run()          # 进程启动时运行的方法
p.terminate()    # 强制终止进程
p.is_alive()     # 如果p仍然运行，返回True
p.join(timeout)  # 主线程等待p终止，timeout是可选的超时时间

# todo 属性
p.daemon    # 默认值为False
p.name      # 进程的名称
p.pid       # 进程的pid
p.exitcode  # 进程在运行时为None、如果为–N，表示被信号N结束(了解即可)
p.authkey   # 进程的身份验证键，默认是由os.urandom()随机生成的32字符的字符串
```

</details>

<details>
<summary>Process类方法、属性(详)</summary>

```python
# todo 参数
class Process{
    group,   # 参数未使用，值始终为None
    target,  # 调用对象，即子进程要执行的任务
    args,    # 调用对象的位置参数元组
    kwargs,  # 调用对象的字典
    name,    # 子进程的名称
}

# todo 方法
p = Process()    # 创建子进程
p.start()        # 启动子进程，并调用该子进程中的 p.run() 
p.run()          # 进程启动时运行的方法
    """
    调用target指定的函数，自定义类的类中一定要实现该方法
    """
p.terminate()    # 强制终止进程
    """
    如果p创建了子进程，该子进程就成了僵尸进程，使用该方法需要特别小心这种情况
    如果p还保存了一个锁那么也将不会被释放，进而导致死锁
    """
p.is_alive()     # 如果p仍然运行，返回True
p.join(timeout)  # 主线程等待p终止，timeout是可选的超时时间
    """
    p.join只能join住start开启的进程，而不能join住run开启的进程
    """

# todo 属性
p.daemon  # 默认值为False
    """
    如果设为True，代表p为后台运行的守护进程，当p的父进程终止时，p也随之终止
    并且设定为True后（必须在p.start()之前设置），p不能创建自己的新进程
    """
p.name  # 进程的名称
p.pid  # 进程的pid
p.exitcode  # 进程在运行时为None、如果为–N，表示被信号N结束(了解即可)
p.authkey  # 进程的身份验证键，默认是由os.urandom()随机生成的32字符的字符串
    """
    这个键的用途是为涉及网络连接的底层进程间通信提供安全性，这类连接只有在具有相同的身份验证键时才能成功（了解即可）
    """

# todo 注意事项
"""
在Windows创建子进程的时候会自动 import 启动它的这个文件，
而在 import 的时候又执行了整个文件。
因此如果将process()直接写在文件中就会无限递归创建子进程报错，
所以必须把创建子进程的部分使用
if __name__ ==‘__main__’:
    pass
判断保护起来，import 的时候，就不会递归运行了。
而且不能在交互式的编辑器下面使用，比如：Jupyter 等！
"""
```

</details>

>注：
1.子进程的执行顺序不是根据启动顺序决定的
2.`p.terminate()`强制关闭进程，但是不会立即关闭，可以通过`p.is_alive()`查看是否存活
3.可以通过`p.join()`让主进程等到子进程真正结束
4.关闭子进程应先 `p.terminate()` 再 `p.join()`

<a id="Process examp"><font color="black"><h4>Process类实例</font></h4></a>

- <a id='Process0'>使用Process开启一个子进程</a>

```python
import os, time
from multiprocessing import Process

def sub_process(arg1, arg2):
    print(f"正在运行子进程......")
    print(f"子进程PID：{os.getpid()}，父进程PID：{os.getppid()}")
    time.sleep(5)
    print(f"子进程的参数：{(arg1, arg2)}")

if __name__ == "__main__":
    print(f"正在运行主进程......")
    print(f"主进程PID：{os.getpid()}")
    print(f"准备运行子进程......")
    p = Process(
        target=sub_process,
        args=("hello", 12),
    )
    print(f"开始运行子进程......")
    p.start()
    print(f"主进程等待子进程结束......")
    p.join()
    print(f"子进程运行结束......")
    print(f"主进程运行即将结束......")
```

- <a id='Process1'>继承Process开启一个子进程</a>

```python
import os, time
from multiprocessing import Process

class MyProcess(Process):
    """自定义类 继承Process类"""

    def __init__(self,arg1,arg2):
        super().__init__()
        self.arg1 = arg1
        self.arg2 = arg2

    def run(self):
        """必须实现一个run方法，run方法中是在子进程中执行的代码"""
        print(f"process name:{self.name}, PID:{self.pid},args:{(self.arg1, self.arg2)}")

if __name__ == '__main__':
    p = MyProcess(1, 2, 3, 4)
    p.start()
```

- <a id='Process2'>使用Process守护进程</a>

```python
import os, time
from multiprocessing import Process

def daemon_process():
    global i_func
    while True:
        i_func += 1
        time.sleep(0.2)
        print(f'守护进程还活着{i_func}')

def com_process():
    print('普通进程开始......')
    time.sleep(1)
    print('普通进程结束......')

i_func = 0
if __name__ == '__main__':
    p_d = Process(target=daemon_process)
    p_d.daemon = True  # 设置子进程为守护进程
    p_d.start()
    p_c = Process(target=com_process)  # 普通进程
    p_c.start()
    print(f"普通进程：{p_c.name}，是否活着：{p_c.is_alive()}")
    p_c.join()
    print(f"普通进程：{p_c.name}，是否活着：{p_c.is_alive()}")
    print("准备休眠......")
    time.sleep(1)
    print("休眠结束......")
```

- <a id='Process3'>多进程socket聊天并发</a>

```python
import socket
from multiprocessing import Process
# server


# client



```

```python
# 使用多进程实现socket聊天并发-server
from socket import *
from multiprocessing import Process

server = socket(AF_INET,SOCK_STREAM)
server.setsockopt(SOL_SOCKET,SO_REUSEADDR,1)
server.bind(('127.0.0.1',8080))
server.listen(5)

def talk(conn,client_addr):
    while True:
        try:
            msg=conn.recv(1024)
            if not msg:break
            conn.send(msg.upper())
        except Exception:
            break

if __name__ == '__main__': #windows下start进程一定要写到这下面
    while True:
        conn,client_addr=server.accept()
        p = Process(target=talk,args=(conn,client_addr))
        p.start()

#client端
from socket import *

client = socket(AF_INET,SOCK_STREAM)
client.connect(('127.0.0.1',8080))

while True:
    msg = input('>>: ').strip()
    if not msg:continue
 
    client.send(msg.encode('utf-8'))
    msg = client.recv(1024)
    print(msg.decode('utf-8'))
```

---

## 子进程

<font color="red" size=6>焯！太TM难写了，不写了，自己看博客吧.
这么多博客，总有一款适合你。</font><font size=10>:sweat_smile:</font>

[subprocess-菜鸟教程](https://www.runoob.com/w3cnote/python3-subprocess.html)

[subprocess-官方文档](https://docs.python.org/3/library/subprocess.html)

[subprocess-相关博客-博客园1](https://www.cnblogs.com/songhaixing/p/14275042.html)

[subprocess-相关博客-博客园2](https://www.cnblogs.com/-qing-/p/10934322.html)

[subprocess-相关博客-脚本之家](https://www.jb51.net/article/133941.htm)

[subprocess-相关博客-CSDN](https://blog.csdn.net/weixin_39059031/article/details/108440342)

[subprocess-相关博客-简书](https://www.jianshu.com/p/430c411160f8)

### `subprocess` 模块

`subprocess`模块，用来创建和管理子进程，并能够与创建的子进程的`stdin`、`stdout`、`stderr`连接通信，获取子进程执行结束后的<font color="orange">返回码</font>，在执行超时或执行错误时得到<font color="orange">异常</font>。

问题：
multiprocessing中，调用p.join()等待子进程执行结束
subprocess模块--父进程如何监听子进程结束？
两个方法：
subprocess.Popen()父进程开启子进程后，不管其是否结束，直接执行下一步；
subprocess.Call()父进程一直等待到子进程运行结束，再执行下一步；

- outline
  - **<a href="#subprocess intro">`subprocess` 模块介绍</a>**
  - **<a href="#subprocess examp">`subprocess` 模块实例</a>**
    - **<a href="#subprocess.run">`subprocess.run` 例子</a>**

<a id="subprocess intro"><font color="black"><h4>`subprocess` 模块介绍</h4></font></a>

```python
subprocess.run()    # 执行args参数所表示的命令，等待命令执行完毕，返回一个CompletedProcess对象。
subprocess.Popen()  # 父进程开启子进程后，不管其是否结束，直接执行下一步；
subprocess.Call()   # 不推荐使用，父进程一直等待到子进程运行结束，再执行下一步；
```

>`run`函数的底层就是`Popen`函数。`run`同步，`Popen`异步。
>**同步函数**：当一个函数是同步执行时，那么当该函数被调用时<font color="orange">不会立即返回</font>，直到该函数所要做的事情全都做完了才返回。
>**异步函数**：如果一个异步函数被调用时，该函数<font color="orange">会立即返回</font>尽管该函数规定的操作任务还没有完成。
>
>>`call`、`check_call`、`check_output`、`getoutput`、`getstatusoutput`、这些函数，都被`run`函数代替了，它们在存在只是为了保持向下兼容。

- `subprocess`的方法、类

```python
def subprocess.run(
    args,                     # 通过创建进程而执行的命令及参数
    stdin=None,               # 指定命令的输入途径
    input=None,               # 命令的具体输入内容
        """
        input与stdin不能同时使用
        input默认是一个bytes流
        """
    stdout=None,              # 指定命令的输出途径
    stderr=None,              # 指定命令的error输出途径
    capture_output=False,     # 是否捕获进程的输出
        """
        capture_output=True的效果与设置以下的效果一样。
            stdout=PIPE, stderr=PIPE
        设置了capture_output=True，就不能再设置stdout和stderr
        """
    shell=False,              # 是否通过shell来执行命令（Linux下默认为/bin/sh）
        """
        shell=False：args只能是一个不带参数的命令字符串，或者是命令和参数组成的一个list
        shell=True：args就可以是一个我们常见的命令字符串
        """
    cwd=None,                 # 指示了当前工作路径
    timeout=None,             # 设置进程执行的超时时间
        """
        如果时间到子进程还未结束，subprocess.TimeoutExpired异常会抛出。
        timeout参数的单位：秒
        """
    check=False,              # 检查异常？
        """
        如果check=True，
        在子进程的返回不为 0 的时候，抛出subprocess.CalledProcessError异常。
        这时，run函数返回的CompletedProcess对象的returncode不可用。
        """
    encoding=None,            # 
    errors=None,              # 
    text=None,                # 将stdin，stdout，stderr修改为string模式
        """
        stdin，stdout，stderr本来都是字节流bytes
        """
    env=None,                 # 
    universal_newlines=None,  # 同text
        """
        这个参数的存在也是为了向下兼容
        从Python3.7开始有text参数
        而Python3.5和3.6都是universal_newlines参数
        因此使用text参数就好了
        """
)


class subprocess.PopenPopen(
    args,                     # 通过创建进程而执行的命令及参数
    bufsize=-1,               # 
    executable=None,          # 
    stdin=None,               # 指定命令的输入途径
    stdout=None,              # 指定命令的输出途径
    stderr=None,              # 指定命令的error输出途径
    preexec_fn=None,          # 
    close_fds=True,           # 
    shell=False,              # 是否通过shell来执行命令
    cwd=None,                 # 指示了当前工作路径
    env=None,                 # 
    universal_newlines=None,  # 同text
    startupinfo=None,         # 
    creationflags=0,          # 
    restore_signals=True,     # 
    start_new_session=False,  # 
    pass_fds=(),              # 
    encoding=None,            # 
    errors=None,              # 
    text=None,                # 将stdin，stdout，stderr修改为string模式
)
```

- `subprocess.Popen`的方法

```python
proc = Popen(args)                 # 
out, err = proc.communicate(bstr)  # 
returncode = proc.wait(timeout)    # 
```

<details>
<summary>nothing</summary>

</details>

<a id="subprocess examp"><font color="black"><h4>`subprocess` 模块实例</h4></font></a>

- <a id="subprocess.run">`subprocess.run` 例子</a>

```python
# todo run args:str、shell
>>> proc = run('dir', shell=True)
······
>>> proc.args
'dir'
>>> proc.returncode
0

# todo run args:list、shell
>>> p = run(["ipconfig", "/all"], shell=True)
······
>>> proc.args
["ipconfig", "/all"]
>>> proc.returncode
0
```

---

## 一些非常有用的函数

### `multiprocessing.freeze_support()` 函数

- outline
  - **<a href="#freeze_support intro">`freeze_support()` 函数介绍</a>**
  - **<a href="#freeze_support examp">`freeze_support()` 函数实例</a>**

<a id="freeze_support intro"><font color="black"><h4>`freeze_support()` 函数介绍</h4></font></a>

- 为什么要有`freeze_support()`？

官方解释：[multiprocessing.freeze_support()¶](https://docs.python.org/3.8/library/multiprocessing.html?highlight=freeze_support#multiprocessing.freeze_support)
　　当使用`multiprocessing`的程序被冻结以生成Windows可执行文件时，添加对该程序的支持。（已使用`Py2Exe`、`PyInstaller`和`cx_Freeze`进行了测试。）
个人理解：参考：[multiprocessing.freeze_support()介绍](https://blog.csdn.net/Owen_goodman/article/details/115521388)
　　Python多进程`multiprocessing`在 Windows 的 Dos 或者 idle 下运行不了会报错，打包成.exe双击之后会一直打开exe，导致内存占满（至于为什么会造成内存溢出，暂时不得而知，后面还需要再研究一下），在 PyCharm 下运行也不会有问题，在 Linux 下没有问题。为了解决这个 bug，python3.8 添加了这个函数。

<font color="orange">简单来说就是，本来之前把使用`multiprocessing`的程序打包成.exe文件有 bug，但是现在只要在`main`入口下添加`multiprocessing.freeze_support()`就可以解决这个 bug 了。</font>

- 关于`freeze_support()`，再多说几句

1. 如果没有调用 `freeze_support()` 在尝试运行被冻结的可执行文件时会抛出 `RuntimeError` 异常。
2. 对 `freeze_support()` 的调用在非 Windows 平台上是无效的。如果该模块在 Windows 平台的 Python 解释器中正常运行 (该程序没有被冻结)， 调用`freeze_support()` 也是无效的。
3. 如果程序将正常运行而不是冻结，则可以省略 `freeze_support()`。
4. 总的来说，不管怎样最好加上 `freeze_support()`。

`freeze_support()`源代码：

```python
def freeze_support():
    '''
    Run code for process object if this in not the main process
    '''
    if is_forking(sys.argv):
        kwds = {}
        for arg in sys.argv[2:]:
            name, value = arg.split('=')
            if value == 'None':
                kwds[name] = None
            else:
                kwds[name] = int(value)
        spawn_main(**kwds)
        sys.exit()
```

<a id="freeze_support examp"><font color="black"><h4>`freeze_support()` 函数实例</h4></font></a>

```python
from multiprocessing import Process, freeze_support

def f():
    print('hello world!')

if __name__ == '__main__':
    freeze_support()
    Process(target=f).start()
```

---

## 进程同步

### `multiprocess.Lock` 类

### `multiprocess.Semaphore` 类

### `multiprocess.Event` 类

---

## 进程间的通信模式

进程间的通信模式 IPC (Inter-Process Communication)

### `multiprocessing.Pipe` 方法

- outline
  - **<a href="#Pipe intro">Pipe方法介绍</a>**
  - **<a href="#Pipe examp">Pipe方法实例</a>**
    - **<a href="#Pipe0">Pipe两个进程间通信</a>**

<a id="Pipe intro"><font color="black"><h4>Pipe方法介绍</h4></font></a>

```python
def Pipe(duplex=True)    # 通信模式
    return conn1, conn2  # 返回一个管道的两端
```

>`duplex=True`：全双工模式（默认），双方均可收发
`duplex=False`：conn1只接收，conn2只发送

<details>
<summary>Pipe方法(略)</summary>

```python
pipe = Pipe()                           # 
pipe[0].recv(msg)                       # 接收
pipe[1].send(msg)                       # 发送
conn.close()                            # 
conn.fileno()                           # 
conn.poll([timeout])                    # 
conn.recv_bytes([maxlength])            # 
conn.send_bytes(buffer, offset, size)   # 
conn.recv_bytes_into(buffer, offset)    # 
```

</details>

<details>
<summary>Pipe方法(详)</summary>

```python
pipe = Pipe()
pipe[0].recv(msg)  # 接收
pipe[1].send(msg)  # 发送
conn.close()
    """
    关闭连接，如果conn被垃圾回收，将自动调用此方法
    """
conn.fileno()
    """
    返回连接使用的整数文件描述符
    """
conn.poll([timeout])
    """
    如果连接上的数据可用，返回True。
    timeout指定等待的最长时限。
    如果省略此参数，方法将立即返回结果。
    如果将timeout射成None，操作将无限期地等待数据到达。
    """
conn.recv_bytes([maxlength])
    """
    接收 conn.send_bytes() 方法发送的一条完整的字节消息。
    maxlength指定要接收的最大字节数。
    如果进入的消息，超过了这个最大值，将引发IOError异常，并且在连接上无法进行进一步读取。
    如果连接的另外一端已经关闭，再也不存在任何数据，将引发EOFError异常。
    """
conn.send_bytes(buffer [, offset [, size]])
    """
    通过连接发送字节数据缓冲区，
    buffer是支持缓冲区接口的任意对象，
    offset是缓冲区中的字节偏移量，
    size是要发送字节数。
    结果数据以单条消息的形式发出，
    然后调用 conn.recv_bytes() 函数进行接收
    """
conn.recv_bytes_into(buffer [, offset])
    """
    接收一条完整的字节消息，并把它保存在buffer对象中，
    该对象支持可写入的缓冲区接口（即bytearray对象或类似的对象）。
    offset指定缓冲区中放置消息处的字节位移。
    返回值是收到的字节数。
    如果消息长度大于可用的缓冲区空间，将引发BufferTooShort异常。
    """
```

</details>

>注：
1.Pipe 的读写效率要高于 Queue
2.如果没有消息可接收，`conn.recv()`方法会一直阻塞。
3.如果管道已经被关闭，`conn.recv()`方法会抛出`EOFError`。
4.`conn.close()`并未关闭通道，只是把状态改为不可再插入元素的状态
5.Pipe是通过 socket 进行进程间通信的

<a id="Pipe examp"><font color="black"><h4>Pipe方法实例</h4></font></a>

- <a id="Pipe0">Pipe两个进程间通信</a>

pipe实现生产者和消费者一对一模型

```python
import os, time, random
from multiprocessing import Process, Pipe

def producer(pipe):
    for product in products:
        print('生产者发送:{}'.format(product))
        pipe.send(product)
        time.sleep(0.2)

def consumer(pipe):
    while True:
        try:
            item = pipe.recv()
            print('消费者接收:{}'.format(item))
            time.sleep(0.2)
        except EOFError as e:
            print('生产者跑路了')
            break

products = [random.randint(1, 10) for i in range(10)]
if __name__ == "__main__":
    pipe = Pipe(duplex=False)

    process_producer = Process(target=producer, args=(pipe[1],), )
    process_consumer = Process(target=consumer, args=(pipe[0],), )

    process_producer.start()
    process_consumer.start()

    pipe[0].close()
    pipe[1].close()

    process_producer.join()
    process_consumer.join()
```

>注：
1.若双方都没有使用管道的某个端点，就应该关闭管道
2.若一方关闭了端点，相应的另一端必须关闭其端点
3.应先关闭通道两端`pipe.close()`再关闭进程`proc.terminate()`再等待进程结束`proc.join()`

---

### `multiprocessing.Queue` 类

- outline
  - **<a href="#Queue intro">Queue类介绍</a>**
  - **<a href="#Queue examp">Queue类实例</a>**
    - **<a href="#Queue0">Queue多个进程间通信</a>**

<a id="Queue intro"><font color="black"><h4>Queue类介绍</h4></font></a>

```python
# todo 参数
class Queue{maxsize=0}  # 队列中允许的最大项数，忽略/默认无大小限制
```

<details>
<summary>Queue类方法(略)</summary>

```python
# todo 方法
q = Queue()                  # 队列实例化
q.get(block, timeout)        # 数据出队，阻塞
q.get_nowait()               # 数据出队，非阻塞
q.put(item, block, timeout)  # 数据入队，阻塞
q.put_nowait()               # 数据入队，非阻塞
q.qsize()                    # 队列大小
q.empty()                    # 队列判空
q.full()                     # 队列判满
q.close()                    # 关闭队列
q.cancel_join_thread()       # 取消队列连接后台线程
q.join_thread()              # 队列连接后台线程
```

</details>

<details>
<summary>Queue类方法(详)</summary>

```python
# todo 方法
q = Queue()
q.get( [ block [ ,timeout ] ] )
    """
    返回q中的一个项目。
    如果q为空，此方法将阻塞，直到队列中有项目可用为止。
    block用于控制阻塞行为，默认为True。
    如果设置为False，将引发Queue.Empty异常（定义在Queue模块中）。
    timeout是可选超时时间，用在阻塞模式中。
    如果在制定的时间间隔内没有项目变为可用，将引发Queue.Empty异常。
    """
q.get_nowait()
    """
    同q.get(False)方法。
    """
q.put(item [, block [,timeout ] ] )
    """
    将item放入队列。
    如果队列已满，此方法将阻塞至有空间可用为止。
    block控制阻塞行为，默认为True。
    如果设置为False，将引发Queue.Empty异常（定义在Queue库模块中）。
    timeout指定在阻塞模式中等待可用空间的时间长短。
    超时后将引发Queue.Full异常。
    """
q.put_nowait()
    """
    同q.get(False)方法。
    """
q.qsize()
    """
    返回队列中目前项目的正确数量。
    此函数的结果并不可靠，
    因为在返回结果和在稍后程序中使用结果之间，
    队列中可能添加或删除了项目。
    在某些系统上，此方法可能引发NotImplementedError异常。
    """
q.empty()
    """
    如果调用此方法时 q为空，返回True。
    如果其他进程或线程正在往队列中添加项目，结果是不可靠的。
    也就是说，在返回和使用结果之间，队列中可能已经加入新的项目。
    """
q.full()
    """
    如果q已满，返回为True。
    由于线程的存在，结果也可能是不可靠的（参考q.empty（）方法）。
    """
q.close()
    """
    关闭队列，防止队列中加入更多数据。
    调用此方法时，后台线程将继续写入那些已入队列但尚未写入的数据，
    但将在此方法完成时马上关闭。
    如果q被垃圾收集，将自动调用此方法。
    关闭队列不会在队列使用者中生成任何类型的数据结束信号或异常。
    例如，如果某个使用者正被阻塞在get（）操作上，
    关闭生产者中的队列不会导致get（）方法返回错误。
    """
q.cancel_join_thread()
    """
    不会再进程退出时自动连接后台线程。
    这可以防止join_thread()方法阻塞。
    """
q.join_thread()
    """
    连接队列的后台线程。
    此方法用于在调用q.close()方法后，
    等待所有队列项被消耗。
    默认情况下，此方法由不是q的原始创建者的所有进程调用。
    调用q.cancel_join_thread()方法可以禁止这种行为。
    """
```

</details>
<!-- <br/> -->

>注：
1.底层队列使用管道和锁定实现
2.`q.put()`和`q.get()`是两个阻塞方法
3.`q.put_nowait()`和`q.get_nowait()`是两个非阻塞方法，故一定要做异常处理！！！
4.两个进程的 Queue 并不是同一个，而是将数据 pickle 后传给另一个进程的 Queue
5.Queue用于父进程与子进程之间的通信或同一父进程的子进程之间通信

<a id="Queue examp"><font color="black"><h4>Queue类实例</h4></font></a>

- <a id="Queue0">Queue多个进程间通信</a>

Queue实现一方发送多方接收

```python
import os, time, random
from multiprocessing import Process, Queue

def proc_put(q, data, proc_name):
    for dat in data:  # 数据发送完就跳出
        try:
            q.put_nowait(dat)  # 数据入队
            print(f"{proc_name}方发送数据{dat}")
        except:
            print(f"队列满了！")
        time.sleep(0.1)

def proc_get(q, proc_name):
    while q.qsize():  # 数据接收完(没有数据)就跳出
        try:
            dat = q.get_nowait()  # 数据出队
            print(f"{proc_name}方接收数据{dat}")
        except:
            print(f"队列空了！")
        time.sleep(0.1)

if __name__ == "__main__":
    q = Queue(5)
    data = [random.randint(1, 100) for i in range(10)]
    proc_send1 = Process(target=proc_put, args=(q, data, "process send1"))
    proc_recv1 = Process(target=proc_get, args=(q, "process recv1"))
    proc_recv2 = Process(target=proc_get, args=(q, "process recv2"))

    proc_send1.start()
    proc_recv1.start()
    proc_recv2.start()

    proc_send1.join()
    proc_recv1.join()
    proc_recv2.join()
```

---

### `multiprocessing.JoinableQueue` 类

可连接的共享进程队列

- outline
  - **<a href="#JQ intro">JoinableQueue类介绍</a>**
  - **<a href="#JQ examp">JoinableQueue类实例</a>**

<a id="JQ intro"><font color="black"><h4>JoinableQueue类介绍</h4></font></a>

```python
class JoinableQueue{maxsize=0}  # 队列中允许的最大项数，忽略/默认无大小限制
```

<font color="grey">JoinableQueue的实例p除了与Queue对象相同的方法之外，还具有以下方法</font>

<details>
<summary>JoinableQueue类方法(略)</summary>

```python
q.task_done()  # 发出信号表示q.get()返回的项目已经被处理
q.join()       # 阻塞直到队列所有项目被处理为止，即所有项目被调用q.task_done()为止
```

<font>说白了，`q.task_done()`就是告诉 os 已经完成这一次的`q.get()`了，`q.join()`告诉 os：Queue已经取完、没有元素了，并且等待结束。</font>

</details>

<details>
<summary>JoinableQueue类方法(详)</summary>

```python
q.task_done()
    """
    使用者使用此方法发出信号，表示q.get()返回的项目已经被处理。
    如果调用此方法的次数大于从队列中删除的项目数量，将引发ValueError异常。
    """
q.join()
    """
    生产者将使用此方法进行阻塞，直到队列中所有项目均被处理。
    阻塞将持续到为队列中的每个项目均调用q.task_done()方法为止。 
    """
```

</details>

<a id="JQ examp"><font color="black"><h4>JoinableQueue类实例</h4></font></a>

```python
import time, random
from multiprocessing import Process, JoinableQueue

def consumer(q, name):
    while True:
        food = q.get()
        time.sleep(random.uniform(0.2, 0.4))
        print('%s吃了一个%s' % (name, food))
        q.task_done()  # 通知队列已经有一个数据被处理了

def producer(q, name, food):
    for i in range(10):
        time.sleep(random.uniform(0.2, 0.4))
        print('%s生产了%s%s' % (name, food, i + 1))
        q.put(food + str(i))

if __name__ == '__main__':
    q = JoinableQueue()
    proc_con = Process(target=consumer, args=(q, 'con'))
    proc_prod = Process(target=producer, args=(q, 'pro', '馒头'))
    proc_con.daemon = True  # 设置守护进程，否则永远运行，join()也没用
    proc_con.start()
    proc_prod.start()
    # proc_con.join()        # 不要使用join()，否则永远运行
    proc_prod.join()         # 生产者要先把所有的数据都放到队列中
    q.join()                 # 阻塞直到放入队列中所有的数据都被处理掉(有多少个数据就接收到了多少task_done)
```

---

## 进程间的数据共享

### `multiprocessing.Value` 方法

### `multiprocessing.Array` 方法

`Value`和`Array`差不多（`Value`和`Array`分别相当于数和数组），就放在一起了，它们都是将数据存储在<font color="red">共享内存</font>映射中。

在进行并发编程时，通常最好尽量避免使用共享状态。使用多个进程时尤其如此。但是，如果你真的需要使用一些共享数据，那么 `multiprocessing`提供了两种方法：`Value`和`Array`。

>`Value`和`Array`是<font color="red">线程不安全</font>的，所以以下内容了解即可，因此建议使用线程安全的`Manager().Value`和`Manager().Array`。

- outline
  - **<a href="#VA intro">`Value`和`Array`方法介绍</a>**
  - **<a href="#VA examp">`Value`和`Array`方法实例</a>**

<a id="VA intro"><font color="black"><h4>`Value`和`Array`方法介绍</h4></font></a>

```python
def Value(typecode_or_type, *args, lock=True)
def Array(typecode_or_type, size_or_initializer, lock=True)
    """
    参数：
        typecode_or_type：返回 ctypes 对象的类型
        *args：传递给 ctypes 的构造参数
        size_or_initializer：数组的长度或者初始化数组的序列
        lock：是否加锁
    返回：
        以上方法返回从共享内存中分配的一个 ctypes 对象
    说明：
        1.size_or_initializer：如果它是一个整数，那么它确定数组的长度，并且数组将被初始化为零。
        否则，size_or_initializer是用于初始化数组的序列，其长度决定数组的长度。
        
        2. ctypes 是 Python 的一个外部函数库，它提供了和C语言兼任的数据类型，
        可以调用 DLLs 或者共享库的函数，能被用作在 python 中包裹这些库。
    例如：
        i = Value('i', 1)
        d = Value('d', 1)
        c = Value('c', '0')
    """
```

`'d'`和`'i'`参数是`array`模块使用的类型的`typecode`。`'d'`表示双精度浮点数，`'i'`表示有符号整数。<font color="red">这些共享对象将是进程和线程安全的。</font>

为了更灵活地使用共享内存，可以使用`multiprocessing.sharedctypes`模块，该模块支持创建从共享内存分配的任意ctypes对象。

```python
# todo Value属性
v = Value("i", 10)
v.value
```

- **共享整数或者单个字符**

共享整数或者单个字符的初始化比较简单，可以直接在下面的<font color="red">code映射表</font>找到对应关系。

<img src="python进程-Value-Type_code.png" width="800">

`Value`初始化整数、字符例子：

```python
from multiprocessing import Value

i = Value('i', 1)
c = Value('c', '0')
print(i)
print(c)
```

- **共享字符串**

如果共享的是字符串，则在上表是找不到映射关系的，就是没有code可用。所以我们需要使用原始的ctype类型：<font color="red">ctype类型对应表</font>。

<img src="python进程-Value-ctypes_type.png" width="830">

`Value`初始化字符串例子：

```python
from ctypes import c_char_p
from multiprocessing import Value, Manager

# ss = Value(c_char_p, 'hello')  # 这里在我的环境会报错，使用下面的Manager则不会
ss = Manager().Value(ctypes.c_char_p, 'hello')
print(ss)
```

<a id="VA intro"><font color="black"><h4>`Value`和`Array`方法实例</h4></font></a>

```python
from multiprocessing import Process, Value, Array

def f(n, a):
    n.value = 3.1415927
    for i in range(len(a)):
        a[i] = -a[i]

if __name__ == '__main__':
    num = Value('d', 0.0)
    arr = Array('i', range(10))

    p = Process(target=f, args=(num, arr))
    p.start()
    p.join()

    print(num.value)
    print(arr[:])
```

---

### `multiprocessing.Manager` 方法

`Manager`是通过共享进程的方式<font color="red">共享数据</font>。<font color="grey">简单来说就是，虽然进程间的数据不共享，但是一个进程可以通过`Manager`修改其他进程的数据。</font>

`Manager`管理的共享数据类型有：`Value`、`Array`、`dict`、`list`、`Lock`、`Semaphore`等等，同时`Manager`还可以共享类的实例对象。

`Manager`不需要加锁，因为`Manager`已经默认给你加锁了。

- outline
  - **<a href="#Manager intro">`Manager`方法介绍</a>**
  - **<a href="#Manager examp">`Manager`方法实例</a>**

<a id="Manager intro"><font color="black"><h4>Manager方法介绍</h4></font></a>

```python
manager = Manager()  # 实例化Manager对象
manager.Value()      # 
manager.Array()      # 
manager.dict()       # 
manager.list()       # 
manager.Lock()       # 
manager.Semaphore()  # 
```

<a id="Manager examp"><font color="black"><h4>Manager方法实例</h4></font></a>

```python
from multiprocessing import Process, Manager

def func1(shareList, shareValue, shareDict, lock):
    with lock:
        shareValue.value += 1
        shareDict[1] = '1'
        shareDict[2] = '2'
        for i in range(len(shareList)):
            shareList[i] += 1

if __name__ == '__main__':
    manager = Manager()
    list1   = manager.list([1, 2, 3, 4, 5])
    value1  = manager.Value('i', 1)
    dict1   = manager.dict()
    lock1   = manager.Lock()

    proc = [
        Process(
            target=func1,
            args=(list1, value1, dict1, lock1),
        ) for i in range(20)
    ]

    for p in proc:
        p.start()
    for p in proc:
        p.join()
    print(list1)
    print(value1)
    print(dict1)

    array1 = manager.Array('i', range(10))
    print(array1)
```

运行结果：

```shell
[21, 22, 23, 24, 25]
Value('i', 21)
{1: '1', 2: '2'}
array('i', [0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

---

## 进程池

### `multiprocessing.Pool` 方法

- outline
  - **<a href="#Pool intro">Pool方法介绍</a>**
  - **<a href="#Pool examp">Pool方法实例</a>**
    - **<a href="#Pool0">使用多线程-应用apply</a>**
    - **<a href="#Pool1">使用多线程-映射map</a>**

<a id="Pool intro"><font color="black"><h4>Pool方法介绍</h4></font></a>

Pool可以提供指定数量的进程，供用户调用，当有新的请求提交到Pool中时，如果池还没有满，那么就会创建一个新的进程用来执行该请求；但如果池中的进程数已经达到规定最大值，那么该请求就会等待，直到池中有进程结束，才会创建新的进程来它。

<details>
<summary>Pool方法(略)</summary>

```python
p.apply(func, args, kwds)                  # 阻塞
p.apply_async(func, args, kwds, callback)  # 非阻塞
p.close()                                  # 关闭pool
p.terminate()                              # 结束工作进程
p.join()                                   # 主进程阻塞，等待子进程的退出
```

</details>

<details>
<summary>Pool方法(详)</summary>

```python
p.apply(func, args, kwds)                  # 阻塞
p.apply_async(func, args, kwds, callback)  # 异步非阻塞
p.close()                                  # 关闭pool，不再接受新的任务
p.terminate()                              # 结束工作进程，不再处理未完成的任务
p.join()                                   # 主进程阻塞，等待子进程的退出

1 方法apply_async()和map_async()的返回值是AsyncResul的实例obj。实例具有以下方法
2 obj.get():返回结果，如果有必要则等待结果到达。timeout是可选的。如果在指定时间内还没有到达，将引发一场。
如果远程操作中引发了异常，它将在调用此方法时再次被引发。
3 obj.ready():如果调用完成，返回True
4 obj.successful():如果调用完成且没有引发异常，返回True，如果在结果就绪之前调用此方法，引发异常
5 obj.wait([timeout]):等待结果变为可用。
6 obj.terminate()：立即终止所有工作进程，同时不执行任何清理或结束任何挂起工作。如果p被垃圾回收，将自动调用此函数
```

</details>

<a id="Pool examp"><font color="black"><h4>Pool方法实例</h4></font></a>

- <a id="Pool0">使用多线程-应用apply</a>

```python
import time
from multiprocessing import Pool

def func(msg):
    print("msg: ", msg)
    time.sleep(0.5)
    print("func end")

if __name__ == "__main__":
    pool = Pool(processes=3)
    for i in range(5):
        msg = "hello world{}".format(i)
        # pool.apply(func=func, args=(msg, ))        # 阻塞！
        pool.apply_async(func=func, args=(msg, ))  # 非阻塞！
    print("close......")
    pool.close()
    pool.join()
    print("done......")
```

>注：
1.调用join之前，先调用close或者函数terminate，否则会出错
2.执行完close后不会有新的进程加入到pool,join函数等待所有子进程结

- <a id="Pool1">使用多线程-映射map</a>

```python
import time
from multiprocessing import Pool

def task(num):
    time.sleep(1)
    print('num:%d; PID:%s' % (num, os.getpid()))
    return num**2

if __name__ == "__main__":
    p = Pool()
    p.map(task, range(20))        # 阻塞
    # p.map_async(task, range(20))  # 异步非阻塞
```

>`map()`和`map_async()`参数为迭代器类型

---

## 分布式进程

分布式进程：将Process进程分布到多台机器上，充分利用多态机器的性能完成复杂的任务。通常会有一个服务进程作为调度者，将任务分布到其他多个进程中，依靠网络通信进行管理。

举个例子：使用多进程做爬虫程序时，一般会有两个进程。一个进程负责抓取链接地址，将链接地址放到queue中；另一个进程负责从queue中取链接地址进行下载和存储到本地。其中，queue需要暴露到进程通信的网络中。

创建分布式进程的<font color="red">大致步骤</font>（具体需求不同会有差异）：

- **STEP1**：实例化Queue对象

得有个队列Queue来存储任务/数据，用于不同进程的通信。
<font color="orange">代码</font>：`queue = queue.Queue()`

- **STEP2**：注册Queue对象

在实例化Queue对象后，还需要把Queue对象暴露在通信网络中才能使用。服务端需要注册对象及其方法，而客户端 只需要注册方法，不需要注册对象。
<font color="orange">代码</font>：
服务端：`BaseManager.register("get_queue", callable=lambda: queue)`
客户端：`BaseManager.register("get_queue")`

- **STEP3**：实例化Manager对象

为了实现不同进程/主机间的通信，得绑定地址、端口和验证口令。
<font color="orange">代码</font>：`manager = BaseManager(address=('127.0.0.1', 8001), authkey=b'pwword')`

- **STEP3.5**：冻结支持

由于python在使用Manager时会出现一些 bug，需要在`mari()`里面使用冻结支持函数，之后才能正常进行开启服务、连接服务等操作。（上面有讲，忘记的话可以翻上去看一下）
<font color="orange">代码</font>：`freeze_support()`

- **STEP4**：服务端开启，客户端连接

服务器开启服务 或者 启动管理、监听信道；客户端连接服务端。
<font color="orange">代码</font>：

1. 服务器启动管理：`manager.start()`
2. 服务器开启服务：
　　先获取服务器对象`server = manager.get_server()`
　　　　　　　　然后永久开启服务：`server.serve_forever()`
3. 客户端连接服务端：`manager.connect()`

- **STEP5**：从Manager对象获得Queue对象

从Manager获取Queue，用于分配、执行任务
<font color="orange">代码</font>：`queue = manager.get_queue()`

- **STEP6**：分配、执行任务

一方(不一定是服务器)分配任务，另一方执行任务。
<font color="orange">代码</font>：
分配：`queue.put(task)`
执行：`queue.get()`

- **STEP7**：结束任务

如果服务端使用永久开启服务(**STEP4**中的2.)，那么就不需要结束任务，否则就要结束任务进程。
<font color="orange">代码</font>：`manager.shutdown()`

<details><summary>关于分布式进程中的队列Queue一些话</summary>

```python
"""
注意，当我们在一台机器上写多进程程序时，创建的Queue可以直接拿来用，
但是，在分布式多进程环境下，添加任务到Queue不可以直接对原始的task_queue进行操作，
那样就绕过了QueueManager的封装，必须通过manager.get_task_queue()获得的Queue接口添加。


任务进程要通过网络连接到服务进程，所以要指定服务进程的IP
先启动task_master.py服务进程
task_master.py进程发送完任务后，开始等待result队列的结果。
再启动task_worker.py进程
task_worker.py进程结束，在task_master.py进程中会继续打印出结果


Queue对象存储在哪？
注意到task_worker.py中根本没有创建Queue的代码，
所以，Queue对象存储在task_master.py进程中，即server
而Queue之所以能通过网络访问，就是通过QueueManager实现的。
由于QueueManager管理的不止一个Queue，
所以，要给每个Queue的网络调用接口起个名字，比如get_task_queue。
authkey有什么用？
这是为了保证两台机器正常通信，不被其他机器恶意干扰。
如果task_worker.py的authkey和task_master.py的authkey不一致，肯定连接不上。
"""
```

</details>

### `multiprocessing.managers` 模块

- outline
  - **<a href="#managers intro">managers类介绍</a>**
  - **<a href="#managers examp">managers类实例</a>**
    - **<a href="#managers1">managers示例1：使用BaseManager进行分布式进程任务（共三方参与）</a>**
    - **<a href="#managers2">managers示例2：使用BaseManager进行分布式进程任务（共两方参与）</a>**
    - **<a href="#managers3">managers示例3：使用BaseManager进行分布式进程任务（一对多）</a>**

<a id="managers intro"><font color="black"><h4>managers类介绍</h4></font></a>

```python
# todo 类
class BaseManager(
    address=None,         # 地址
    authkey=None,         # 身份验证
    serializer='pickle',  # 序列化程序
    ctx=None,             # 断续器？
):
```

<details>
<summary>BaseManager 类方法(略)</summary>

```python
# todo 类方法
BaseManager.start()       # 为manager对象生成服务器进程
BaseManager.connect()     # 将manager对象连接到服务器进程
BaseManager.register()    # 使用manager类型注册类型 ID
BaseManager.get_server()  # 返回具有serve_forever()方法和地址属性的服务器对象
BaseManager.join()        # 加入manager进程（如果已生成）
BaseManager.shutdown()    # 关闭manager进程
```

</details>

<details><summary>BaseManager 类方法(详)</summary>
————————————
<details><summary>BaseManager.start()函数</summary>

```python
def start(initializer=None, initargs=())
    """
    启动manager
    """
```

具体使用见<font color="red">例2</font>

</details>
————————————
<details><summary>BaseManager.connect()函数</summary>

```python
def connect()
    """
    连接服务器进程
    """
```

具体使用见<font color="red">例1</font>

</details>
————————————
<details><summary>BaseManager.register()函数</summary>

```python
def BaseManager.register(
    typeid,             # 字符串，类型标识符
    callable,           # 可调用对象的
    proxytype,          # 
    exposed,            # 
    method_to_typeid,   # 
    create_method,      # 
):
```

>说得通俗一点：
1.`register`函数干的事就是给`BaseManager`类注册一个函数（类似动态绑定？）
2.`typeid`参数就是注册对象可以调用的函数
3.`callable`参数一般是队列的实例化对象，一般用函数返回，如下所示：

```python
import queue
from multiprocessing import BaseManager
q = queue.Queue()

# todo 法1：定义函数
def get_q():
    global q
    return q
BaseManager.register('get_q', callable=get_q)

# todo 法2：匿名函数
BaseManager.register('get_q', callable=lambda: q)

# 建议使用标准函数来代替lambda函数，避免python2.7中，pickle无法序列化lambda的问题，虽然但是现在基本都在用python3
```

  <details><summary>BaseManager.register() 的(官方？)参数描述(已翻译)：</summary>

```python
def register(
    typeid, 
        """
        typeid is a “type identifier” which is used to identify a particular type of shared object. This must be a string.
        typeid 是一个“类型标识符”，用于标识特定类型的共享对象。这必须是一个字符串。
        """
    callable, 
        """
        callable is a callable used for creating objects for this type identifier. 
        callable 是一个可调用的使用，用于为此类型标识符创建对象。

        If a manager instance will be created using the from_address() classmethod or if the create_method argument is False then this can be left as None.
        如果将使用from_address()类方法或 create_method=False 创建 Manager 实例，则这可以留下。
        """
    proxytype, 
        """
        proxytype is a subclass of BaseProxy which is used to create proxies for shared objects with this typeid. 
        proxytype 是BaseProxy的子类，用于创建具有此类型的共享对象的代理。

        If None then a proxy class is created automatically.
        如果 proxytype=None，则自动创建代理类proxy。
        """
    exposed, 
        """
        exposed is used to specify a sequence of method names which proxies for this typeid should be allowed to access using BaseProxy._callmethod(). 
        exposed 用于指定应允许使用 BaseProxy._callMethod() 的代理的方法名称的序列。

        (If exposed is None then proxytype._exposed_ is used instead if it exists.) 
        （如果 exposed=None，则不使用proxytype._exposed_如果存在。）

        In the case where no exposed list is specified, all “public methods” of the shared object will be accessible. 
        在未指定未公开列表的情况下，将可以访问共享对象的所有“公共方法”。

        (Here a “public method” means any attribute which has a __call__() method and whose name does not begin with '_'.)
        （这里有一个“公共方法”是指具有 __call __()方法的任何属性，其名称不会以'_'开头。）
        """
    method_to_typeid, 
        """
        method_to_typeid is a mapping used to specify the return type of those exposed methods which should return a proxy. It maps method names to typeid strings. 
        method_to_typeId 是用于指定应返回代理的那些公开方法的返回类型的映射。它将方法名称映射到 typeid 字符串。

        (If method_to_typeid is None then proxytype._method_to_typeid_ is used instead if it exists.) 
        （如果method_to_typeId=None，则使用proxytype._method_to_typeid_，而不是存在。）

        If a method’s name is not a key of this mapping or if the mapping is None then the object returned by the method will be copied by value.
        如果方法的名称不是此映射的键，或者如果映射为None，则该方法返回的对象将被值复制。
        """
    create_method, 
        """
        create_method determines whether a method should be created with name typeid which can be used to tell the server process to create a new shared object and return a proxy for it. By default it is True.
        create_method 确定是否应该使用名称类型创建方法，该类型可以用于告诉服务器进程创建新的共享对象并返回代理。默认create_method=True。
        """
):
    """
    A classmethod which can be used for registering a type or callable with the manager class.
    一个类方法，可用于注册类型或可调用manager类。
    """
```

  </details>
</details>
————————————
<details><summary>BaseManager.get_server() 函数</summary>

```python
def get_server()
    """
    返回有serve_forever()方法的服务器对象
    """
```

具体使用见<font color="red">例1</font>

</details>
————————————
<details><summary>BaseManager.join() 函数</summary>

这个函数没啥好说的，可以参考之前的`join`，但是貌似在这很少用到。。。。。。

```python
def join(timeout=None)
```

</details>
————————————
<details><summary><font>BaseManager.shutdown() 函数</font></summary>

```python
def BaseManager.shutdown()
    """
    只有当进程start()之后才能使用，用于关闭manager进程
    """
```

具体使用见<font color="red">例2</font>

</details>
————————————
</details>

</details>

<a id="managers examp"><font color="black"><h4>managers类实例</h4></font></a>

————————————————————————————————————————

- <a id="managers1">managers示例1：使用BaseManager进行分布式进程任务（共三方参与）</a>

实例说明：本例中继承了`BaseManager`类，当然，把其中的`QueueManager`换成`BaseManager`也是完全没问题的。

<font color="red">服务端Server</font>

```python
import queue
from multiprocessing.managers import BaseManager

que = queue.Queue()


class QueueManager(BaseManager):
    pass


QueueManager.register("get_queue", callable=lambda:que)
m = QueueManager(address=('127.0.0.1', 50000), authkey="abc".encode())

s = m.get_server()
s.serve_forever()
```

<font color="red">发送端Send</font>

```python
from multiprocessing.managers import BaseManager


class QueueManager(BaseManager):
    pass


QueueManager.register("get_queue")
m = QueueManager(address=("127.0.0.1", 50000), authkey="abc".encode())
m.connect()

queue = m.get_queue()
queue.put("hello")
```

<font color="red">接收端Recv</font>

```python
from multiprocessing.managers import BaseManager


class QueueManager(BaseManager):
    pass


QueueManager.register("get_queue")
m = QueueManager(address=("127.0.0.1", 50000), authkey="abc".encode())
m.connect()

queue = m.get_queue()
print(queue.get())
print(m.address)
```

>使用方法：依次启动服务端、发送端和接收端即可。

————————————————————————————————————————

- <a id="managers2">managers示例2：使用BaseManager进行分布式进程任务（共两方参与）</a>

实例说明：manager服务端用于控制，worker工作及用于执行。这个例子创建了两个队列，大体思路差不多，具体步骤可展开下面内容查看。

<details><summary>创建分布式进程需要的<font color="red">步骤</font>：</summary>

- **一、建立用于进程间通信的队列Queue**

服务进程创建任务队列 task_queue 和结果队列 result_queue。任务队列 task_queue 用来作为<font color="orange">传递任务给任务进</font>程的通道；服务进程创建结果队列 result_queue 用来作为任务进程<font color="orange">完成任务后回复服务进程</font>的通道。在分布式多进程环境下，必须由 Queuemanager 获得 Queue 接口来添加任务。

- **二、在网络上注册第一步中建立的队列，暴露给其他进程（主机）**

注册后获得网络队列，相当于本地队列的映像。

- **三、建立对象实例manager，绑定端口和验证口令**

对象的类Queuemanager继承自BaseManager

- **四、启动管理实例manager，监管信息通道**

即：启动第三步中建立的实例

- **五、通过管理实例的方法获得通过网络访问的Queue对象**

即：再把网络队列实体化成可以使用的本地队列

- **六、创建任务、上传任务、分配任务**

创建任务到 “本地”队列中，自动上传任务到网络队列中，分配给任务进程进行处理。
</details>

<font color="red">manager服务器</font>

```python
from multiprocessing.managers import BaseManager
from multiprocessing import freeze_support, Queue

# 任务个数
task_number = 10

# 收发队列
task_quue = Queue(task_number)
result_queue = Queue(task_number)


def get_task():
    return task_quue


def get_result():
    return result_queue


# 创建类似的queueManager
class QueueManager(BaseManager):
    pass


def win_run():
    # 注册在网络上，callable 关联了Queue 对象
    # 将Queue对象在网络中暴露
    #window下绑定调用接口不能直接使用lambda，所以只能先定义函数再绑定
    QueueManager.register('get_task_queue', callable=get_task)
    QueueManager.register('get_result_queue', callable=get_result)
    # 绑定端口和设置验证口令
    manager = QueueManager(address=('127.0.0.1', 8001), authkey='qiye'.encode())

    # 启动管理，监听信息通道
    manager.start()

    try:

        # 通过网络获取任务队列和结果队列
        task = manager.get_task_queue()
        result = manager.get_result_queue()

        # 添加任务
        for url in ["ImageUrl_" + str(i) for i in range(10)]:
            print('url is %s' % url)
            task.put(url)

        print('try get result')
        for i in range(10):
            print('result is %s' % result.get(timeout=1000))

    except:
        print('Manager error')
    finally:
        manager.shutdown()


if __name__ == '__main__':
    # window下多进程可能有问题，添加这句话缓解
    freeze_support()
    win_run()
```

<font color="red">worker工作机</font>

```python
import time
from multiprocessing.managers import BaseManager

# 创建类似的QueueManager:
class QueueManager(BaseManager):
    pass

# 实现第一步：使用QueueManager注册获取Queue的方法名称
QueueManager.register('get_task_queue')
QueueManager.register('get_result_queue')

# 实现第二步：连接到服务器:
server_addr = '127.0.0.1'
print('Connect to server %s...' % server_addr)
# 端口和验证口令注意保持与服务进程设置的完全一致:
m = QueueManager(address=(server_addr, 8001), authkey='qiye'.encode())
# 从网络连接:
m.connect()

# 实现第三步：获取Queue的对象:
task = m.get_task_queue()
result = m.get_result_queue()

# 实现第四步：从task队列取任务,并把结果写入result队列:
while(not task.empty()):
        image_url = task.get(True,timeout=5)
        print('run task download %s...' % image_url)
        time.sleep(1)
        result.put('%s--->success'%image_url)
# 处理结束:
print('worker exit.')
```

————————————————————————————————————————

- <a id="managers3">managers示例3：使用BaseManager进行分布式进程任务（一对多）</a>

实例说明：与例2差不多，依次启动服务端和多个客户端即可。

<font color="red">服务端manager</font>

```python
# encoding:utf-8

import random, time, queue
from multiprocessing.managers import BaseManager

# 发送任务的队列
task_queue = queue.Queue()
# 接收结果的队列
result_queue = queue.Queue()


# 使用标准函数来代替lambda函数，避免python2.7中，pickle无法序列化lambda的问题
def get_task_queue():
    global task_queue
    return task_queue


# 使用标准函数来代替lambda函数，避免python2.7中，pickle无法序列化lambda的问题
def get_result_queue():
    global task_queue
    return task_queue


def startManager(host, port, authkey):
    # 把两个Queue都注册到网络上，callable参数关联了Queue对象，注意回调函数不能使用括号
    BaseManager.register('get_task_queue', callable=get_task_queue)
    BaseManager.register('get_result_queue', callable=get_result_queue)
    # 设置host,绑定端口port，设置验证码为authkey
    manager = BaseManager(address=(host, port), authkey=authkey)
    # 启动manager服务器
    manager.start()
    return manager


def put_queue(manager):
    # 通过网络访问queueu
    task = manager.get_task_queue()
    while 1:
        n = random.randint(0, 1000)
        print ('Put task %d' % n)
        task.put(n)
        time.sleep(0.5)


if __name__ == "__main__":
    host = '127.0.0.1'
    port = 5000
    authkey = 'abc'
    # 启动manager服务器
    manager = startManager(host, port, authkey.encode())
    # 给task队列添加数据
    put_queue(manager)
    # 关闭服务器
    manager.shutdown()
```

<font color="red">执行端worker</font>

```python
# encoding:utf-8

import random, time, queue
from multiprocessing.managers import BaseManager


def start_worker(host, port, authkey):
    # 由于这个BaseManager只从网络上获取queue，所以注册时只提供名字
    BaseManager.register('get_task_queue')
    BaseManager.register('get_result_queue')
    print ('Connect to server %s' % host)
    # 注意，端口port和验证码authkey必须和manager服务器设置的完全一致
    worker = BaseManager(address=(host, port), authkey=authkey)
    # 链接到manager服务器
    worker.connect()
    return worker


def get_queue(worker):
    task = worker.get_task_queue()
    result = worker.get_result_queue()
    # 从task队列取数据，并添加到result队列中
    while 1:
        if task.empty():
            time.sleep(1)
            continue
        n = task.get(timeout=1)
        print ('worker get %d' % n)
        result.put(n)
        time.sleep(1)


if __name__ == "__main__":
    host = '127.0.0.1'
    port = 5000
    authkey = 'abc'
    # 启动worker
    worker = start_worker(host, port, authkey.encode())
    # 获取队列
    get_queue(worker)
```

---

## 参考

较全的进程内容：

|连接|标签|说明|网站|
|:-:|:-:|:-:|:-:|
|[python基础-进程](https://blog.csdn.net/qq_41922768/category_7767239.html)|`multiprocessing`|这个专栏系列内容很多很全、讲原理、解释参数、实例多|CSDN|
|[多进程](https://www.liaoxuefeng.com/wiki/1016959663602400/1017628290184064)|`Process` `Queue` `Pool`|讲原理、不解释参数、一般|廖雪峰的官方网站|
|[Python process (进程)](https://www.cnblogs.com/dbf-/p/11127000.html)|`Process` `Pipe` `Queue` `Manager` `Lock` `Pool`|不解释参数、有一定原理内容|博客园|
|[《Python》进程之间的通信（IPC）、进程之间的数据共享、进程池](https://www.cnblogs.com/yzh2857/p/9683398.html)|`Pipe` `Queue` `JoinableQueue` `Manager` `Process` `Lock` `Pool`|解释参数、有一定的原理、有上述未涉及内容|博客园|
|[一篇文章搞定Python多进程(全)](https://zhuanlan.zhihu.com/p/64702600)|`Process` `Pipe` `Queue` `Manager` `Pool`|解释参数、有一定的原理、有上述未涉及内容|知乎|

线程池：

|连接|标签|说明|网站|
|:-:|:-:|:-:|:-:|
|[python进阶08并发之四map, apply, map_async, apply_async差异](https://hexo.yuanjh.cn/hexo/e279ffad/)|`Pool`|并发map/apply的差异|个人博客|
|[Python multiprocessing.Pool: Difference between map, apply, map_async, apply_async](http://blog.shenwei.me/python-multiprocessing-pool-difference-between-map-apply-map_async-apply_async/)|`Pool`|并发map/apply的差异|个人博客？|

`subprocess`：

|连接|标签|说明|网站|
|:-:|:-:|:-:|:-:|
|[Python进阶(十)多进程multiprocessing和subprocess模块](https://blog.csdn.net/weixin_39059031/article/details/108440342)|`multiprocessing` `subprocess`|原理讲明白、参数解释清楚、例子多（插一句，里面的python系列内容也写的挺好的）|CSDN|

分布式进程：（大概看一下就行了，毕竟大家都是互相抄的）

|连接|标签|说明|网站|
|:-:|:-:|:-:|:-:|
|[multiprocessing --- 基于进程的并行](https://docs.python.org/zh-cn/3.8/library/multiprocessing.html?highlight=freeze_support#examples)|`multiprocessing`|python官网的例子|python官网|
|[BaseManager](https://www.cnblogs.com/liunnis/tag/BaseManager/)|`BaseManager`|这个系列有.东西|博客园|
|[python分布式进程](https://www.cnblogs.com/guguobao/p/9400299.html)|`managers`|。。。。。。|博客园|
|[python 分布式进程实现](https://www.cnblogs.com/chmyee/p/10081531.html)|`managers`|。。。。。。|博客园|
|[python 分布式进程](https://www.ktanx.com/blog/p/870)|`managers`|。。。。。。|ktanx|
|[在Python程序中实现分布式进程的教程](https://blog.csdn.net/chengxun03/article/details/105802073)|`managers`|。。。。。。|CSDN|
|[python分布式进程（windows下）](https://blog.csdn.net/lilong117194/article/details/76051843)|`managers`|。。。。。。|CSDN|
|[Python 分布式进程](http://t.zoukankan.com/bin-l-p-8615201.html)|`managers`|。。。。。。|个人博客|

## 待完成

- [ ] socket实例
- [x] 通信模式
- [ ] [进程同步](https://blog.csdn.net/qq_41922768/article/details/83995322)
- [x] [数据共享Manager](https://blog.csdn.net/qq_41922768/article/details/84024587)
- [x] [共享内存Value,Array](https://blog.csdn.net/ctwy291314/article/details/89358144)
- [ ] 子进程`subprocess`
- [x] 进程池
- [ ] IO序列化
- [x] 分布式进程
