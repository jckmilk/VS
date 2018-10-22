##IPv4套接字地址结构
    struct in_addr 
	{
    in_addr_t s_addr;/* 32-bit IPV4 address*/
                     /* network byte ordered*/
    };
	struct sockaddr_in 
	{
	unit8_t sin_len;
	sa_family sin_family;
	in_port_t sin_port;
	struct in_addr sin_addr;
	char sin_zero[8]  
	};	

- 长度字段增加对OSI协议的支持。 无符号端整数(unsigned short)
- in_addr_t 数据必须是一个至少32位的无符号整型，in_port_t必须是  至少16位的无符号整型，sa_family可以是任意无符号整型，在不支持长度字段时它是16位无符号整型，否则是8为无符号整型
- 套接字地质结构仅在给定主机上使用，结构本身不在主机之间传递
##通用套接字地址结构
	    struct sockaddr 
    	{
    	unit8_t sa_len;
    	sa_family sa_family;
    	char  sa_data[14]
    	};
## IPV6套接字结构
    struct in_addr 
    	{
         unit8_t s6_addr[16];/* 128-bit IPV4 address*/
                             /* network byte ordered*/
    };
    	struct sockaddr_in6 
    	{
    	unit8_t sin6_len;
    	sa_family sin6_family;
		unit32_t sin5_flowinfo;
    	in_port_t sin6_port;
    	struct in6_addr sin6_addr;
    	unit32_t sin6_scope_id;
    	};	


##新的通用套接字地址结构
    struct sockaddr_storage
    {
    unit8_t ss_len;
    sa_family_t ss_family;
    }

##值-结果参数
(1)从进程到内核传递套接字地址结构的函数有三个：bind，connect，sendto。这些函数的一个参数是指向某一个套接字地址结构的指针，另一个参数是该结构的整数大小。如

    struct sockaddr_in serv;
    connect(sockfd,(SA*)&serv,sizeof(serv));
指针和指针所指向的内容大小都传递给内核了，于是内核知道从进程赋值多少的数据。
(2) 从内核到进程传递套接字地址结构的函数有4个:accept，recvfrom，getsockname，getpeername。这4个函数的其中两个参数是指向某个套接字地址结构的指针和指向表示该结构大小的整数变量的指针。

    struct sockaddr_un  clil;
    socklen_t len;
    len=sizoof(cli);
    getpeername(unixfd,(SA*)&cli,&len)
既充当入参，又充当出参。

##字节排序函数
小端字节和大端字节，网际协议使用大端字节序传送多字节整数。

    uint16_t htons(uint16_t, host16bitvalue);
    uint32_t htonl(uint32_t, host32bitvalue);
    uint16_t ntons(uint16_t, host16bitvalue);
    uint32_t ntonl(uint32_t, host32bitvalue);

#inet-aton、inet_addr和inet_ntoa函数
地址转换函数，将点分十进制数串转换为32位IPV4地址。
inet_pton和inet_ntop对ipv4和ipv6都适用。

    int inet_pton(int family, const char* strptr, void *addrptr);
    成功返回1，不是有效的格式返回0，出错返回-1；
    const char*inet_ntop(int family, const void* addrptr, char* strptr, size_t len)
    成功返回指向结果的指针，出错则为NULL;


