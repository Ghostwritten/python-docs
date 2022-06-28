#  python 进程、线程、协程


并发编程（不是并行）目前有四种方式：多进程、多线程、协程和异步。

 1. 多进程编程在python中有类似C的os.fork,更高层封装的有multiprocessing标准库
 2. 多线程编程python中有Thread和threading
 3. 异步编程在linux下主+要有三种实现select，poll，epoll
 4. 协程在python中通常会说到yield，关于协程的库主要有greenlet, stackless, gevent, eventlet等实现。

从多进程、多线程、协程和分布 式进程等四个方面，熟悉爬虫开发中灵活运用进程和线程。
## 1. 多进程

Python实现多进程的方式主要有两种，一种方法是使用os模块中的fork方法， 另一种方法是使用multiprocessing模块。这两种方法的区别在于前者仅适用于 Unix/Linux操作系统，对Windows不支持，后者则是跨平台的实现方式。由于现在 很多爬虫程序都是运行在Unix/Linux操作系统上，所以本节对两种方式都进行讲 解。
### 1.2 os模块fork方式实现多进程
Python的os模块封装了常见的系统调用，其中就有`fork方法`。fork方法来自于 Unix/Linux操作系统中提供的一个fork系统调用，这个方法非常特殊。普**通的方法 都是调用一次，返回一次，而fork方法是调用一次，返回两次，原因在于操作系统 将当前进程（父进程）复制出一份进程（子进程），这两个进程几乎完全相同，于 是fork方法分别在父进程和子进程中返回。子进程中永远返回0，父进程中返回的是 子进程的ID。**下面举个例子，对Python使用fork方法创建进程进行讲解。其中os模 块中的getpid方法用于获取当前进程的ID，getppid方法用于获取父进程的ID。

```python
import os     

if __name__ == '__main__':        
   print 'current Process (%s) start ...'%(os.getpid())        
   pid = os.fork()        
   if pid < 0:                
      print 'error in fork'        
   elif pid == 0:                
        print 'I am child process(%s) and my parent process is (%s)',(os.getpid(),                
        os.getppid())        
   else:                
        print 'I(%s) created a chlid process (%s).',(os.getpid(),pid)
```

```python
$  python os3.py 
current Process (3113) start ...
I(%s) created a chlid process (%s). (3113, 3114)
I am child process(%s) and my parent process is (%s) (3114, 3113)
```

### 1.2 multiprocessing模块创建多进程
multiprocessing模块提供了一个Process类来描述一个进程对象。创建子进 程时，只需要传入一个执行函数和函数的参数，即可完成一个Process实例的创 建，用start（）方法启动进程，用join（）方法实现进程间的同步。下面通过一 个例子来演示创建多进程的流程，代码如下：

```python
#!/usr/bin/python
#coding:utf-8
import os     
from multiprocessing import Process     
# 子进程要执行的代码     
def run_proc(name):        
    print 'Child process %s (%s) Running...' % (name, os.getpid())     

if __name__ == '__main__':
   print 'Parent process %s.' % os.getpid()        
   for i in range(5):                
       p = Process(target=run_proc, args=(str(i),))                
       print 'Process will start.'                
       p.start()        

p.join()        
print 'Process end.'
```

```python
$ python os4.py 
Parent process 3810.
Process will start.
Process will start.
Child process 0 (3811) Running...
Process will start.
Process will start.
Child process 1 (3812) Running...
Process will start.
Child process 2 (3813) Running...
Child process 3 (3814) Running...
Child process 4 (3815) Running...
Process end.
```
以上介绍了创建进程的两种方法，但是要启动大量的子进程，使用进程池批量 创建子进程的方式更加常见，因为当被操作对象数目不大时，可以直接利用 multiprocessing中的Process动态生成多个进程，如果是上百个、上千个目标， 手动去限制进程数量却又太过繁琐，这时候进程池Pool发挥作用的时候就到了。

### 1.3 multiprocessing模块提供了一个Pool类来代表进程池对象
Pool可以提供指定数量的进程供用户调用，默认大小是CPU的核数。当有新的 请求提交到Pool中时，如果池还没有满，那么就会创建一个新的进程用来执行该请 求；但如果池中的进程数已经达到规定最大值，那么该请求就会等待，直到池中有
进程结束，才会创建新的进程来处理它。下面通过一个例子来演示进程池的工作流 程，代码如下：

```python
#coding:utf-8

from multiprocessing import Pool     
import os, time, random          

def run_task(name):        
    print 'Task %s (pid = %s) is running...' % (name, os.getpid())        
    time.sleep(random.random() * 3)        
    print 'Task %s end.' % name          

if __name__=='__main__':        
   print 'Current process %s.' % os.getpid()        
   p = Pool(processes=3)        
   for i in range(5):                
       p.apply_async(run_task, args=(i,))        
       print 'Waiting for all subprocesses done...'        
   p.close()        

   p.join()        
   print 'All subprocesses done.'

```

```python
$ python os5.py 
Current process 4238.
Waiting for all subprocesses done...
Waiting for all subprocesses done...
Waiting for all subprocesses done...
Waiting for all subprocesses done...
Waiting for all subprocesses done...
Task 0 (pid = 4239) is running...
Task 1 (pid = 4241) is running...
Task 2 (pid = 4240) is running...
Task 2 end.
Task 3 (pid = 4240) is running...
Task 3 end.
Task 4 (pid = 4240) is running...
Task 1 end.
Task 0 end.
Task 4 end.
All subprocesses done.
```
上述程序先创建了容量为3的进程池，依次向进程池中添加了5个任务。从运行 结果中可以看到虽然添加了5个任务，但是一开始只运行了3个，而且每次最多运行3 个进程。当一个任务结束了，新的任务依次添加进来，任务执行使用的进程依然是 原来的进程，这一点通过进程的pid就可以看出来。
 **注意: 　Pool对象调用join（）方法会等待所有子进程执行完毕，调用 join（）之前必须先调用close（），调用close（）之后就不能继续添加新的 Process了。**


### 1.4 进程间通信
假如创建了大量的进程，那进程间通信是必不可少的。Python提供了多种进程 间通信的方式，例如Queue、Pipe、Value+Array等。本节主要讲解Queue和Pipe 这两种方式。Queue和Pipe的区别在于Pipe常用来在两个进程间通信，Queue用来 在多个进程间实现通信。
#### 1.4.1 Queue
首先讲解一下Queue通信方式。Queue是多进程安全的队列，可以使用Queue实 现多进程之间的数据传递。有两个方法：Put和Get可以进行Queue操作：

 - ·`Put`方法用以插入数据到队列中，它还有两个可选参数：`blocked`和`timeout`。
   如果blocked为True（默认值），并且timeout为正值，该方法会阻塞timeout指定
   的时间，直到该队列有剩余的空间。如果超时，会抛出Queue.Full异常。如果
   blocked为False，但该Queue已满，会立即抛出Queue.Full异常。
 - ·`Get`方法可以从队列读取并且删除一个元素。同样，Get方法有两个可选参数：
   `blocked`和`timeout`。如果blocked为True（默认值），并且timeout为正值，那么
   在等待时间内没有取到任何元素，会抛出Queue.Empty异常。如果blocked为
   False，分两种情况：如果Queue有一个值可用，则立即返回该值；否则，如果队列 为空，则立即抛出Queue.Empty异常。

下面通过一个例子进行说明：在父进程中创建三个子进程，两个子进程往Queue 中写入数据，一个子进程从Queue中读取数据。程序示例如下：
 

```python
 #coding: utf-8

from multiprocessing import Process, Queue     
import os, time, random          
# 写数据进程执行的代码:     

def proc_write(q,urls):        
    print('Process(%s) is writing...' % os.getpid())        
    for url in urls:                
        q.put(url)                
        print('Put %s to queue...' % url)                
        time.sleep(random.random())          
# 读数据进程执行的代码:     
def proc_read(q):        
    print('Process(%s) is reading...' % os.getpid())        
    while True:                     
          url = q.get(True)                
          print('Get %s from queue.' % url)          
    
if __name__=='__main__':        
# 父进程创建Queue，并传给各个子进程：        
   q = Queue()        
   proc_writer1 = Process(target=proc_write, args=(q,['url_1', 'url_2', 'url_3']))        
   proc_writer2 = Process(target=proc_write, args=(q,['url_4','url_5','url_6']))        
   proc_reader = Process(target=proc_read, args=(q,))        
# 启动子进程proc_writer，写入:        
   proc_writer1.start()        
   proc_writer2.start()        
# 启动子进程proc_reader，读取:        
   proc_reader.start()       
# 等待proc_writer结束:
   proc_writer1.join()        
   proc_writer2.join()        
# proc_reader进程里是死循环，无法等待其结束，只能强行终止:        
   proc_reader.terminate()
```

```python
$ python os6.py 
Process(4996) is writing...
Put url_1 to queue...
Process(4997) is writing...
Put url_4 to queue...
Process(4999) is reading...
Get url_1 from queue.
Get url_4 from queue.
Put url_5 to queue...
Get url_5 from queue.
Put url_6 to queue...
Get url_6 from queue.
Put url_2 to queue...
Get url_2 from queue.
Put url_3 to queue...
Get url_3 from queue.
```
#### 1.4.2 Pipe
最后介绍一下Pipe的通信机制，Pipe常用来在两个进程间进行通信，两个进程 分别位于管道的两端。
Pipe方法返回（conn1，conn2）代表一个管道的两个端。Pipe方法有duplex 参数，如果duplex参数为True（默认值），那么这个管道是全双工模式，也就是说 conn1和conn2均可收发。若duplex为False，conn1只负责接收消息，conn2只负 责发送消息。send和recv方法分别是发送和接收消息的方法。例如，在全双工模式 下，可以调用conn1.send发送消息，conn1.recv接收消息。如果没有消息可接 收，recv方法会一直阻塞。如果管道已经被关闭，那么recv方法会抛出EOFError。
下面通过一个例子进行说明：创建两个进程，一个子进程通过Pipe发送数据， 一个子进程通过Pipe接收数据。程序示例如下：
 

```python
import multiprocessing     
import random     
import time,os          

def proc_send(pipe,urls):        
    for url in urls:                
        print "Process(%s) send: %s" %(os.getpid(),url)                
        pipe.send(url)                
        time.sleep(random.random())          

def proc_recv(pipe):        
    while True:                
        print "Process(%s) rev:%s" %(os.getpid(),pipe.recv())                
        time.sleep(random.random())          

if __name__ == "__main__":        

  pipe = multiprocessing.Pipe()        
  p1 = multiprocessing.Process(target=proc_send, args=(pipe[0],['url_'+str(i) for i in range(10) ]))        
  p2 = multiprocessing.Process(target=proc_recv, args=(pipe[1],))        
  p1.start()        
  p2.start()        
  p1.join()        
  p2.join()
```

```powershell
$ python os7.py 
Process(5454) send: url_0
Process(5455) rev:url_0
Process(5454) send: url_1
Process(5454) send: url_2
Process(5455) rev:url_1
Process(5454) send: url_3
Process(5454) send: url_4
Process(5455) rev:url_2
Process(5455) rev:url_3
Process(5454) send: url_5
Process(5454) send: url_6
Process(5455) rev:url_4
Process(5455) rev:url_5
Process(5455) rev:url_6
Process(5454) send: url_7
Process(5454) send: url_8
Process(5454) send: url_9
Process(5455) rev:url_7
Process(5455) rev:url_8
Process(5455) rev:url_9
```
**注意 　以上多进程程序运行结果的打印顺序在不同的系统和硬件条件下 略有不同。**

## 2. 多线程
多线程类似于同时执行多个不同程序，多线程运行有如下优点：

 - ·可以把运行时间长的任务放到后台去处理。
 - ·用户界面可以更加吸引人，比如用户点击了一个按钮去触发某些事件的处理， 可以弹出一个进度条来显示处理的进度。
 - ·程序的运行速度可能加快。
 - ·在一些需要等待的任务实现上，如用户输入、文件读写和网络收发数据等，线
   程就比较有用了。在这种情况下我们可以释放一些珍贵的资源，如内存占用等。

Python的标准库提供了两个模块：thread和threading，thread是低级模 块，threading是高级模块，对thread进行了封装。绝大多数情况下，我们只需要 使用threading这个高级模块。
### 2.1 用threading模块创建多线程
`threading`模块一般通过两种方式创建多线程：第一种方式是把一个函数传入 并创建`Thread实例`，然后调用start方法开始执行；第二种方式是直接从 threading.Thread继承并创建线程类，然后重写__init__方法和run方法。
首先介绍第一种方法，通过一个简单例子演示创建多线程的流程，程序如下：

```python
#coding:utf-8
import random     
import time, threading     
# 新线程执行的代码:     
def thread_run(urls):        
    print 'Current %s is running...' % threading.current_thread().name        
    for url in urls:                
        print '%s ---->>> %s' % (threading.current_thread().name,url)                
        time.sleep(random.random())        
    print '%s ended.' % threading.current_thread().name          
print '%s is running...' % threading.current_thread().name     
t1 = threading.Thread(target=thread_run, name='Thread_1',args=(['url_1','url_2','url_3'],))     
t2 = threading.Thread(target=thread_run, name='Thread_2',args=(['url_4','url_5','url_6'],))     
t1.start()     
t2.start()     
t1.join()     
t2.join()     
print '%s ended.' % threading.current_thread().name
```

```python
$ python thr1.py
MainThread is running...
Current Thread_1 is running...
Thread_1 ---->>> url_1
Current Thread_2 is running...
Thread_2 ---->>> url_4
Thread_2 ---->>> url_5
Thread_1 ---->>> url_2
Thread_1 ---->>> url_3
Thread_2 ---->>> url_6
Thread_2 ended.
Thread_1 ended.
MainThread ended.
```
第二种方式从`threading.Thread`继承创建线程类，下面将方法一的程序进行重 写，程序如下：
 
 

```python
#coding:utf-8

import random     
import threading     
import time     

class myThread(threading.Thread):        

      def __init__(self,name,urls):                
         threading.Thread.__init__(self,name=name)                
         self.urls = urls             
      def run(self):                
          print 'Current %s is running...' % threading.current_thread().name                
          for url in self.urls:                        
              print '%s ---->>> %s' % (threading.current_thread().name,url)                        
              time.sleep(random.random())                
          print '%s ended.' % threading.current_thread().name     

print '%s is running...' % threading.current_thread().name     
t1 = myThread(name='Thread_1',urls=['url_1','url_2','url_3'])     
t2 = myThread(name='Thread_2',urls=['url_4','url_5','url_6'])     
t1.start()     
t2.start()     
t1.join()     
t2.join()
```

### 2.2 线程同步
如果多个线程共同对某个数据修改，则可能出现不可预料的结果，为了保证数 据的正确性，需要对多个线程进行同步。使用Thread对象的Lock和RLock可以实现 简单的线程同步，这两个对象都有acquire方法和release方法，对于那些每次只允 许一个线程操作的数据，可以将其操作放到acquire和release方法之间。
对于Lock对象而言，如果一个线程连续两次进行acquire操作，那么由于第一 次acquire之后没有release，第二次acquire将挂起线程。这会导致Lock对象永远 不会release，使得线程死锁。RLock对象允许一个线程多次对其进行acquire操 作，因为在其内部通过一个counter变量维护着线程acquire的次数。而且每一次的 acquire操作必须有一个release操作与之对应，在所有的release操作完成之后， 别的线程才能申请该RLock对象。下面通过一个简单的例子演示线程同步的过程：

```python
import threading

mylock = threading.RLock()     
num=0     
class myThread(threading.Thread):        
      def __init__(self, name):                
          threading.Thread.__init__(self,name=name)             
      def run(self):                
          global num                
          while True:                        
                mylock.acquire()                        
                print '%s locked, Number: %d'%(threading.current_thread().name, num)                        
                if num>=4:                                
                   mylock.release()                                
                   print '%s released, Number: %d'%(threading.current_thread().name, num)
                   break                        
                num+=1                        
                print '%s released, Number: %d'%(threading.current_thread().name, num)                        
                mylock.release()               

if __name__== '__main__':        
   thread1 = myThread('Thread_1')        
   thread2 = myThread('Thread_2')        
   thread1.start()        
   thread2.start()
```

```python
$ python th3.py
Thread_1 locked, Number: 0
Thread_1 released, Number: 1
Thread_1 locked, Number: 1
Thread_1 released, Number: 2
Thread_1 locked, Number: 2
Thread_1 released, Number: 3
Thread_1 locked, Number: 3
Thread_1 released, Number: 4
Thread_1 locked, Number: 4
Thread_1 released, Number: 4
Thread_2 locked, Number: 4
Thread_2 released, Number: 4
```
### 2.3 全局解释器锁（GIL）
在Python的原始解释器CPython中存在着GIL（Global Interpreter Lock， 全局解释器锁），因此在解释执行Python代码时，会产生互斥锁来限制线程对共享 资源的访问，直到解释器遇到I/O操作或者操作次数达到一定数目时才会释放GIL。 由于全局解释器锁的存在，在进行多线程操作的时候，不能调用多个CPU内核，只能 利用一个内核，所以在进行CPU密集型操作的时候，不推荐使用多线程，更加倾向于 多进程。那么多线程适合什么样的应用场景呢？对于IO密集型操作，多线程可以明 显提高效率，例如Python爬虫的开发，绝大多数时间爬虫是在等待socket返回数 据，网络IO的操作延时比CPU大得多。
## 3. 协程
协程 （coroutine），又称微线程，纤程，是一种用户级的轻量级线程。协程 拥有自己的寄存器上下文和栈。协程调度切换时，将寄存器上下文和栈保存到其他 地方，在切回来的时候，恢复先前保存的寄存器上下文和栈。因此协程能保留上一 次调用时的状态，每次过程重入时，就相当于进入上一次调用的状态。

在并发编程 中，协程与线程类似，每个协程表示一个执行单元，有自己的本地数据，与其他协 程共享全局数据和其他资源。协程需要用户自己来编写调度逻辑，对于CPU来说，协程其实是单线程，所以 CPU不用去考虑怎么调度、切换上下文，这就省去了CPU的切换开销，所以协程在一 定程度上又好于多线程。那么在Python中是如何实现协程的呢？	

Python通过yield提供了对协程的基本支持，但是不完全，而使用第三方 gevent库是更好的选择，gevent提供了比较完善的协程支持。gevent是一个基于 协程的Python网络函数库，使用greenlet在libev事件循环顶部提供了一个有高级 别并发性的API。主要特性有以下几点：

 - ·基于libev的快速事件循环，Linux上是epoll机制。
 - ·基于greenlet的轻量级执行单元。
 - ·API复用了Python标准库里的内容。
 - ·支持SSL的协作式sockets。
 - ·可通过线程池或c-ares实现DNS查询。
 - ·通过monkey patching功能使得第三方模块变成协作式。
 
gevent对协程的支持，本质上是greenlet在实现切换工作。greenlet工作流 程如下：假如进行访问网络的IO操作时，出现阻塞，greenlet就显式切换到另一段 没有被阻塞的代码段执行，直到原先的阻塞状况消失以后，再自动切换回原来的代 码段继续处理。因此，greenlet是一种合理安排的串行方式。

由于IO操作非常耗时，经常使程序处于等待状态，有了gevent为我们自动切换 协程，就保证总有greenlet在运行，而不是等待IO，这就是协程一般比多线程效率 高的原因。由于切换是在IO操作时自动完成，所以gevent需要修改Python自带的一 些标准库，将一些常见的阻塞，如socket、select等地方实现协程跳转，这一过程 在启动时通过monkey patch完成。下面通过一个的例子来演示gevent的使用流 程，代码如下：




参考：

 - [gevent调度流程解析](https://www.cnblogs.com/xybaby/p/6370799.html)
 - [Python process, thread and coroutine](https://pythonmana.com/2020/12/20201217122757825b.html)
 - [Difference between Multi-Processing, Multi-threading and Coroutine](https://sekiro-j.github.io/post/tcp/)
 - [Choosing between Python threads vs coroutines vs processes](https://blog.vijayprasanna13.me/posts/python-threads-coroutines-processes/)
 - [python process thread coroutine](https://copyfuture.com/blogs-details/202204300024361017)
 - [Combining Coroutines with Threads and Processes](https://pymotw.com/3/asyncio/executors.html)
