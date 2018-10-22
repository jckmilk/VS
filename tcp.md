#TCP 函数
##socket() 函数 创建tcp套接字
    `sockfd=socket(AF_INET, SOCK_STREAM, 0)`
返回套接字描述符


## 套接口地址结构
    
    struct sockaddr_in
     
    {
     
    short sin_family;/*Address family一般来说AF_INET（地址族）PF_INET（协议族）*/
     
    unsigned short sin_port;/*Port number(必须要采用网络数据格式,普通数字可以用htons()函数转换成网络数据格式的数字)htons 的用处就是把实际主机内存中的整数存放方式调整成网络字节顺序。*/
     
    struct in_addr sin_addr;/*IP address in network byte order（Internet address）*/
     
    unsigned char sin_zero[8];/*Same size as struct sockaddr没有实际意义,只是为了　跟SOCKADDR结构在内存中对齐*/
     
    };

##inet_pton()函数
    inet_pton：将“点分十进制” －> “二进制整数”
    int inet_pton(int af, const char *src, void *dst);
    这个函数转换字符串到网络地址，第一个参数af是地址簇，第二个参数*src是来源地址，第三个参数* dst接收转换后的数据。

##connect() 函数
    函数原型: int connect(SOCKET s, const struct sockaddr * name, int namelen);
    参数:
    s：标识一个未连接socket
    name：指向要连接套接字的sockaddr结构体的指针
    namelen：sockaddr结构体的字节长度

##read()函数
    ssize_t read(int fd,void * buf ,size_t count);
    函数说明
    read()会把参数fd 所指的文件传送count个字节到buf指针所指的内存中。若参数count为0，则read为实际读取到的字节数，如果返回0，表示已到达文件尾或是无可读取的数据，此外文件读写位置会随读取到的字节移动。
## bind()函数
    int bind( int sockfd , const struct sockaddr * my_addr, socklen_t addrlen);
    参数列表中，sockfd 表示已经建立的socket编号（描述符）；
    my_addr 是一个指向sockaddr结构体类型的指针；
    参数addrlen表示my_addr结构的长度，可以用sizeof操作符获得。
##Listen()函数
    int listen(SOCKET sockfd, int backlog);
    listen函数一般在调用bind之后-调用accept之前调用。
    listen在套接字函数中表示让一个套接字处于监听到来的连接请求的状态
## accept（）函数
    accept(  
    SOCKET s,  
    struct sockaddr FAR * addr,  
    int FAR * addrlen  
    );  
    sockfd是由socket函数返回的套接字描述符，参数addr和addrlen用来返回已连接的对端进程（客户端）的协议地址。如果我们对客户端的协议地址不感兴趣，可以把arrd和addrlen均置为空指针

##write() 函数

    


