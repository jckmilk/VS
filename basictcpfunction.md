#基本TCP套接字编程
基本的TCP客户与服务端进程
并发服务器，子进程fork()的方法。


##socket()函数
为了执行网络I/O，一个进程第一要调用socket()函数，指定通信协议类型

    int socket(int family,int type,int protocol);
    成功返回非负描述符，出错为-1。

##connect()函数
客户用connect函数建立tcp服务器的连接

    int connect(int sockfd, const struct sockaddr *servaddr, socklen_t addrlen);
    成功返回0，出错则为-1；

connect函数激发tcp三次握手，仅在连接建立成功或出错才返回。
出错情况如下：
1. TCP没有收到SYN分节响应，返回etimeout错误。无响应等待6s发送一个，仍无响应，等待24s再发送，总共等了75s仍无响应则返回本错误。
2. 若SYN的返回为RST，则表示服务器主机在指定端口上没有进程在等待，这是一种硬错误，客户一收到RST马上返回ECONNREFUSED错误。
产生RST的三个条件：

- 该端口上没有正在监听的服务器；
- TCP想取消一个已有的连接；
- TCP接收到一个根本不存在的连接上的分支。

3.客户发送的SYN在某一个路由上引发“destination UNreachable”ICMP错误，这是一个软错误。客户主机内核保存该消息，并按照第一种情况的时间间隔发送SYN。如果还没收到，则把ICMP错误作为EHOSTUNREACH或ENETUNREACH错误返回给进程。

每次调用connect()函数失败都必须close当前的套接字描述符并重新调用socket。

##bind函数
bind函数把一个本地协议地址赋予一个套接字。

    int bind(int sockfd, const struct sockaddr *myaddr, socklen_t addrlen);
    成功返回0，否则返回-1.
    
如果tcp客户或服务器未曾用bind绑定一个端口，当调用connect或listen时，内核需要选择一个临时端口。TCP服务器需要绑定。

##listen函数
仅有服务器调用：
(1) listen函数把一个未连接套接字转化为一个被动套接字，指示内核应接受指向改套接字的连接请求。调用listen套接字从close的切换到listen
(2) 第二个参数规定了内核应该为相应套接字排队的最大连接数。

    int listen(int sockfd, int blockblog);
    成功返回0，否则返回-1.
内核为任何一个监听套接字维护两个队列:
(1)未完成队列，等待三次握手
(2)已完成队列，完成三次握手
backlog 套接字队列的最大已完成连接数目。
联想到syn攻击。

##accept函数
TCP服务器调用，用于从已完成的连接队列返回一个已完成的连接，如果已完成队列为空，则进程进入睡眠

    int accept
    (int sockfd, struct sockaddr *cliaddr, socklen_t *addrlen);
    成功返回非负描述符，出错则为-1；


监听套接字
socket产生，一个服务器仅仅创建一个监听套接字。
已连接套接字
accept产生
内核为每一个由服务器进程接收到的客户连接一个已连接的套接字（三次握手完成），当连接结束，则已连接套接字被关闭。

本函数最多三个返回值：新套接字描述符，错误指示整数。客户进程的协议地址，该地址的大小。如果不对客户协议地址不感兴趣，则均置为空。

##fork和exec函数
    pid_t fork(void);
    在子进程中为0，父进程中为子进程ID，出错则为-1.
调用一次，返回两次，
调用进程中返回一次，返回值为子进程ID
子进程中返回一次，返回值为0；
子进程只有一个父进程 调用getppid取得父进程的ID。父进程有多个子进程 则必须记录每次调用fork的返回值。

父进程在调用fork之前的所有描述符在fork返回之后由子进程分享。
父进程调用accept之后调用fork。所接受的已连接套接字随后就在父进程与子进程之间共享。子进程接着读写已连接套接字，父进程则关闭。

fork的典型用法：
(1)一个进程创建一个资深的副本，每一个副本都可以在另一个副本指向其他任务的时候处理各自的操作。
(2)一个进程要执行另一个程序。调用fork创建一个自身的副本，其中一个副本地阿偶用exec去替换成新的程序。shell程序用法。

    
exec函数，现在的进程调用exec函数，exec函数把当前进程映像替换为新的程序文件。进程ID并不改变，出错返回调用者，否则控制江北传递给新程序的起点。

##并发服务器
同时服务多个用户
unix编写并发服务器最简单的方法就是fork一个子进程来服务每个用户。
每一个文件或套接字都有一个引用计数，引用计数在文件表项中维护，它是当前打开着的引用该文件或套接字的描述符个数。
清理套接字需要等待引用计数为0.

## close函数

int close（int sockfd);

##getsockname和getpeername函数

getsockname:返回与某个套接字关联的本地协议地址，
getpeername：返回与某个套接字关联的外地协议地址

getsockname 不需要绑定

在没有调用bind的客户端，connect成功返回后，getsockname返回由内核赋予该链接的本地IP地址和本地端口号
以端口0调用bind(告诉内核去选择本地端口号）后，getsockname返回内核赋予的本地端口号
在一个以通配IP调用bind的服务器上，与客户连接一旦建立，accept返回。getsockname返回内核赋予的本地IP地址。套接字描述符参数必须是已连接套接字。









