---
layout: post
title: For python multiprocess
---
# Python的进程间通信
进程间通讯有多种方式，包括信号，管道，消息队列，信号量，共享内存，socket等

 ## 1.共享内存

Python可以通过mmap模块实现进程之间的共享内存

mmap文件对象既像一个字符串也像一个普通文件对象。  
像字符串时因为我们可以改变其中的单个字符，如，obj[index] = 'a'，同时我们也可以改变一小段的字符  
如 obj[2:5]='aaa'。像文件对象是因为在mmap中会有操作标记，我们可以使用seek()方法来改变mmap对象的操作标记  
mmap对象通过mmap()方法来构建，Windows系统和Unix系统下，构建方法是不同的。  
window的构造方法：  
class mmap.mmap(fileno, length[, tagname[, access[, offset]]])  
linux的构造方法：  
class mmap.mmap(fileno, length[, flags[, prot[, access[, offset]]]])  
 
fileno是文件的标号，可以是普通的文件对象的标号，（文件对象通过fileno()方法获得）  
也可以是-1，-1代表这个内存是无名的。length参数定义该内存的长度，如果为0  
就去fileno对应的文件的最大长度，access是改mmap对象的权限，可以是ACCESS_READ,ACCESS_WRITE   
ACCESS_COPY三个值  
linux中  
flags的值可以是MAP_PRIVATE 和MAP_SHARED ，默认是MAP_SHARED ，改参数用来标识改内存是私有的还是共享的  
prot的值可以是PROT_READ 和PROT_WRITE，也是用来定义该mmap的权限的  
flags+ prot 和access这种权限定义方式只能取一种，否则会报错  
window中  
tagname是这个mmap的标记，用来唯一标记这一块共享内存，作用像fileno  
 
由于有tagname这个参数，所以在windows中，可以通过领fileno为-1，然后自定义一个tagname  
例如‘mysharename’,来令多个进程都能共享同一块内存  
但是在linux中，这种方法就不可以用了，只能通过打开一个文件，获取fileno来实现。  
所以在window中，共享内存更加灵活  
mmap对象常用的方法：  
mmap.close() 关闭对象  
mmap.find(string[, start[, end]])   在共享内存中查找内容，返回匹配内容最小的操作标记  
mmap.flush([offset, size])  把内存的数据保存到硬盘中  
mmap.move(dest, src, count) 移动操作标记  
mmap.read(num)  从操作标记开始读取num个长度的字符  
mmap.read_byte()  读取二进制数据  
mmap.readline()   读取一行数据  
mmap.resize(newsize)  修改mmap的长度  
mmap.rfind(string[, start[, end]])   在共享内存中查找内容，返回匹配内容最大的操作标记  
mmap.seek(pos[, whence])  移动操作标记  
mmap.size()  返回mmap对象的长度  
mmap.tell()  返回当前操作标记的位置  
mmap.write(string)  写入内容  
mmap.write_byte(byte)  写入二进制内容  
 

linux构造例子：
 
```py
share_file='/tmp/mm.txt'
f = open(share_file, 'wb')
f.write('a' * share_size)
f.close()
f = open(share_file, 'r+b')
mm = mmap.mmap(f.fileno(), 0)
f.close()
```
因为mmap对象的长度不能大于文件的长度，不然会报错：ValueError: mmap offset is greater than file siz

所以需要以wb的形式，先打开共享的文件，然后写入需要共享内存的长度的内容，关闭文件后以r+b方式打开文件，然后构造mmap对象。

当然，下次就可以直接用r+b的方式打开文件，然后构造对象了

 

参考：https://docs.python.org/2/library/mmap.html#module-mmap

 

 

 ## 2.信号

信号（signal）--     进程之间通讯的方式。一个进程一旦接收到信号就会打断原来的程序执行流程来处理信号。

几个常用信号:

SIGINT     终止进程  中断进程  (control+c)

SIGTERM   终止进程     软件终止信号

SIGKILL   终止进程     杀死进程

SIGALRM 闹钟信号

相对于共享内存，信号更加偏向于系统层面的，linux系统也是通过信号来管理进程，而且系统也规定了某些进程接到某些信号后的行为。

当然我们可以通过绑定信号处理函数来修改进程收到信号以后的行为

```py
#encoding=utf-8
import os
import signal
from time import sleep
def my_term(a,b):
    print "收到sigterm信号"
signal.signal(signal.SIGTERM,my_term)
def my_usr1(a,b):
    print "收到SIGUSR1信号"
signal.signal(signal.SIGUSR1,my_usr1)
while 1:
    print "我是进程id是",os.getpid()
    sleep(1)

#可以通过os.kill(pid，信号)来主动发送信号

 ```

## 3.通过Queue

```py
import threading
from time import sleep

def f(q,t):
    q.put(t)

from multiprocessing import Process,Queue
if __name__ == '__main__':
    q=Queue()
    p = Process(target=f, args=(q,'ljx.sa'))
    p.start()
    p.join()
    p1 = Process(target=f, args=(q,'ljx.elex'))
    p1.start()
    p1.join()
    print q.qsize()
```
