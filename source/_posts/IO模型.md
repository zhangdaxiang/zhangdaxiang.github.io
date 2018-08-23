---
title: IO模型
date: 2018-06-06 10:09:22
tags: [python,IO]
categories: python
---

对于一个network IO (这里我们以read举例)，它会涉及到两个系统对象，一个是调用这个IO的process (or thread)，另一个就是系统内核(kernel)。当一个read操作发生时，它会经历两个阶段：
*	等待数据准备 (Waiting for the data to be ready)
  *将数据从内核拷贝到进程中 (Copying the data from the kernel to the process)

以下几种IO Model的区别就是在两个阶段上各有不同的情况。

### Blocking IO(阻塞IO)
在linux中，默认情况下所有的socket都是blocking，一个典型的读操作流程大概是这样：
<img src='/img/IO模型/Blocking IO.jpg' alt = 'Blocking_IO'>
当用户进程调用了recvfrom这个系统调用，kernel就开始了IO的第一个阶段：准备数据。对于network io来说，很多时候数据在一开始还没有到达（比如，还没有收到一个完整的UDP包），这个时候kernel就要等待足够的数据到来。而在用户进程这边，整个进程会被阻塞。当kernel一直等到数据准备好了，它就会将数据从kernel中拷贝到用户内存，然后kernel返回结果，用户进程才解除block的状态，重新运行起来。
所以，blocking IO的特点就是在IO执行的两个阶段都被block了。
### non-Blocking IO(非阻塞IO)
linux下，可以通过设置socket使其变为non-blocking`setblocking(False)`。当对一个non-blocking socket执行读操作时，流程是这个样子：
<img src='/img/IO模型/non-Blocking IO.jpg' alt = 'non-Blocking_IO'>
从图中可以看出，当用户进程发出read操作时，如果kernel中的数据还没有准备好，那么它并不会block用户进程，而是立刻返回一个error。从用户进程角度讲 ，它发起一个read操作后，并不需要等待，而是马上就得到了一个结果。用户进程判断结果是一个error时，它就知道数据还没有准备好，于是它可以再次发送read操作。一旦kernel中的数据准备好了，并且又再次收到了用户进程的system call，那么它马上就将数据拷贝到了用户内存，然后返回。所以，用户进程其实是需要不断的主动询问kernel数据好了没有。需要注意，拷贝数据整个过程，进程仍然是属于阻塞的状态。
即”非阻塞将大的整片时间的阻塞分成N多的小的阻塞, 所以进程不断地有机会 ‘被’ CPU光顾”。
### IO multiplexing(IO多路复用)
IO多路复用即为`select`,`poll`,`epoll`。好处就在于单个process就可以同时处理多个网络连接的IO。基本原理就是`select`,`poll`,`epoll`会不断的轮询所负责的所有socket，当某个socket有数据到达了，就通知用户进程。它的流程如图：
<img src='/img/IO模型/IO multiplexing.jpg' alt = 'IO_multiplexing'>
当用户进程调用了`select`，那么整个进程会被block，而同时，kernel会“监视”所有`select`负责的socket，当任何一个socket中的数据准备好了，`select`就会返回。这个时候用户进程再调用read操作，将数据从kernel拷贝到用户进程。
这个图和blocking IO的图其实并没有太大的不同，事实上，还更差一些。因为这里需要使用两个system call (select 和 recvfrom)，而blocking IO只调用了一个system call (recvfrom)。但是，用`select`的优势在于它可以同时处理多个connection。（多说一句。所以，如果处理的连接数不是很高的话，使用select/epoll的web server不一定比使用multi-threading + blocking IO的web server性能更好，可能延迟还更大。`select`,`poll`,`epoll`的优势并不是对于单个连接能处理得更快，而是在于能处理更多的连接。）
在IO multiplexing Model中，实际中，对于每一个socket，一般都设置成为non-blocking，但是，如上图所示，整个用户的process其实是一直被block的。只不过process是被`select`这个函数block，而不是被socket IO给block。
```python
#***********************server.py

import socket
import select
sk=socket.socket()
sk.bind(("127.0.0.1",8800))
sk.listen(5)
sk.setblocking(False)
inputs=[sk,]

while True:
    r,w,e=select.select(inputs,[],[],5)
    print(len(r))

    for obj in r:
        if obj==sk:
            conn,add=obj.accept()
            print("conn:",conn)
            inputs.append(conn)
        else:

            data_byte=obj.recv(1024)
            print(str(data_byte,'utf8'))
            if not data_byte:
                inputs.remove(obj)
                continue
            inp=input('回答%s: >>>'%inputs.index(obj))
            obj.sendall(bytes(inp,'utf8'))

    print('>>',r)


#***********************client.py

import socket
sk=socket.socket()
sk.connect(('127.0.0.1',8802))

while True:
    inp=input(">>>>")   # how much one night?
    sk.sendall(bytes(inp,"utf8"))
    data=sk.recv(1024)
    print(str(data,'utf8'))
```
### Asynchronous IO(异步IO)
linux下的asynchronous IO其实用得很少。先看一下它的流程：
<img src='/img/IO模型/Asynchronous IO.jpg' alt = 'Asynchronous _IO'>
用户进程发起read操作之后，立刻就可以开始去做其它的事。而另一方面，从kernel的角度，当它受到一个asynchronous read之后，首先它会立刻返回，所以不会对用户进程产生任何block。然后，kernel会等待数据准备完成，然后将数据拷贝到用户内存，当这一切都完成之后，kernel会给用户进程发送一个signal，告诉它read操作完成了。
### IO模型比较
<img src='/img/IO模型/compare IO models.jpg' alt = 'compare IO models'>