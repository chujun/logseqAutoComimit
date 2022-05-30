- 定义:也称事件驱动IO，在单个线程里同时监控多个套接字，通过 select/poll/epoll查看所负责的所有 socket，进程阻塞,当任意一个socket 有数据准备好，就通知用户进程。
  俗称:多路是指网络连接，复用指的是同一个线程
  
  
  
  系统调用:两次一次select/poll/epoll，一次recvform
  
  select函数工作过程:多个进程的 IO 可以注册到同一个管道上，关键是select函数，多个进程的 IO 可以注册到同一个select上，当用户进程调用该select，select会监听所有注册好的 IO，如果所有被监听的 IO 需要的数据都没有准备好时，调用进程会阻塞，等待有套接字变为可读。当任意一个 IO 需要的数据准备好后，即当有套接字可读以后，select调用就会返回，然后进程再通过recvfrom来把对应的数据拷贝到用户进程缓冲区。
  
  图片描述select实现方式，poll/epoll工作方式大致相同
  ![select实现方式IO多路复用.png](../assets/image_1653824421764_0.png)
  
  第一阶段和第二阶段都阻塞，是同步阻塞(三种实现方式都是)。
- 准备知识
  ((629317ef-ddcb-45f7-8e4b-44af198efc0a))
- 五种常见的IO模型
  ((62931088-07cf-4392-835b-8ab943e3351f))
- 与同步阻塞IO：
  ((629310c1-0a9c-42b2-a918-74796479c2df)) 
  优势:可以同时等待多个描述符就绪。
  劣势:需要两次系统调用，同步阻塞IO只需要一次系统调用。
  所以在并发量不高的情况下,同步阻塞IO+线程池的方式相比IO多路复用机制可能效率更高(具体需要测试)
- IO多路复用机制三种实现select/poll/epoll函数
  select/poll/epoll三种函数实现
  select:有I/O事件发生了，却并不知道具体是哪几个文件描述符
  select具有O(n)的无差别轮询复杂度，同时处理的流越多，无差别轮询时间就越长
  poll:可以同时轮训多个IO数据准备是否就绪，
  epollo:可以同时等待多个IO数据准备就绪,进程阻塞，只要有
  FD 是文件描述符的缩写
	- 1. select 
	  select:有I/O事件发生了，通知用户进程，却并不知道具体是哪几个流(文件描述符)，只能无差别轮询所有流，找出有读写操作的流
	  select具有O(n)的无差别轮询复杂度，同时处理的流越多，无差别轮询时间就越长
	  select函数定义
	  ```
	  // API
	  // 返回值就绪描述符的数目,若超时则返回0，若出错则返回-1
	  int select(
	      int max_fd, 
	      fd_set *readset, 
	      fd_set *writeset, 
	      fd_set *exceptset, 
	      struct timeval *timeout
	  )                              
	  ```
	  select调用过程
	  ![image.png](../assets/image_1653882483619_0.png) 
	  select调用过程
	  （1）使用copy_from_user从用户空间拷贝fd_set到内核空间
	  （2）注册回调函数__pollwait
	  （3）遍历所有fd，调用其对应的poll方法（对于socket，这个poll方法是sock_poll，sock_poll根据情况会调用到tcp_poll,udp_poll或者datagram_poll）
	  （4）以tcp_poll为例，其核心实现就是__pollwait，也就是上面注册的回调函数。
	  （5）__pollwait的主要工作就是把current（当前进程）挂到设备的等待队列中，不同的设备有不同的等待队列，对于tcp_poll来说，其等待队列是sk->sk_sleep（注意把进程挂到等待队列中并不代表进程已经睡眠了）。在设备收到一条消息（网络设备）或填写完文件数据（磁盘设备）后，会唤醒设备等待队列上睡眠的进程，这时current便被唤醒了。
	  （6）poll方法返回时会返回一个描述读写操作是否就绪的mask掩码，根据这个mask掩码给fd_set赋值。
	  （7）如果遍历完所有的fd，还没有返回一个可读写的mask掩码，则会调用schedule_timeout是调用select的进程（也就是current）进入睡眠。当设备驱动发生自身资源可读写后，会唤醒其等待队列上睡眠的进程。如果超过一定的超时时间（schedule_timeout指定），还是没人唤醒，则调用select的进程会重新被唤醒获得CPU，进而重新遍历fd，判断有没有就绪的fd。
	  （8）把fd_set从内核空间拷贝到用户空间
	  
	  详情
	  ```cpp
	  #include <sys/select.h>
	  #include <sys/time.h>
	  
	  #define FD_SETSIZE 1024
	  #define NFDBITS (8 * sizeof(unsigned long))
	  #define __FDSET_LONGS (FD_SETSIZE/NFDBITS)
	  
	  // 数据结构 (bitmap)
	  typedef struct {
	      unsigned long fds_bits[__FDSET_LONGS];
	  } fd_set;
	  
	  // API
	  // 返回值就绪描述符的数目,若超时则返回0，若出错则返回-1
	  int select(
	      int max_fd, 
	      fd_set *readset, 
	      fd_set *writeset, 
	      fd_set *exceptset, 
	      struct timeval *timeout
	  )                              
	  
	  FD_ZERO(int fd, fd_set* fds)   // 清空集合
	  FD_SET(int fd, fd_set* fds)    // 将给定的描述符加入集合
	  FD_ISSET(int fd, fd_set* fds)  // 判断指定描述符是否在集合中 
	  FD_CLR(int fd, fd_set* fds)    // 将给定的描述符从文件中删除  
	  
	  ```
	  select缺点:
	  FD ((629436f6-7eb2-4324-9f58-24e6112dfc12))
	  1. 单个进程所打开的FD是有限制的，通过 FD_SETSIZE 设置，默认1024 ;
	  2. 每次调用 select，都需要把 fd 集合从用户态拷贝到内核态，这个开销在 fd 很多时会很大；
	  3. 需要遍历整个fd集合，不管那个fd socker,对 socket 扫描时是线性扫描，采用轮询的方法，效率较低（高并发）
	-
	- 2. poll
	  待确定？如果用的是poll方式，是不是就是同步非阻塞的。
	- 3. epoll
	  优缺点
- IO多路复用机制三种实现函数的比较
- IO多路复用机制的应用
  常用于处理高并发的网络请求,所有请求里同时发生读写操作的比例一般不会很高，绝大多数请求读写都是阻塞状态
  1. redis
  2. nginx
- 资料
  [彻底理解 IO 多路复用实现机制](https://juejin.cn/post/6882984260672847879)