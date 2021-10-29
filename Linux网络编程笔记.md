# 一、基础概念

## 1. 网络基础概念

### 1.1 协议

协议: 协议是双方共同指定的一组规则, 在网络通信中表示通信双方
	  	传递数据和解释数据的一组规则.

```c
传输层 常见协议有TCP/UDP协议。
应用层 常见的协议有HTTP协议，FTP协议。
网络层 常见协议有IP协议、ICMP协议、IGMP协议。
网络接口层 常见协议有ARP协议、RARP协议。

1. TCP传输控制协议（Transmission Control Protocol）是一种面向连接的、可靠的、基于字节流的传输层通信协议。

2. UDP用户数据报协议（User Datagram Protocol）是OSI参考模型中一种无连接的传输层协议，提供面向事务的简单不可靠信息传送服务。

3. HTTP超文本传输协议（Hyper Text Transfer Protocol）是互联网上应用最为广泛的一种网络协议。

4. FTP文件传输协议（File Transfer Protocol）

5. IP协议是因特网互联协议（Internet Protocol）

6. ICMP协议是Internet控制报文协议（Internet Control Message Protocol）它是TCP/IP协议族的一个子协议，用于在IP主机、路由器之间传递控制消息。

7. IGMP协议是 Internet 组管理协议（Internet Group Management Protocol），是因特网协议家族中的一个组播协议。该协议运行在主机和组播路由器之间。

8. ARP协议是正向地址解析协议（Address Resolution Protocol），通过已知的IP，寻找对应主机的MAC地址。

9. RARP是反向地址转换协议，通过MAC地址确定IP地址。

```

## 2. OSI七层模型和TCP四层模型

​		OSI 是Open System Interconnection的缩写, 意为开放式系统互联. [国际标准化组织](https://baike.so.com/doc/5340499-5575942.html)(ISO)制定了OSI 模型, 该模型定义了不同计算机互联的标准, 是设计和描述计算机网络通信的基本框架.

```c
网络分层 OSI 7层模型: 物链网输会示用（物联网淑慧适用）
	物理层---双绞线，光纤（传输介质），将模拟信号转换为数字信号
	数据链路层---数据校验，定义了网络传输的基本单位-帧 
	网络层---定义网络，两台机器之间传输的路径选择点到点的传输
	传输层---传输数据 TCP，UDP，端到端的传输  
	会话层---通过传输层建立数据传输的通道. 
	表示层---编解码，翻译工作.
	应用层---为客户提供各种应用服务，email服务，ftp服务，ssh服务 

```

## 3. 两种网络应用程序设计模式

### 3.1 C/S模式

​	传统的网络应用设计模式，客户机(client)/服务器(server)模式。需要在通讯两端各自部署客户机和服务器来完成数据通信。
B/S模式
浏览器()/服务器(server)模式。只需在一端部署服务器，而另外一端使用每台PC都默认配置的浏览器即可完成数据的传输。
优缺点
​	对于C/S模式来说，其优点明显。客户端位于目标主机上可以保证性能，将数据缓存至客户端本地，从而提高数据传输效率。且，一般来说客户端和服务器程序由一个开发团队创作，所以他们之间所采用的协议相对灵活。可以在标准协议的基础上根据需求裁剪及定制。例如，腾讯公司所采用的通信协议，即为ftp协议的修改剪裁版。
​	因此，传统的网络应用程序及较大型的网络应用程序都首选C/S模式进行开发。如，知名的网络游戏魔兽世界。3D画面，数据量庞大，使用C/S模式可以提前在本地进行大量数据的缓存处理，从而提高观感。
​	C/S模式的缺点也较突出。由于客户端和服务器都需要有一个开发团队来完成开发。工作量将成倍提升，开发周期较长。另外，从用户角度出发，需要将客户端安插至用户主机上，对用户主机的安全性构成威胁。这也是很多用户不愿使用C/S模式应用程序的重要原因。

### 3.2 B/S模式

B/S模式相比C/S模式而言，由于它没有独立的客户端，使用标准浏览器作为客户端，其工作开发量较小。只需开发服务器端即可。另外由于其采用浏览器显示数据，因此移植性非常好，不受平台限制。如早期的偷菜游戏，在各个平台上都可以完美运行。
	B/S模式的缺点也较明显。由于使用第三方浏览器，因此网络应用支持受限。另外，没有客户端放到对方主机上，缓存数据不尽如人意，从而传输数据量受到限制。应用的观感大打折扣。第三，必须与浏览器一样，采用标准 http 协议进行通信，协议选择不灵活.



# 二、SOCKET编程

## 1.预备知识

网络字节序:

​    	大端和小端的概念

​       		大端: 低位地址存放高位数据, 高位地址存放低位数据

​       		小端: 低位地址存放低位数据, 高位地址存放高位数据

大端和小端的使用使用场合: 

​		大端和小端只是对数据类型长度是两个及以上的, 如int  short, 对于单字节 没限制, 在网络中经常需要考虑大端和小端的是 IP 和端口.

```c
	如何验证本机上大端还是小端??-----使用共用体.
		编写代码endian.c进行测试, 测试本机上是大端模式还是小端模式?
	
	网络传输用的是大端法, 如果机器用的是小端法, 则需要进行大小端的转换.
下面4个函数就是进行大小端转换的函数:
	   #include <arpa/inet.h>
       uint32_t htonl(uint32_t hostlong);
       uint16_t htons(uint16_t hostshort);
       uint32_t ntohl(uint32_t netlong);
       uint16_t ntohs(uint16_t netshort);
	函数名的h表示主机host, n表示网络network, s表示short, l表示long
上述的几个函数, 如果本来不需要转换函数内部就不会做转换.

IP地址转换函数:
	p->表示点分十进制的字符串形式
	to->到
	n->表示network网络
    
    int inet_pton(int af, const char *src, void *dst);
    函数说明: 将字符串形式的点分十进制IP转换为大端模式的网络IP(整形4字节数)
    参数说明:
        af: AF_INET
        src: 字符串形式的点分十进制的IP地址
        dst: 存放转换后的变量的地址
        例如: inet_pton(AF_INET, "127.0.0.1", &serv.sin_addr.s_addr);	

    手工也可以计算: 如192.168.232.145, 先将4个正数分别转换为16进制数, 
    192--->0xC0  168--->0xA8   232--->0xE8   145--->0x91
    最后按照大端字节序存放: 0x91E8A8C0, 这个就是4字节的整形值.

    const char *inet_ntop(int af, const void *src, char *dst, socklen_t size);
    函数说明: 网络IP转换为字符串形式的点分十进制的IP
    参数说明:
        af: AF_INET
        src: 网络的整形的IP地址
        dst: 转换后的IP地址,一般为字符串数组
        size: dst的长度
    返回值: 
        成功--返回指向dst的指针
        失败--返回NULL, 并设置errno

    例如: IP地址为010aa8c0, 转换为点分十进制的格式:
    01---->1    0a---->10   a8---->168   c0---->192
    由于从网络中的IP地址是高端模式, 所以转换为点分十进制后应该为: 	192.168.10.1

```

## 2. sockaddr结构体介绍

![image-20210819155300916](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210819155300916.png)

```c
struct sockaddr 结构说明:
   struct sockaddr {
        sa_family_t sa_family;
        char     sa_data[14];
   }
struct sockaddr_in 结构:
struct sockaddr_in {
         sa_family_t    sin_family; /* address family: AF_INET */
         in_port_t      sin_port;   /* port in network byte order */
         struct in_addr sin_addr;   /* internet address */
   };

   /* Internet address. */
   struct in_addr {
         uint32_t  s_addr;     /* address in network byte order */
   };	 //网络字节序IP--大端模式

man 7 ip查看
```

## 3. socket编程主要API函数

```c
int socket(int domain, int type, int protocol);
函数描述: 创建socket
参数说明:
    domain: 协议版本
        AF_INET IPV4
        AF_INET6 IPV6
        AF_UNIX AF_LOCAL本地套接字使用
    type:协议类型
        SOCK_STREAM 流式, 默认使用的协议是TCP协议
        SOCK_DGRAM  报式, 默认使用的是UDP协议
    protocal: 
        一般填0, 表示使用对应类型的默认协议.
返回值: 
    成功: 返回一个大于0的文件描述符
    失败: 返回-1, 并设置errno

	当调用socket函数以后, 返回一个文件描述符, 内核会提供与该文件描述符相对应的读和写缓冲区, 同时还有两个队列, 分别是请求连接队列和已连接队列.

```



```c
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
函数描述: 将socket文件描述符和IP,PORT绑定
参数说明:
    socket: 调用socket函数返回的文件描述符
    addr: 本地服务器的IP地址和PORT, 
        struct sockaddr_in serv;
        serv.sin_family = AF_INET;
        serv.sin_port = htons(8888);
        //serv.sin_addr.s_addr = htonl(INADDR_ANY);
        //INADDR_ANY: 表示使用本机任意有效的可用IP

        inet_pton(AF_INET, "127.0.0.1", &serv.sin_addr.s_addr);
    addrlen: addr变量的占用的内存大小 
返回值: 
    成功: 返回0
    失败: 返回-1, 并设置errno

```



```c
int listen(int sockfd, int backlog);
函数描述: 将套接字由主动态变为被动态
参数说明:
    sockfd: 调用socket函数返回的文件描述符
    backlog: 同时请求连接的最大个数(还未建立连接) 
返回值:
    成功: 返回0
    失败: 返回-1, 并设置errno

```



```c
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);	
函数说明:获得一个连接, 若当前没有连接则会阻塞等待.
函数参数:
    sockfd: 调用socket函数返回的文件描述符
    addr: 传出参数, 保存客户端的地址信息
    addrlen: 传入传出参数,  addr变量所占内存空间大小
返回值:
    成功: 返回一个新的文件描述符,用于和客户端通信
    失败: 返回-1, 并设置errno值.

	accept函数是一个阻塞函数, 若没有新的连接请求, 则一直阻塞.
	从已连接队列中获取一个新的连接, 并获得一个新的文件描述符, 该文件描述符用于和客户端通信.  (内核会负责将请求队列中的连接拿到已连接队列中)

```



```c
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
函数说明: 连接服务器
函数参数:
    sockfd: 调用socket函数返回的文件描述符
    addr: 服务端的地址信息
    addrlen: addr变量的内存大小
返回值:
    成功: 返回0
    失败: 返回-1, 并设置errno值


接下来就可以使用write和read函数进行读写操作了.
除了使用read/write函数以外, 还可以使用recv和send函数

读取数据和发送数据:
    ssize_t read(int fd, void *buf, size_t count);
    ssize_t write(int fd, const void *buf, size_t count);
    ssize_t recv(int sockfd, void *buf, size_t len, int flags);
    ssize_t send(int sockfd, const void *buf, size_t len, int flags);	
	对应recv和send这两个函数flags直接填0就可以了.

	注意: 如果写缓冲区已满, write也会阻塞, read读操作的时候, 若读缓冲区没有数据会引起阻塞.

```

## 4. socket编写服务端和客户端程序的步骤图示

![image-20210819160959570](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210819160959570.png)

```c
服务端开发流程:
	1 创建socket,返回一个文件描述符lfd---socket()
		--该文件描述符用于监听客户端连接
	2 将lfd和IP  PORT进行绑定----bind()
	3 将lfd由主动变为被动监听----listen()
	4 接受一个新的连接,得到一个文件描述符cfd----accept()
		---该文件描述符是用于和客户端进行通信的
	5 while(1)
	  {
	  	接收数据---read或者recv
	  	发送数据---write或者send
	  }
	6 关闭文件描述符----close(lfd)  close(cfd);
```

```c
客户端的开发流程:
	1 创建socket, 返回一个文件描述符cfd---socket()
		---该文件描述符是用于和服务端通信
	2 连接服务端---connect() 
	3 while(1)
	  {
	  	//发送数据---write或者send
	  	//接收数据---read或者recv
	  }	  
	4 close(cfd)
```

服务器端代码示例：

```c
//服务端程序
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <ctype.h>

int main()
{
	//创建socket
	//int socket(int domain, int type, int protocol);
	int lfd = socket(AF_INET, SOCK_STREAM, 0);
	if(lfd<0)
	{
		perror("socket error");
		return -1;
	}
	
	//int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
	//绑定
	struct sockaddr_in serv;
	bzero(&serv, sizeof(serv));
	serv.sin_family = AF_INET;
	serv.sin_port = htons(8888);
	serv.sin_addr.s_addr = htonl(INADDR_ANY); //表示使用本地任意可用IP
	int ret = bind(lfd, (struct sockaddr *)&serv, sizeof(serv));
	if(ret<0)
	{
		perror("bind error");	
		return -1;
	}

	//监听
	//int listen(int sockfd, int backlog);
	listen(lfd, 128);

	//int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
	struct sockaddr_in client;
	socklen_t len = sizeof(client);
	int cfd = accept(lfd, (struct sockaddr *)&client, &len);  //len是一个输入输出参数
	//const char *inet_ntop(int af, const void *src, char *dst, socklen_t size);
	
	//获取client端的IP和端口
	char sIP[16];
	memset(sIP, 0x00, sizeof(sIP));
	printf("client-->IP:[%s],PORT:[%d]\n", inet_ntop(AF_INET, &client.sin_addr.s_addr, sIP, sizeof(sIP)), ntohs(client.sin_port));
	printf("lfd==[%d], cfd==[%d]\n", lfd, cfd);

	int i = 0;
	int n = 0;
	char buf[1024];

	while(1)
	{
		//接收数据
		memset(buf, 0x00, sizeof(buf));
		n = read(cfd, buf, sizeof(buf));
		if(n<=0)
		{
			printf("read error or client close, n==[%d]\n", n);
			break;
		}
		printf("n==[%d], buf==[%s]\n", n, buf);	

		for(i=0; i<n; i++)
		{
			buf[i] = toupper(buf[i]);
		}

		//发送数据
		write(cfd, buf, n);
	}

	//关闭监听文件描述符和通信文件描述符
	close(lfd);
	close(cfd);
	
	return 0;
}

```

用户端代码示例：

```c
//客户端代码
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>

int main()
{
	//创建socket---用于和服务端进行通信
	int cfd = socket(AF_INET, SOCK_STREAM, 0);
	if(cfd<0)
	{
		perror("socket error");
		return -1;
	}

	//连接服务端
	//int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
	struct sockaddr_in serv;
	serv.sin_family = AF_INET;
	serv.sin_port = htons(8888);
	inet_pton(AF_INET, "127.0.0.1", &serv.sin_addr.s_addr);
	printf("[%x]\n", serv.sin_addr.s_addr);
	int ret = connect(cfd, (struct sockaddr *)&serv, sizeof(serv));
	if(ret<0)
	{
		perror("connect error");
		return -1;
	}	

	int n = 0;
	char buf[256];
	while(1)
	{
		//读标准输入数据
		memset(buf, 0x00, sizeof(buf));
		n = read(STDIN_FILENO, buf, sizeof(buf));
		
		//发送数据
		write(cfd, buf, n);

		//读服务端发来的数据
		memset(buf, 0x00, sizeof(buf));
		n = read(cfd, buf, sizeof(buf));
		if(n<=0)
		{
			printf("read error or server closed, n==[%d]\n", n);
			break;
		}
		printf("n==[%d], buf==[%s]\n", n, buf);
	}

	//关闭套接字cfd
	close(cfd);

	return 0;
}
```

细节：

1. 调用accept函数是从已连接队列中取出一个可用链接，如果没有则会阻塞等待。

2. netstat -anp | grep 端口号 可以查看对应端口的连接



```c
测试本机是大端还是小端：

#include <stdio.h>
#include <stdlib.h>

union {
    short s;
    char c[sizeof(short)];
} un2;

union {
	int s;
	char c[sizeof(int)];
}un4;

int main()
{
	printf("[%d][%d][%d]\n", sizeof(short), sizeof(int), sizeof(long int));

	//测试short类型
    un2.s = 0x0102;// 0x0102 =? 16*16+2
    printf("%d,%d,%d\n",un2.c[0],un2.c[1],un2.s);

	//测试int类型
	//un4.s = 0x12345678;
	un4.s = 0x01020304;
	printf("%d,%d,%d,%d,%d\n", un4.c[0], un4.c[1], un4.c[2], un4.c[3], un4.s);
    return 0;
}


```

## 5.粘包问题

​		只有TCP连接会产生粘包问题，UDP连接永远不会。粘包问题主要还是因为接收方不知道消息之间的界限，不知道一次性提取多少字节的数据所造成的。

解决方法：

1. 包头+数据

   ​	如4位的数据长度+数据 -----------> 00101234567890 

   ​	其中0010表示数据长度, 1234567890表示10个字节长度的数据.

   ​	另外, 发送端和接收端可以协商更为复杂的报文结构, 这个报文结   构就相当于双方约定的一个协议.

2. 添加结尾标记.

   ​	如结尾最后一个字符为\n \$等.

3. 数据包定长
           如发送方和接收方约定, 每次只发送128个字节的内容, 接收方接收定      长128个字节就可以了.



## 6.多进程版本的服务器思路分析

```c
1. 创建socket, 得到一个监听的文件描述符lfd---socket()
    2. 将lfd和IP和端口port进行绑定-----bind();
3. 设置监听----listen()
    4. 进入while(1)
{
    //等待有新的客户端连接到来
    cfd = accept();

    //fork一个子进程, 让子进程去处理数据
    pid = fork();
    if(pid<0)
    {
        exit(-1);
    }
    else if(pid>0)
    {
        //关闭通信文件描述符cfd
        close(cfd);
    }
    else if(pid==0)
    {
        //关闭监听文件描述符
        close(lfd);

        //收发数据
        while(1)
        {
            //读数据
            n = read(cfd, buf, sizeof(buf));
            if(n<=0)
            {
                break;
            }

            //发送数据给对方
            write(cfd, buf, n);
        }

        close(cfd);

        //下面的exit必须有, 防止子进程再去创建子进程
        exit(0);
    }
}
close(lfd);
	
还需要添加的功能: 父进程使用SIGCHLD信号完成对子进程的回收
注意点: accept或者read函数是阻塞函数, 会被信号打断, 此时不应该视为一个错误.
        errno=EINTR

父子进程能够共享的:
	文件描述符(子进程复制父进程的文件描述符)
	mmap共享映射区 
```

```c
//多进程版本的服务器
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <signal.h>
#include <sys/wait.h>
#include <ctype.h>
#include "wrap.h"

//信号处理函数
void waitchild(int signo)
{
	pid_t wpid;
	while(1)
	{
		wpid = waitpid(-1, NULL, WNOHANG);
		if(wpid>0)
		{
			printf("child exit, wpid==[%d]\n", wpid);
		}
		else if(wpid==0 || wpid==-1)
		{
			break;
		}
	}
}

int main()
{
	//创建socket
	int lfd = Socket(AF_INET, SOCK_STREAM, 0);

	//设置端口复用
	int opt = 1;
	setsockopt(lfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(int));

	//绑定-bind
	struct sockaddr_in serv;
	serv.sin_family = AF_INET;
	serv.sin_port = htons(8888);
	serv.sin_addr.s_addr = htonl(INADDR_ANY);
	Bind(lfd, (struct sockaddr *)&serv, sizeof(serv));

	//监听-listen
	Listen(lfd, 128);

	//阻塞SIGCHLD信号
	sigset_t mask;
	sigemptyset(&mask);
	sigaddset(&mask, SIGCHLD);
	sigprocmask(SIG_BLOCK, &mask, NULL);

	int cfd;
	socklen_t len;
	char sIP[16];
	pid_t pid;
	struct sockaddr_in client;
	while(1)
	{
		//等待客户端连接--accept
		memset(sIP, 0x00, sizeof(sIP));
		len = sizeof(client);
		bzero(&client, sizeof(client));
		
		cfd = Accept(lfd, (struct sockaddr *)&client, &len);	
		printf("client-->[%s]:[%d]\n", inet_ntop(AF_INET, &client.sin_addr.s_addr, sIP, sizeof(sIP)), ntohs(client.sin_port));

		//创建子进程
		pid = fork();
		if(pid<0)
		{
			perror("fork error");
			close(lfd);
			return -1;
		}	
		else if(pid>0) //父进程
		{
			//关闭通信的文件描述符
			close(cfd);

			//注册SIGCHLD信号处理函数
			struct sigaction act;
			act.sa_handler = waitchild;
			act.sa_flags = 0;
			sigemptyset(&act.sa_mask);
			sigaction(SIGCHLD, &act, NULL);

			//解除对SIGCHLD信号的阻塞
			sigprocmask(SIG_UNBLOCK, &mask, NULL);
		}
		else if(pid==0) //子进程
		{
			//关闭监听文件描述符
			close(lfd);	

			int i = 0;
			int n = 0;
			char buf[1024];

			while(1)
			{
				memset(buf, 0x00, sizeof(buf));
				n = Read(cfd, buf, sizeof(buf));
				if(n<=0)	
				{
					printf("read error or client closed, n==[%d]\n", n);
					break;	
				}
				printf("read over, n==[%d],buf==[%s]\n", n, buf);
				
				for(i=0; i<n; i++)
				{
					buf[i] = toupper(buf[i]);
				}
				write(cfd, buf, n);
			}

			close(cfd);
			exit(0);
		}
	}

	//关闭监听文件描述符
	close(lfd);

	return 0;
}

```



## 7.多线程的服务器开发流程

```c
{
	1 创建socket, 得到一个监听的文件描述符lfd---socket()
	2 将lfd和IP和端口port进行绑定-----bind();
	3 设置监听----listen() 
	4 while(1)
	  {
	  	//接受新的客户端连接请求
	  	cfd = accept();
	  	
	  	//创建一个子线程
	  	pthread_create(&threadID, NULL, thread_work, &cfd);
	  	
	  	//设置线程为分离属性
	  	pthread_detach(threadID);
	  	
	  }
	  
	  close(lfd);
}	  	
	  
子线程执行函数:
	void *thread_work(void *arg)
	{
		//获得参数: 通信文件描述符
		int cfd = *(int *)arg;
		
		while(1)
		{
			//读数据
			n = read(cfd, buf, sizeof(buf));
			if(n<=0)
			{
				break;
			}
			
			//发送数据
			write(cfd, buf, n);
		}
		
		close(cfd);
	}
	
问题:
	1 子线程能否关闭lfd?
		子线程不能关闭监听文件描述符lfd,原因是子线程和主线程共享文件描述符
		而不是复制的.
	2 主线程能否关闭cfd?
		主线程不能关闭cfd, 主线程和子线程共享一个cfd, 而不是复制的, close之后cfd就会被真正关闭.
	3 多个子线程共享cfd, 会有什么问题发生?
		cdf只会指向最后一个线程的cfd，多个一起连接时，会覆盖掉之前的连接
struct INFO
{
	int cfd;
	pthread_t threadID;
	struct sockaddr_in client;
};
struct INFO info[100];

//初始化INFO数组
for(i=0; i<100; i++)
{
	info[i].cfd=-1;
}


for(i=0; i<100; i++)
{
	if(info[i].cfd==-1)
	{
		//这块内存可以使用
	}
}

if(i==100)
{
	//拒绝接受新的连接
	close(cfd);
}
```



```c
//多线程版本的服务器
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <ctype.h>
#include <pthread.h>
#include "wrap.h"

typedef struct info
{
	int cfd;   //若为-1表示可用, 大于0表示已被占用
	int idx;
	pthread_t thread;
	struct sockaddr_in client; //客户端地址
}INFO;

INFO thInfo[1024];

//线程执行函数
void *thread_work(void *arg)
{
	INFO *p = (INFO *)arg;
	printf("idx==[%d]\n", p->idx);

	char sIP[16];
	memset(sIP, 0x00, sizeof(sIP));
	printf("new client:[%s][%d]\n", inet_ntop(AF_INET, &(p->client.sin_addr.s_addr), sIP, sizeof(sIP)), ntohs(p->client.sin_port));

	int n;
	int cfd = p->cfd;
	struct sockaddr_in client;
	memcpy(&client, &(p->client), sizeof(client));
	
	char buf[1024];
	
	while(1)
	{
		memset(buf, 0x00, sizeof(buf));
		//读数据
		n = Read(cfd, buf, sizeof(buf));
		if(n<=0)
		{
			printf("read error or client closed, n==[%d]\n", n);
			Close(cfd);
			p->cfd =-1;  //设置为-1表示该位置可用
			pthread_exit(NULL);
		}
		
		for(int i=0; i<n; i++)
		{
			buf[i] = toupper(buf[i]);
		}
		//发送数据
		Write(cfd, buf, n);
	}
}

void init_thInfo()
{
	int i = 0;
	for(i=0; i<1024; i++)
	{
		thInfo[i].cfd = -1;;
	}
}

int findIndex()
{
	int i;
	for(i=0; i<1024; i++)
	{
		if(thInfo[i].cfd==-1)
		{
			break;
		}
	}
	if(i==1024)
	{
		return -1;
	}
	
	return i;
}

int main()
{
	//创建socket
	int lfd = Socket(AF_INET, SOCK_STREAM, 0);
	
	//设置端口复用
	int opt = 1;
	setsockopt(lfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(int));
	
	//绑定--将lfd 和 IP PORT绑定
	struct sockaddr_in serv;
	bzero(&serv, sizeof(serv));
	serv.sin_family = AF_INET;
	serv.sin_port = htons(8888);
	serv.sin_addr.s_addr = htonl(INADDR_ANY);
	Bind(lfd, (struct sockaddr *)&serv, sizeof(serv));
	
	//监听
	Listen(lfd, 128);
		
	//初始化
	init_thInfo();
	
	int cfd;
	int ret;
	int idx;
	socklen_t len;
	pthread_t thread;
	struct sockaddr_in client;
	while(1)
	{
		len = sizeof(client);
		bzero(&client, sizeof(client));
		//获得一个新的连接
		cfd = Accept(lfd, (struct sockaddr *)&client, &len);
		
		//找数组中空闲的位置
		idx = findIndex();
		if(idx==-1)
		{
			Close(cfd);
			continue;
		}
		
		//对空闲位置的元素的成员赋值
		thInfo[idx].cfd = cfd;
		thInfo[idx].idx = idx;
		memcpy(&thInfo[idx].client, &client, sizeof(client));
		
		//创建子线程---该子线程完成对数据的收发
		ret = pthread_create(&thInfo[idx].thread, NULL, thread_work, &thInfo[idx]);
		if(ret!=0)
		{
			printf("create thread error:[%s]\n", strerror(ret));
			exit(-1);
		}
		
		//设置子线程为分离属性
		pthread_detach(thInfo[idx].thread);
	
	}
	
	Close(lfd);
	
	return 0;
	
}

```



# 三.使用SSH命令行传输文件到远程服务器

### 1、上传本地文件到服务器

```shell
scp /path/filename username@servername:/path/
```

例如scp /var/www/test.php root@192.168.0.101:/var/www/ 把本机/var/www/目录下的test.php文件上传到192.168.0.101这台服务器上的/var/www/目录中

 

### 2、从服务器上下载文件

下载文件我们经常使用wget，但是如果没有http服务，如何从服务器上下载文件呢？

```shell
scp username@servername:/path/filename /var/www/local_dir（本地目录）
```

例如scp root@192.168.0.101:/var/www/test.txt 把192.168.0.101上的/var/www/test.txt 的文件下载到/var/www/local_dir（本地目录）

 

### 3、从服务器下载整个目录

```shell
scp -r username@servername:/var/www/remote_dir/（远程目录） /var/www/local_dir（本地目录）
```

例如:scp -r root@192.168.0.101:/var/www/test /var/www/

 

### 4、上传目录到服务器

```shell
scp -r local_dir username@servername:remote_dir
```

例如：scp -r test root@192.168.0.101:/var/www/ 把当前目录下的test目录上传到服务器的/var/www/ 目录



# 四、TCP状态转换图

![image-20210823161034104](E:\typroa_pic\image-20210823161034104.png)

了解TCP状态转换图可以帮助开发人员查找问题.

说明: 上图中粗线表示主动方, 虚线表示被动方, 细线部分表示一些特殊情况, 了解即可, 不必深入研究.

​    对于建立连接的过程客户端属于主动方, 服务端属于被动接受方(图的上半部分)

而对于关闭(图的下半部分), 服务端和客户端都可以先进行关闭.处于ESTABLISHED状态的时候就可以收发数据了, 双方在通信过程当中一直处于ESTABLISHED状态, 数据传输期间没有状态的变化.TIME_WAIT状态一定是出现在主动关闭的一方.

​	主动关闭的Socket端会进入TIME_WAIT状态，并且持续2MSL时间长度，MSL就是maximum segment lifetime(最大分节生命期），这是一个IP数据包能在互联网上生存的最长时间，超过这个时间将在网络中消失。

使用netstat -anp可以查看连接状态

![image-20210823161137639](E:\typroa_pic\image-20210823161137639.png)

![image-20210817212135934](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210817212135934.png?lastModify=1629708575)

![image-20210817213805828](file://C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210817213805828.png?lastModify=1629708964)

```c
注:数据传输的时候带了一个字节的数据, 所以server发送给client的ACK=x+2

为什么需要2MSL?
	原因之一: 让四次挥手的过程更可靠, 确保最后一个发送给对方的ACK到达;
若对方没有收到ACK应答, 对方会再次发送FIN请求关闭, 此时在2MS时间内被动关闭方仍然可以发送ACK给对方.
	
	原因之二: 为了保证在2MSL时间内, 不能启动相同的SOCKET-PAIR.
	TIME_WAIT一定是出现在主动关闭的一方, 也就是说2MS是针对主动关闭一方来说的;由于TCP有可能存在丢包重传, 丢包重传若发给了已经断开连接之后相同的socket-pair(该连接是新建的, 与原来的socket-pair完全相同, 双方使用的是相同的IP和端口), 这样会对之后的连接造成困扰, 严重可能引起程序异常.

	如何避免问题2呢??
		--很多操作系统实现的时候, 只要端口被占用, 服务就不能启动.

测试: 启动服务端和客户端, 然后先关闭服务端, 再次启动服务端, 此时服务端报错: bind error: Address already in use; 若是先关闭的客户端, 再关闭的服务端, 此时启动服务端就不会报这个错误.

socket-pair的概念: 客户端与服务端连接其实是一个连接对, 可以通过使用netstat -anp | grep 端口号 进行查看.
```

## 1. 设置端口复用

```c
setsockopt 函数
int setsockopt(int sockfd, int level, int optname, const void *optval, socklen_t optlen);
	setsockopt(lfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(int));
	setsockopt(lfd, SOL_SOCKET, SO_REUSEPORT, &opt, sizeof(int));

```

## 2. shutdown和close的区别

1 shutdown可以实现半关闭, close不行
2 shutdown关闭的时候, 不考虑文件描述符的引用计数, 是直接彻底关闭(关闭只针对用户行为，内核仍然可以发送接收)
close考虑文件描述符的引用计数, 调用一次close只是将引用计数减1, 
只有减小到0的时候才会真正关闭.

## 3. 长连接和端连接的概念

​	长连接: 连接建立好之后,一直保持连接不关闭
​	短连接: 连接使用完之后就立刻关闭.
​	

## 4.什么是心跳包

​	用于监测网络连接（主要是长连接）是否正常的字符串.
在什么情况下使用心跳包?
​	主要用于监测长连接是否正常.
如何使用心跳包?
​	通信双方需要协商规则(协议), 如4个字节长度+数据部分

```c
使用方法：
    方法1
    	keepAlive = 1;
    	setsockopt(listenfd, SOL_SOCKET, SO_KEEPALIVE, (void*)&keepAlive, sizeof(keepAlive));
    	由于不能实时的检测网络情况, 一般不用这种方法
    方法2: 在应用程序中自己定义心跳包, 使用灵活, 能实时把控.
```



# 五、IO多路复用

## 1. select模型

```c
多路IO技术: select, 同时监听多个文件描述符, 将监控的操作交给内核去处理, 

数据类型fd_set: 文件描述符集合--本质是位图(关于集合可联想一个信号集sigset_t)
int select(int nfds, fd_set * readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
函数介绍: 委托内核监控该文件描述符对应的读,写或者错误事件的发生.
参数说明: 
	nfds: 最大的文件描述符+1
        
	readfds: 读集合, 是一个传入传出参数
		传入: 指的是告诉内核哪些文件描述符需要监控
		传出: 指的是内核告诉应用程序哪些文件描述符发生了变化
	writefds: 写文件描述符集合(传入传出参数)
	execptfds: 异常文件描述符集合(传入传出参数)
        
	timeout: 
		NULL--表示永久阻塞, 直到有事件发生
		0 --表示不阻塞, 立刻返回, 不管是否有监控的事件发生
		>0--表示阻塞的时长，若没有超过时长，则一直阻塞，若在时长内有事件发生，则立刻返回；若超过时长，也立刻返回。
返回值: 
    成功返回发生变化的文件描述符的个数,没有事件发生则返回0
    失败返回-1, 并设置errno值.


/usr/include/x86_64-linux-gnu/sys/select.h和
/usr/include/x86_64-linux-gnu/bits/select.h
从上面的文件中可以看出, 这几个宏本质上还是位操作.

void FD_CLR(int fd, fd_set *set);
将fd从set集合中清除.
int FD_ISSET(int fd, fd_set *set);
功能描述: 判断fd是否在集合中
返回值: 如果fd在set集合中, 返回1, 否则返回0.
void FD_SET(int fd, fd_set *set);
将fd设置到set集合中.
void FD_ZERO(fd_set *set);
初始化set集合.//很重要

调用select函数其实就是委托内核帮我们去检测哪些文件描述符有可读数据,可写,错误发生;
```

```c
（1）执行fd_set set；FD_ZERO(&set)；则set用位表示为 0000,0000 。

（2）若fd = 5 ，则执行 FD_SET(fd,&set)后，set变为 00010000 （第5位置为1）

（3）若再加入fd=2 ，fd=1，则set变为 0001.0011

（4）执行select(6,&set,0,0,0)阻塞等待

（5）若fd=1,fd=2上都发生可读事件，则select返回，此时set变为0000，0011。没有可读事件发生时 fd = 5 被清空。
```



### 1.1 select开发网络服务器流程

```c
1 创建socket, 得到监听文件描述符lfd---socket()
2 设置端口复用-----setsockopt()
3 将lfd和IP  PORT绑定----bind()
4 设置监听---listen()
5 fd_set readfds;  //定义文件描述符集变量
  fd_set tmpfds;
  FD_ZERO(&readfds);  //初始化文件描述符集变量
  FD_SET(lfd, &readfds);//将lfd加入到readfds集合中;
  maxfd = lfd;
  while(1)
  {
  	tmpfds = rdfds;
  	nready = select(maxfd+1, &tmpfds, NULL, NULL, NULL);
  	if(nready<0)
  	{
  		if(errno==EINTR)//被信号中断
  		{
  			continue;
  		}
  		break;
  	}
  	
  	//有客户端连接请求到来
  	if(FD_ISSET(lfd, &tmpfds))
  	{
  		//接受新的客户端连接请求
  		cfd = accept(lfd, NULL, NULL);
  		
  		//将cfd加入到readfds集合中
  		FD_SET(cfd, &readfds);
  		
  		//修改内核监控的文件描述符的范围
  		if(maxfd<cfd)
  		{
  			maxfd = cfd;
  		}
  		
  		if(--nready==0)
  		{
  			continue;
  		}
  	}
  	
  	
  	//有客户端数据发来
  	for(i=lfd+1; i<=maxfd; i++)
  	{
  		if(FD_ISSET(i, &tmpfds))
  		{
            while(1)
            {
                //read数据
                n = read(i, buf, sizeof(buf));
                if(n<=0)
                {
                    close(i);
                    //将文件描述符i从内核中去除
                    FD_CLR(i, &readfds);
                }

                //write应答数据给客户端
                write(i, buf, n);
            }
                
  		}
  		
		if(--nready==0)
  		{
  			break;
  		}
  	}
  	
  	close(lfd);
  	
  	return 0;
  }
```

代码实现

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <signal.h>
#include <sys/wait.h>
#include <ctype.h>
#include "wrap.h"

int main()
{
	//创建socket
	int lfd = Socket(AF_INET,SOCK_STREAM,0);
	
	//设置端口复用
	int opt = 1;
	setsockopt(lfd,SOL_SOCKET,SO_REUSEADDR,&opt,sizeof(opt));
	
	//绑定端口号
	struct sockaddr_in serv;
	serv.sin_family = AF_INET;
	serv.sin_port = htons(8888);
	serv.sin_addr.s_addr = htonl(INADDR_ANY);
	Bind(lfd,(struct sockaddr *)&serv,sizeof(serv));

	//监听
	Listen(lfd,128);

	//定义文件描述符集变量
	fd_set readfds;
	fd_set tmpfds;
	
	//初始化
	FD_ZERO(&readfds);	
	FD_ZERO(&tmpfds);
	
	FD_SET(lfd,&readfds);//将lfd加入文件描述符集合中,委托内核监控

	int nready;
	int maxfd = lfd;
	int i;
	int cfd;
	int n;
	char buf[1024];
	int sockfd;

	while(1)
	{
		tmpfds = readfds;//及时更新tmpfds，有新的文件描述符加入
	
		//select返回发生变化的文件描述符个数
		//tmpfds是输入输出参数：
		//	输入：告诉内核要监测哪些文件描述符
		//	输出：将变化了之后的文件描述符集输出，没有告诉具体变化的文件描述符，变化是指有数据发来或者有新的客户端连接请求到来
		nready = select(maxfd+1, &tmpfds,NULL,NULL,NULL);
		
		if(nready<0)
		{
			if(errno==EINTR)//被信号中断
			{
				continue;
			}	
			break;
		}
		printf("nready==[%d]\n",nready);
		
		//有客户端连接请求
		if(FD_ISSET(lfd,&tmpfds))
		{
			//接受新的连接请求
			cfd = Accept(lfd,NULL,NULL);
			
			//将cfd加入到文件描述符集中
			FD_SET(cfd,&readfds);

			//修改内核监控的文件描述符的范围
			if(maxfd<cfd)
			{
				maxfd = cfd ;
			}
			if(--nready==0)
			{
				continue;
			}
		}


		//有数据发来

		for(i = lfd+1;i<=maxfd;i++)
		{
			sockfd=i;
			if(FD_ISSET(sockfd,&tmpfds))
			{
				//读数据
				memset(buf,0x00,sizeof(buf));
				n=Read(sockfd,buf,sizeof(buf));
				if(n<=0)
				{
					//关闭连接
					close(sockfd);
					FD_CLR(sockfd,&readfds);
				}
				else
				{	
					printf("n==[%d],buf==[%s]\n",n,buf);
					int k = 0;
					for(k = 0;k<n;k++)
					{	
						buf[k]=toupper(buf[k]);
					}
					Write(sockfd,buf,n);
				}
				if(--nready==0)break;//防止作空循环
			}
			
		}
	
	}
	
	close(lfd);
	return 0;
}

```



### 1.2 select优缺点

```c
select优点:
	1 一个进程可以支持多个客户端
	2 select支持跨平台
select缺点:
	1 代码编写困难
	2 会涉及到用户区到内核区的来回拷贝
	3 当客户端多个连接, 但少数活跃的情况, select效率较低
例如: 作为极端的一种情况, 3-1023文件描述符全部打开, 但是只有1023有发送数据, select就显得效率低下
	4 最大支持1024个客户端连接
select最大支持1024个客户端连接不是有文件描述符表最多可以支持1024个文件描述符限制的, 而是由FD_SETSIZE=1024限制的.	

FD_SETSIZE=1024  fd_set使用了该宏, 当然可以修改内核, 然后再重新编译内核, 一般不建议这么做.
```

## 2. poll模型

```c
int poll(struct pollfd *fds, nfds_t nfds, int timeout);
函数说明: 跟select类似, 委托内核监控可读, 可写, 异常事件
函数参数:
	fds: 一个struct pollfd结构体数组的首地址
	   struct pollfd {
	       int   fd;    //要监控的文件描述符,如果fd为-1, 表示内核不再监控
	       short events; //输入参数, 表示告诉内核要监控的事件, 读事件, 写事件, 异常事件  
	       short revents;//输出参数, 表示内核告诉应用程序有哪些文件描述符有事件发生    
	   };
	   events/revents:
	   	POLLIN:可读事件
	   	POLLOUT: 可写事件
	   	POLLERR: 异常事件
	nfds: 告诉内核监控的范围, 具体是: 数组下标的最大值+1 
	timeout: 
		=0: 不阻塞, 立刻返回
		-1: 表示一直阻塞, 直到有事件发生
		>0: 表示阻塞时长, 在时长范围内若有事件发生会立刻返回;
			如果超过了时长也会立刻返回
函数返回值:
	>0: 发生变化的文件描述符的个数
	=0: 没有文件描述符发生变化
	-1: 表示异常
```

### 2.1 poll开发服务端流程

```c
使用poll模型开发服务端流程:
{
1 创建socket, 得到监听文件描述符lfd----socket()
2 设置端口复用----setsockopt()
3 绑定----bind()
4 监听----listen()
5 struct pollfd client[1024];
  client[0].fd = lfd;
  client[0].events = POLLIN;
  
  int maxi = 0;
  for(i=1; i<1024; i++)
  {
  	client[i].fd = -1;
  }
  
  while(1)
  {
  	nready = poll(client, maxi+1, -1);
  	//异常情况
  	if(nready<0)
  	{
  		if(errno==EINTR)  // 被信号中断
  		{
  			continue;
  		}
  		break;
  	}
  	
  	//有客户端连接请求到来
  	if(client[0].revents==POLLIN)
  	{
  		//接受新的客户端连接
  		cfd = accept(lfd, NULL, NULL);
  		
  		//寻找在client数组中可用位置
  		for(i=0; i<1024; i++)
  		{
  			if(client[i].fd==-1)
  			{
  				client[i].fd = cfd;
  				client[i].events = POLLIN;
  				break;
  			}
  		}
  		
  		//客户端连接数达到最大值
  		if(i==1024)
  		{
  			close(cfd);
  			continue;
  		}
  		
  		//修改client数组下标最大值
  		if(maxi<i)
  		{
  			maxi = i;
  		}
  		
  		if(--nready==0)
  		{
  			continue;
  		}
  	}
  	
  	//下面是有客户端发送数据的情况
  	for(i=1; i<=maxi; i++)
  	{
  		sockfd = client[i].fd;
  		//如果client数组中fd为-1, 表示已经不再让你内核监控了, 已经close了
  		if(client[i].fd==-1)
  		{
  			continue;
  		}
  		
  		if(client[i].revents==POLLIN)
  		{
  			//read 数据
  			n = read(sockfd, buf, sizeof(buf));
  			if(n<=0)
  			{
  				close(sockfd);
  				client[i].fd = -1;
  				
  			}
  			else 
  			{
  				//发送数据给客户端
  				write(sockfd, buf, n);
  			} 	
  			
	  		if(--nready==0)
	  		{
	  			break;
	  		}	
  		}  		
  	}  	
  }
 
  close(lfd); 
  return 0;
} 
```

代码实现

```c
//IO多路复用技术poll函数的使用 
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <errno.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <poll.h>
#include "wrap.h"

int main()
{
	int i;
	int n;
	int lfd;
	int cfd;
	int ret;
	int nready;
	int maxfd;
	char buf[1024];
	socklen_t len;
	int sockfd;
	fd_set tmpfds, rdfds;
	struct sockaddr_in svraddr, cliaddr;
	
	//创建socket
	lfd = Socket(AF_INET, SOCK_STREAM, 0);

	//允许端口复用
	int opt = 1;
	setsockopt(lfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(int));

	//绑定bind
	svraddr.sin_family = AF_INET;
	svraddr.sin_addr.s_addr = htonl(INADDR_ANY);
	svraddr.sin_port = htons(8888);
	ret = Bind(lfd, (struct sockaddr *)&svraddr, sizeof(struct sockaddr_in));

	//监听listen
	ret = Listen(lfd, 128);

	struct pollfd client[1024];
	for(i=0; i<1024; i++)
	{
		client[i].fd = -1;
	}		

	//将监听文件描述符委托给内核监控----监控读事件
	client[0].fd = lfd;
	client[0].events = POLLIN;

	maxfd = 0; //maxfd表示内核监控的范围

	while(1)
	{
		nready = poll(client, maxfd+1, -1);
		if(nready<0)
		{
			perror("poll error");
			exit(1);
		}
		
		//有客户端连接请求
		if(client[0].fd==lfd && (client[0].revents & POLLIN))
		{
			cfd = Accept(lfd, NULL, NULL);

			//寻找client数组中的可用位置
			for(i=1; i<1024; i++)
			{
				if(client[i].fd==-1)
				{
					client[i].fd = cfd;
					client[i].events = POLLIN;
					break;
				}
			}

			//若没有可用位置, 则关闭连接
			if(i==1024)
			{
				Close(cfd);
				continue;
			}

			if(maxfd<i)
			{
				maxfd = i;
			}
			
			if(--nready==0)
			{
				continue;
			}
		}

		//下面是有数据到来的情况
		for(i=1; i<=maxfd; i++)
		{
			//若fd为-1, 表示连接已经关闭或者没有连接
			if(client[i].fd==-1)	
			{
				continue;
			}
			
			sockfd = client[i].fd;
			memset(buf, 0x00, sizeof(buf));
			n = Read(sockfd, buf, sizeof(buf));
			if(n<=0)
			{
				printf("read error or client closed,n==[%d]\n", n);
				Close(sockfd);
				client[i].fd = -1; //fd为-1,表示不再让内核监控
			}
			else
			{
				printf("read over,n==[%d],buf==[%s]\n", n, buf);
				write(sockfd, buf, n);
			}

			if(--nready==0)
			{
				break;
			}
		}

	}


	Close(lfd);
	return 0;
}

```



## 3. epoll模型

```c
函数介绍:
int epoll_create(int size);
函数说明: 创建一个树根
参数说明:
	size: 最大节点数, 此参数在linux 2.6.8已被忽略, 但必须传递一个大于0的数.
返回值:
    成功: 返回一个大于0的文件描述符, 代表整个树的树根.
    失败: 返回-1, 并设置errno值.

int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
函数说明: 将要监听的节点往epoll树上添加, 删除和修改
参数说明:
    epfd: epoll树根
    op:
    EPOLL_CTL_ADD: 添加事件节点到树上
    EPOLL_CTL_DEL: 从树上删除事件节点
    EPOLL_CTL_MOD: 修改树上对应的事件节点
    fd: 事件节点对应的文件描述符
    event: 要操作的事件节点
            typedef union epoll_data {
            void        *ptr;
            int          fd;
            uint32_t     u32;
            uint64_t     u64;
        } epoll_data_t;

        struct epoll_event {
            uint32_t     events;      /* Epoll events */
                   epoll_data_t data;        /* User data variable */
               };
event.events常用的有:
 EPOLLIN: 读事件
 EPOLLOUT: 写事件
 EPOLLERR: 错误事件
                 EPOLLET: 边缘触发模式
event.fd: 要监控的事件对应的文件描述符

int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
函数说明:等待内核返回事件发生
参数说明:
epfd: epoll树根
events: 传出参数, 其实是一个事件结构体数组
maxevents: events数组大小
timeout:
-1: 表示永久阻塞
0: 立即返回
>0: 表示超时等待事件
     返回值:
成功: 返回发生事件的个数
失败: 若timeout=0, 没有事件发生则返回; 返回-1, 设置errno值, 

	epoll_wait的events是一个传出参数, 调用epoll_ctl传递给内核什么值, 当epoll_wait返回的时候, 内核就传回什么值,不会对struct event的结构体变量的值做任何修改.

```

### 3.1 epoll开发服务器流程

```c
使用epoll模型开发服务器流程:
{
1 创建socket, 得到监听文件描述符lfd----socket()
2 设置端口复用----setsockopt()
3 绑定----bind()
4 监听----listen()
5 创建一棵epoll树
  int epfd = epoll_create();
	
  //将监听文件描述符上树
  struct epoll_event ev;
  ev.evetns = EPOLLIN;  //可读事件
  ev.data.fd = lfd;
  epoll_ctl(epfd, EPOLL_CTL_ADD, lfd, &ev);
  
  struct epoll_event events[1024];
  while(1)
  {
  	nready = epoll_wait(epfd, events, 1024, -1);
  	if(nready<0)
  	{
  		if(errno==EINTR)//被信号中断
  		{
  			continue;
  		}
  		break;
  	}
  	
  	for(i=0; i<nready; i++)
  	{
  		sockfd = events[i].data.fd;
  		//有客户端连接请求到来  	
  		if(sockfd==lfd)
  		{
  			cfd = accept(lfd, NULL, NULL);
  			
  			//将cfd对应的读事件上epoll树
  			ev.data.fd = cfd;
  			ev.evetns = EPOLLIN; 
  			epoll_ctl(epfd, EPOLL_CTL_ADD, cfd, &ev);
  			continue;
  		}
  		
  		//有客户端发送数据过来
  		n = Read(sockfd, buf, sizeof(buf));
  		if(n<=0)
  		{
  			close(sockfd);
  			//将sockfd对应的事件节点从epoll树上删除
  			epoll_ctl(epfd, EPOLL_CTL_DEL, sockfd, NULL);
  			perror("read error or client closed");
  			continue;
  		}
  		else 
  		{
  			write(sockfd, buf, n);
  		}
  	} 
  }
  
  Close(epfd);
  close(lfd);
  
  return 0;
}
```

代码实现

```c
//epoll模型测试
#include <sys/epoll.h>
#include <ctype.h>
#include "wrap.h"


int main()
{
	int lfd;
	struct epoll_event ev;
	

	//创建socket
	lfd = Socket(AF_INET,SOCK_STREAM,0);
	
	//设置端口复用
	int opt=1;	
 setsockopt(lfd,SOL_SOCKET,SO_REUSEADDR,&opt,sizeof(opt));

	//绑定端口号
	struct sockaddr_in serv;
	serv.sin_family = AF_INET;
	serv.sin_port = htons(8888);
	serv.sin_addr.s_addr=htonl(INADDR_ANY);
	Bind(lfd,(struct sockaddr *)&serv,sizeof(serv));

	//监听
	Listen(lfd,128);
	
	//创建一棵epoll树
	int epfd = epoll_create(1024);
	
	if(epfd<0)
	{
		perror("create epoll error");
		return -1;
	}
	
	//把监听描述符添加到树上
	ev.events = EPOLLIN;
	ev.data.fd = lfd;
	epoll_ctl(epfd,EPOLL_CTL_ADD,lfd,&ev);

	struct epoll_event events[1024];
	int n;
	char buf[1024];
	int nready;
	
	while(1)
	{
		//等待内核返回事件发生
		nready = epoll_wait(epfd,events,1024,-1);
		if(nready<0)
		{
			perror("epoll_wait error");
			if(errno==EINTR)continue;
			break;
		}
		
		//循环访问返回数组，处理请求
		int i;
		int k;
		for(i = 0;i<nready;i++)
		{
			int sockfd = events[i].data.fd;
			//有客户端连接到来
			if(sockfd==lfd)//区分是客户端连接还是数据连接
			{
				int cfd = Accept(lfd,NULL,NULL);
				
				//将新的cfd添加到epoll树
				ev.events = EPOLLIN;
				ev.data.fd = cfd;
				epoll_ctl(epfd,EPOLL_CTL_ADD,cfd,&ev);
				continue;	
			}
			
			//有数据到来
			memset(buf,0x00,sizeof(buf));
	//		n = recv(sockfd,buf,sizeof(buf),0);
			n = Read(sockfd,buf,sizeof(buf));

			if(n<=0)
			{
				printf("n==[%d],buf==[%s]\n",n,buf);
				close(sockfd);
				//将sockfd对应的事件就节点从epoll树上删除
				epoll_ctl(epfd,EPOLL_CTL_DEL,sockfd,NULL);				
			}
			else
			{
				printf("n==[%d],buf==[%s]\n",n,buf); 
				for(k=0;k<n;k++)buf[k]=toupper(buf[k]);
			//	send(sockfd,buf,n,0);
				Write(sockfd,buf,n);
			}			
		}		
	}
	close(lfd);
	close(epfd);
	return 0;
}
```

### 3.2 epoll的两种工作模式

```c
1 epoll默认情况下是LT模式, 在这种模式下, 若读数据一次性没有读完,
  缓冲区中还有可读数据, 则epoll_wait还会再次通知
2 若将epoll设置为ET模式, 若读数据的时候一次性没有读完, 则epoll_wait不再通知,
  直到下次有新的数据发来.
      ev.events = EPOLLIN | EPOLLET;
```
```c
epoll的两种模式ET和LT模式
	水平触发: 高电平代表1
		只要缓冲区中有数据, 就一直通知
	边缘触发: 电平有变化就代表1
		缓冲区中有数据只会通知一次, 之后再有数据才会通知.(若是读数据的时候没有读完, 则剩余的数据不会再通知, 直到有新的数据到来)
	
	边缘非阻塞模式: 提高效率
用实验验证LT和ET模式
	ET模式由于只通知一次, 所以在读的时候要循环读, 直到读完, 但是当读完之后read就会阻塞, 所以应该将该文件描述符设置为非阻塞模式(fcntl函数).
	
	read函数在非阻塞模式下读的时候, 若返回-1, 且errno为EAGAIN, 则表示当前资源不可用, 也就是说缓冲区无数据(缓冲区的数据已经读完了); 或者当read返回的读到的数据长度小于请求的数据长度时，就可以确定此时缓冲区中已没有数据可读了，也就可以认为此时读事件已处理完成。

```

ET模式代码测试

```c
//EPOLL模型测试:测试ET和LT模式的区别
#include "wrap.h"
#include <sys/epoll.h>
#include <ctype.h>
#include <fcntl.h>

int main()
{
	int ret;
	int n;
	int i;
	int k;
	int nready;
	int lfd;
	int cfd;
	int sockfd;
	char buf[1024];
	socklen_t socklen;
	struct sockaddr_in svraddr;
	struct epoll_event ev;
	struct epoll_event events[1024];

	//创建socket
	lfd = Socket(AF_INET, SOCK_STREAM, 0);

	//设置文件描述符为端口复用
	int opt = 1;
	setsockopt(lfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(int));

	//绑定bind
	svraddr.sin_family = AF_INET;
	svraddr.sin_addr.s_addr = htonl(INADDR_ANY);
	svraddr.sin_port = htons(8888);
	Bind(lfd, (struct sockaddr *)&svraddr, sizeof(struct sockaddr_in));

	//监听listen
	Listen(lfd, 128);

	//创建一棵epoll树
	int epfd = epoll_create(1024);
	if(epfd<0)
	{
		perror("create epoll error");
		return -1;
	}

	//将lfd上epoll树
	ev.data.fd = lfd;
	ev.events = EPOLLIN;
	epoll_ctl(epfd, EPOLL_CTL_ADD, lfd, &ev);

	while(1)
	{
		nready = epoll_wait(epfd, events, 1024, -1);
		if(nready<0)
		{
			perror("epoll_wait error");
			if(errno==EINTR)
			{
				continue;
			}
			break;			
		}

		for(i=0; i<nready; i++)
		{
			//有客户端连接请求
			sockfd = events[i].data.fd;
			if(sockfd==lfd)
			{
				cfd = Accept(lfd, NULL, NULL);
				//将新的cfd上epoll树
				ev.data.fd = cfd;
                //设置新连接为边缘触发
				ev.events = EPOLLIN | EPOLLET;
				epoll_ctl(epfd, EPOLL_CTL_ADD, cfd, &ev);

				//将cfd设置为非阻塞
				int flag = fcntl(cfd, F_GETFL);
				flag |= O_NONBLOCK;
				fcntl(cfd, F_SETFL, flag);

				continue;
			}

			//有客户端发送数据过来
			memset(buf, 0x00, sizeof(buf));
			while(1)
			{
				n = Read(sockfd, buf, 2);
				printf("n==[%d]\n", n);
				//读完数据的情况
				if(n==-1)
				{
					printf("read over, n==[%d]\n", n);
					break;
				}
				//对方关闭连接或者读异常
				if(n==0 || (n<0&&n!=-1))
				{
					printf("n==[%d], buf==[%s]\n", n, buf);
					close(sockfd);
					//将sockfd对应的事件就节点从epoll树上删除
					epoll_ctl(epfd, EPOLL_CTL_DEL, sockfd, NULL);
					break;

				}
				else //正常读到数据的情况
				{
					printf("n==[%d], buf==[%s]\n", n, buf);
					for(k=0; k<n; k++)
					{
						buf[k] = toupper(buf[k]);
					}
					Write(sockfd, buf, n);
				}
			}
		}
	}

	close(epfd);
	close(lfd);
	return 0;
}

```

思考:
	1 在ET模式下, 如何在epoll_wait返回一次的情况下读完数据?
		循环读数据, 直到读完数据, 但是读完数据之后会阻塞.
	2 若能够一次性读完还需要设置什么? 
		将通信文件描述符设置为非阻塞模式，防止read阻塞.



## 4. epoll反应堆思想

![image-20210827150908469](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210827150908469.png)

​	

```c
	epoll反应堆的核心思想是: 在调用epoll_ctl函数的时候, 将events上树的时候,利用epoll_data_t的ptr成员, 将一个文件描述符,事件和回调函数封装成一个结构体, 然后让ptr指向这个结构体, 然后调用epoll_wait函数返回的时候, 可以得到具体的events, 然后获得events结构体中的events.data.ptr指针, ptr指针指向的结构体中有回调函数, 最终可以调用这个回调函数
```

### 4.1 epoll反应堆代码分析

![image-20210827163349776](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210827163349776.png)



![image-20210827163636155](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210827163636155.png)



```c
//反应堆简单版
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <fcntl.h>
#include <string.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <sys/epoll.h>
#include "wrap.h"

#define _BUF_LEN_  1024
#define _EVENT_SIZE_ 1024

//全局epoll树的根
int gepfd = 0;

//事件驱动结构体
typedef struct xx_event{
    int fd;
    int events;
    void (*call_back)(int fd,int events,void *arg);
    void *arg;
    char buf[1024];
    int buflen;
    int epfd;
}xevent;

xevent myevents[_EVENT_SIZE_+1];

void readData(int fd,int events,void *arg);

//添加事件
//eventadd(lfd,EPOLLIN,initAccept,&myevents[_EVENT_SIZE_-1],&myevents[_EVENT_SIZE_-1]);
void eventadd(int fd,int events,void (*call_back)(int ,int ,void *),void *arg,xevent *ev)
{
    ev->fd = fd;
    ev->events = events;
    //ev->arg = arg;//代表结构体自己,可以通过arg得到结构体的所有信息
    ev->call_back = call_back;

    struct epoll_event epv;
    epv.events = events;
    epv.data.ptr = ev;//核心思想
    epoll_ctl(gepfd,EPOLL_CTL_ADD,fd,&epv);//上树
}

//修改事件
//eventset(fd,EPOLLOUT,senddata,arg,ev);
void eventset(int fd,int events,void (*call_back)(int ,int ,void *),void *arg,xevent *ev)
{
    ev->fd = fd;
    ev->events = events;
    //ev->arg = arg;
    ev->call_back = call_back;

    struct epoll_event epv;
    epv.events = events;
    epv.data.ptr = ev;
    epoll_ctl(gepfd,EPOLL_CTL_MOD,fd,&epv);//修改
}

//删除事件
void eventdel(xevent *ev,int fd,int events)
{
	printf("begin call %s\n",__FUNCTION__);

    ev->fd = 0;
    ev->events = 0;
    ev->call_back = NULL;
    memset(ev->buf,0x00,sizeof(ev->buf));
    ev->buflen = 0;

    struct epoll_event epv;
    epv.data.ptr = NULL;
    epv.events = events;
    epoll_ctl(gepfd,EPOLL_CTL_DEL,fd,&epv);//下树
}

//发送数据
void senddata(int fd,int events,void *arg)
{
    printf("begin call %s\n",__FUNCTION__);

    xevent *ev = arg;
    Write(fd,ev->buf,ev->buflen);
    eventset(fd,EPOLLIN,readData,arg,ev);
}

//读数据
void readData(int fd,int events,void *arg)
{
    printf("begin call %s\n",__FUNCTION__);
    xevent *ev = arg;

    ev->buflen = Read(fd,ev->buf,sizeof(ev->buf));
    if(ev->buflen>0) //读到数据
	{	
		//void eventset(int fd,int events,void (*call_back)(int ,int ,void *),void *arg,xevent *ev)
        eventset(fd,EPOLLOUT,senddata,arg,ev);

    }
	else if(ev->buflen==0) //对方关闭连接
	{
        Close(fd);
        eventdel(ev,fd,EPOLLIN);
    }

}
//新连接处理
void initAccept(int fd,int events,void *arg)
{
    printf("begin call %s,gepfd =%d\n",__FUNCTION__,gepfd);//__FUNCTION__ 函数名

    int i;
    struct sockaddr_in addr;
    socklen_t len = sizeof(addr);
    int cfd = Accept(fd,(struct sockaddr*)&addr,&len);//是否会阻塞？
	
	//查找myevents数组中可用的位置
    for(i = 0 ; i < _EVENT_SIZE_; i ++)
	{
        if(myevents[i].fd==0)
		{
            break;
        }
    }

    //设置读事件
    eventadd(cfd,EPOLLIN,readData,&myevents[i],&myevents[i]);
}

int main(int argc,char *argv[])
{
	//创建socket
    int lfd = Socket(AF_INET,SOCK_STREAM,0);

    //端口复用
    int opt = 1;
    setsockopt(lfd,SOL_SOCKET,SO_REUSEADDR,&opt,sizeof(opt));

	//绑定
    struct sockaddr_in servaddr;
    servaddr.sin_family = AF_INET;
    servaddr.sin_port = htons(8888);
    servaddr.sin_addr.s_addr = htonl(INADDR_ANY);
    Bind(lfd,(struct sockaddr*)&servaddr,sizeof(servaddr));
    
	//监听
    Listen(lfd,128);

	//创建epoll树根节点
    gepfd = epoll_create(1024);
    printf("gepfd === %d\n",gepfd);

    struct epoll_event events[1024];

    //添加最初始事件，将侦听的描述符上树
    eventadd(lfd,EPOLLIN,initAccept,&myevents[_EVENT_SIZE_],&myevents[_EVENT_SIZE_]);
    //void eventadd(int fd,int events,void (*call_back)(int ,int ,void *),void *arg,xevent *ev)

    while(1)
	{
        int nready = epoll_wait(gepfd,events,1024,-1);
		if(nready<0) //调用epoll_wait失败
		{
			perr_exit("epoll_wait error");
			
		}
        else if(nready>0) //调用epoll_wait成功,返回有事件发生的文件描述符的个数
		{
            int i = 0;
            for(i=0;i<nready; i++)
			{
                xevent *xe = events[i].data.ptr;//取ptr指向结构体地址
                printf("fd=%d\n",xe->fd);

                if(xe->events & events[i].events)
				{
                    xe->call_back(xe->fd,xe->events,xe);//调用事件对应的回调
                }
            }
        }
    }

	//关闭监听文件描述符
	Close(lfd);

    return 0;
}

```



![image-20210827212521703](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210827212521703.png)

```c
// epoll基于非阻塞I/O事件驱动
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <sys/epoll.h>
#include <fcntl.h>
#include <errno.h>
#include <time.h>
#include <ctype.h>
#include "wrap.h"

#define MAX_EVENTS  1024                                    //监听上限数
#define BUFLEN      4096

void recvdata(int fd, int events, void *arg);
void senddata(int fd, int events, void *arg);

/* 描述就绪文件描述符相关信息 */
struct myevent_s {
    int fd;                                                 //要监听的文件描述符
    int events;                                             //对应的监听事件
    void *arg;                                              //泛型参数
    void (*call_back)(int fd, int events, void *arg);       //回调函数
    int status;                                             //是否在监听:1->在红黑树上(监听), 0->不在(不监听)
    char buf[BUFLEN];
    int len;
    long last_active;                                       //记录每次加入红黑树 g_efd 的时间值
};

int g_efd;                                                  //全局变量, 保存epoll_create返回的文件描述符
int g_lfd;													//全局变量, 保存监听的文件描述符
struct myevent_s g_events[MAX_EVENTS+1];                    //自定义结构体类型数组. +1-->listen fd


/*将结构体 myevent_s 成员变量 初始化*/
void eventset(struct myevent_s *ev, int fd, void (*call_back)(int, int, void *), void *arg)
{
    ev->fd = fd;
    ev->call_back = call_back;
    ev->events = 0;
    ev->arg = arg;
    ev->status = 0;
    //memset(ev->buf, 0, sizeof(ev->buf));
    //ev->len = 0;
    ev->last_active = time(NULL);    //调用eventset函数的时间 unix时间戳

    return;
}

/* 向 epoll监听的红黑树 添加一个 文件描述符 */
void eventadd(int efd, int events, struct myevent_s *ev)
{
    struct epoll_event epv = {0, {0}};
    int op;
    epv.data.ptr = ev;
    epv.events = ev->events = events;       //EPOLLIN 或 EPOLLOUT

    if(ev->status == 1) 
	{                    
        //已经在红黑树 g_efd 里
        op = EPOLL_CTL_MOD;//修改其属性
    } 
	else 
	{                        
        //不在红黑树里
        op = EPOLL_CTL_ADD;  
        //将其加入红黑树 g_efd, 并将status置1
        ev->status = 1;
    }

    if (epoll_ctl(efd, op, ev->fd, &epv) < 0)           //实际添加/修改
	{
        printf("event add failed [fd=%d], events[%d]\n", ev->fd, events);
	}
    else
	{
        printf("event add OK [fd=%d], op=%d, events[%0X]\n", ev->fd, op, events);
	}

    return ;
}

/* 从epoll 监听的 红黑树中删除一个 文件描述符*/

void eventdel(int efd, struct myevent_s *ev)
{
    struct epoll_event epv = {0, {0}};

    if (ev->status != 1)                                        //不在红黑树上
        return ;

    epv.data.ptr = ev;
    ev->status = 0;                                             //修改状态
    epoll_ctl(efd, EPOLL_CTL_DEL, ev->fd, &epv);                //从红黑树 efd 上将 ev->fd 摘除

    return ;
}

/*  当有文件描述符就绪, epoll返回, 调用该函数 与客户端建立链接 */
// 回调函数 - 监听的文件描述符发送读事件时被调用
void acceptconn(int lfd, int events, void *arg)
{
    struct sockaddr_in cin;
    socklen_t len = sizeof(cin);
    int cfd, i;

    cfd = Accept(lfd, (struct sockaddr *)&cin, &len);

	//使用do while(0)的目的是为了避免使用goto语句
    do 
	{
        for (i = 0; i < MAX_EVENTS; i++)                                //从全局数组g_events中找一个空闲元素
		{
            if (g_events[i].status == 0)                                //类似于select中找值为-1的元素
			{
                break;  //找到第一个能用的                                                //跳出 for
			}
		}

        if (i == MAX_EVENTS) 
		{
            printf("%s: max connect limit[%d]\n", __func__, MAX_EVENTS);
            break;   //避免goto, 跳出do while(0) 不执行后续代码
        }

		//将cfd设置为非阻塞
        int flags = 0;
		flags = fcntl(cfd, F_GETFL, 0);
		flags |= O_NONBLOCK;
        if ((flags = fcntl(cfd, F_SETFL, flags)) < 0) 
		{
            printf("%s: fcntl nonblocking failed, %s\n", __func__, strerror(errno));
            break;//避免goto
        }

        /* 给cfd设置一个 myevent_s 结构体, 回调函数 设置为 recvdata */
        eventset(&g_events[i], cfd, recvdata, &g_events[i]);   

		//将cfd添加到红黑树g_efd中,监听读事件
        eventadd(g_efd, EPOLLIN, &g_events[i]); 

    }while(0);

    printf("new connect [%s:%d][time:%ld], pos[%d]\n", 
            inet_ntoa(cin.sin_addr), ntohs(cin.sin_port), g_events[i].last_active, i);
    return ;
}

// 回调函数 - 通信的文件描述符发生读事件时候被调用
void recvdata(int fd, int events, void *arg)
{
    int len;
    struct myevent_s *ev = (struct myevent_s *)arg;

	//读取客户端发来的数据
	memset(ev->buf, 0x00, sizeof(ev->buf));
    len = Read(fd, ev->buf, sizeof(ev->buf));//读文件描述符, 数据存入myevent_s成员buf中

    eventdel(g_efd, ev); //将该节点从红黑树上摘除

    if (len > 0) 	
	{
        ev->len = len;
        ev->buf[len] = '\0';                                //手动添加字符串结束标记
        printf("C[%d]:%s\n", fd, ev->buf);

        eventset(ev, fd, senddata, ev);                     //设置该 fd 对应的回调函数为 senddata
        eventadd(g_efd, EPOLLOUT, ev);                      //将fd加入红黑树g_efd中,监听其写事件
    } 
	else if (len == 0) 
	{
        Close(ev->fd);
        /* ev-g_events 地址相减得到偏移元素位置 */
        printf("[fd=%d] pos[%ld], closed\n", fd, ev-g_events);
    } 
	else 
	{
        Close(ev->fd);
        printf("read [fd=%d] error[%d]:%s\n", fd, errno, strerror(errno));
    }

    return;
}

// 回调函数 - 通信的文件描述符发生写事件时候被调用
void senddata(int fd, int events, void *arg)
{
    int len;
    struct myevent_s *ev = (struct myevent_s *)arg;

	//将小写转换为大写发送给客户端
	int i=0;
	for(i=0; i<ev->len; i++)
	{
		ev->buf[i] = toupper(ev->buf[i]);
	}

	//发送数据给客户端
    len = Write(fd, ev->buf, ev->len);
    if (len > 0) 
	{
        printf("send[fd=%d]-->[%d]:[%s]\n", fd, len, ev->buf);
        eventdel(g_efd, ev);                                //从红黑树g_efd中移除
        eventset(ev, fd, recvdata, ev);                     //将该fd的 回调函数改为 recvdata
        eventadd(g_efd, EPOLLIN, ev);                       //从新添加到红黑树上， 设为监听读事件
    } 
	else 
	{
        Close(ev->fd);                                      //关闭链接
        eventdel(g_efd, ev);                                //从红黑树g_efd中移除
        printf("send[fd=%d] error %s\n", fd, strerror(errno));
    }

    return;
}

/*创建 socket, 初始化lfd */

void initlistensocket()
{
	//创建socket
    g_lfd = Socket(AF_INET, SOCK_STREAM, 0);

	//对事件结构体赋值
    /* void eventset(struct myevent_s *ev, int fd, void (*call_back)(int, int, void *), void *arg);  */
    eventset(&g_events[MAX_EVENTS], g_lfd, acceptconn, &g_events[MAX_EVENTS]);//仅仅是对g_events[MAX_EVENTS]进行设置

	//将监听文件描述符上树
    eventadd(g_efd, EPOLLIN, &g_events[MAX_EVENTS]);

	//绑定
    struct sockaddr_in servaddr;
	memset(&servaddr, 0, sizeof(servaddr));
	servaddr.sin_family = AF_INET;
	servaddr.sin_addr.s_addr = htonl(INADDR_ANY);
	servaddr.sin_port = htons(8888);
	Bind(g_lfd, (struct sockaddr *)&servaddr, sizeof(servaddr));

	Listen(g_lfd, 20);

    return;
}

int main(int argc, char *argv[])
{
    g_efd = epoll_create(MAX_EVENTS+1);                 //创建红黑树,返回给全局 g_efd 
	if(g_efd<0)
	{
		perror("create epoll error");
		return -1;
	}

	//socket-bind-listen-将监听文件描述符上树
    initlistensocket();

    struct epoll_event events[MAX_EVENTS+1];            //保存已经满足就绪事件的文件描述符数组 

    int checkpos = 0, i;
    while (1) 
	{
        /* 超时验证，每次测试100个链接，不测试listenfd 当客户端60秒内没有和服务器通信，则关闭此客户端链接 */
        long now = time(NULL);                          //当前时间
		//一次循环检测100个。 使用checkpos控制检测对象
        for (i = 0; i < 100; i++, checkpos++) 
		{
            if (checkpos == MAX_EVENTS)
			{
                checkpos = 0;
			}

            if (g_events[checkpos].status != 1)         //不在红黑树 g_efd 上
			{
                continue;
			}

            long duration = now - g_events[checkpos].last_active;       //客户端不活跃的世间

            if (duration >= 60) 
			{
                Close(g_events[checkpos].fd);                           //关闭与该客户端链接
                printf("[fd=%d] timeout\n", g_events[checkpos].fd);
                eventdel(g_efd, &g_events[checkpos]);                   //将该客户端 从红黑树 g_efd移除
            }
        }

        /*监听红黑树g_efd, 将满足的事件的文件描述符加至events数组中, 1秒没有事件满足, 返回 0*/
        int nfd = epoll_wait(g_efd, events, MAX_EVENTS+1, 1000);
        if (nfd < 0) 
		{
            printf("epoll_wait error, exit\n");
            break;
        }

        for (i = 0; i < nfd; i++) 
		{
            /*使用自定义结构体myevent_s类型指针,接收联合体data的void *ptr成员*/
            struct myevent_s *ev = (struct myevent_s *)events[i].data.ptr;  

			//读就绪事件
            if ((events[i].events & EPOLLIN) && (ev->events & EPOLLIN)) 
			{
                //ev->call_back(ev->fd, events[i].events, ev->arg);
                ev->call_back(ev->fd, events[i].events, ev);
            }
			//写就绪事件
            if ((events[i].events & EPOLLOUT) && (ev->events & EPOLLOUT))
			{
                //ev->call_back(ev->fd, events[i].events, ev->arg);
                ev->call_back(ev->fd, events[i].events, ev);
            }
        }
    }

    /*关闭文件描述符 */
	Close(g_efd);
	Close(g_lfd);

    return 0;
}

```

# 六、线程池

## 1.基本概念

```c
1. 什么是线程池? 
是一个抽象的概念, 若干个线程组合到一起, 形成线程池.

2. 为什么需要线程池? 
	多线程版服务器一个客户端就需要创建一个线程! 若客户端太多, 显然不太合适.

3.  什么时候需要创建线程池呢？
    简单的说，如果一个应用需要频繁的创建和销毁线程，而任务执行的时间又非常短，这样线程创建和销毁的带来的开销就不容忽视，这时也是线程池该出场的机会了。如果线程创建和销毁时间相比任务执行时间可以忽略不计，则没有必要使用线程池了。
	实现的时候类似于生产者和消费者.

线程池和任务池:
	任务池相当于共享资源, 所以需要使用互斥锁, 当任务池中没有任务的时候需要让线程阻塞, 所以需要使用条件变量.

如何让线程执行不同的任务?
	使用回调函数, 在任务中设置任务执行函数, 这样可以起到不同的任务执行不同的函数.

```

```c
	熟悉结构体 threadpool_t
	线程池如何创建起来? 各种初始化，malloc，pthread_create，pthread_cond_init  pthread_mutex_init
    
	线程池内都有几类线程? 
    2类：管理线程+工作线程
    
	管理者线程的任务是什么?任务如何实现? 
    任务是添加线程或者删除线程，通过2个算法，删除线程 wait_exit_thr_num = 10 
    
	工作线程如何工作? 
    等待有任务，抢到任务，修改busy_thr_num ++ 执行任务 修改 busy_thr_num --
    
	线程池是如何销毁的?
    自爆shutdown 诱杀！

```



## 2.思想和代码讲解

思想：

![image-20210829161155334](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210829161155334.png)

![image-20210829161311192](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210829161311192.png)



### 2.1 简单版本线程池

![image-20210829161356861](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210829161356861.png)



头文件代码：

```c
#ifndef _THREADPOOL_H
#define _THREADPOOL_H

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <pthread.h>


typedef struct _PoolTask
{
    int tasknum;//模拟任务编号
    void *arg;//回调函数参数
    void (*task_func)(void *arg);//任务的回调函数
}PoolTask ;

typedef struct _ThreadPool
{
    int max_job_num;//最大任务个数
    int job_num;//实际任务个数
    PoolTask *tasks;//任务队列数组
    int job_push;//入队位置
    int job_pop;// 出队位置

    int thr_num;//线程池内线程个数
    pthread_t *threads;//线程池内线程数组
    int shutdown;//是否关闭线程池
    pthread_mutex_t pool_lock;//线程池的锁
    pthread_cond_t empty_task;//任务队列为空的条件
    pthread_cond_t not_empty_task;//任务队列不为空的条件

}ThreadPool;

void create_threadpool(int thrnum,int maxtasknum);//创建线程池--thrnum  代表线程个数，maxtasknum 最大任务个数
void destroy_threadpool(ThreadPool *pool);//摧毁线程池
void addtask(ThreadPool *pool);//添加任务到线程池
void taskRun(void *arg);//任务回调函数

#endif

```





简单版本线程池：

![image-20210829221341927](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210829221341927.png)



```c
//简易版线程池
#include "threadpoolsimple.h"

ThreadPool *thrPool = NULL;

int beginnum = 1000;

void *thrRun(void *arg)  //线程回调函数
{
    //printf("begin call %s-----\n",__FUNCTION__);
    ThreadPool *pool = (ThreadPool*)arg;
    int taskpos = 0;//任务位置
    PoolTask *task = (PoolTask *)malloc(sizeof(PoolTask));

    while(1)
	{
        //获取任务，先要尝试加锁
        pthread_mutex_lock(&thrPool->pool_lock);

		//无任务并且线程池不是要摧毁
        while(thrPool->job_num <= 0 && !thrPool->shutdown )
		{
			//如果没有任务，线程会阻塞
            pthread_cond_wait(&thrPool->not_empty_task,&thrPool->pool_lock);//隐藏操作，当函数条件满足时，会自动加锁或解锁
        }
        
        if(thrPool->job_num)
		{
            //有任务需要处理
            taskpos = (thrPool->job_pop++)%thrPool->max_job_num;
            //printf("task out %d...tasknum===%d tid=%lu\n",taskpos,thrPool->tasks[taskpos].tasknum,pthread_self());
			//为什么要拷贝？避免任务被修改，生产者会添加任务
            memcpy(task,&thrPool->tasks[taskpos],sizeof(PoolTask));
            task->arg = task;
            thrPool->job_num--;
            //task = &thrPool->tasks[taskpos];
            pthread_cond_signal(&thrPool->empty_task);//通知生产者
        }

        if(thrPool->shutdown)
		{
            //代表要摧毁线程池，此时线程退出即可
            //pthread_detach(pthread_self());//临死前分家
            pthread_mutex_unlock(&thrPool->pool_lock);
            free(task);
			pthread_exit(NULL);
        }

        //释放锁
        pthread_mutex_unlock(&thrPool->pool_lock);
        task->task_func(task->arg);//执行回调函数
    }
    
    //printf("end call %s-----\n",__FUNCTION__);
}

//创建线程池
void create_threadpool(int thrnum,int maxtasknum)
{
    printf("begin call %s-----\n",__FUNCTION__);
    thrPool = (ThreadPool*)malloc(sizeof(ThreadPool));

    thrPool->thr_num = thrnum;
    thrPool->max_job_num = maxtasknum;
    thrPool->shutdown = 0;//是否摧毁线程池，1代表摧毁
    thrPool->job_push = 0;//任务队列添加的位置
    thrPool->job_pop = 0;//任务队列出队的位置
    thrPool->job_num = 0;//初始化的任务个数为0

    thrPool->tasks = (PoolTask*)malloc((sizeof(PoolTask)*maxtasknum));//申请最大的任务队列

    //初始化锁和条件变量
    pthread_mutex_init(&thrPool->pool_lock,NULL);
    pthread_cond_init(&thrPool->empty_task,NULL);
    pthread_cond_init(&thrPool->not_empty_task,NULL);

    int i = 0;
    thrPool->threads = (pthread_t *)malloc(sizeof(pthread_t)*thrnum);//申请n个线程id的空间
	
	pthread_attr_t attr;
	pthread_attr_init(&attr);
	pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);
    for(i = 0;i < thrnum;i++)
	{
        pthread_create(&thrPool->threads[i],&attr,thrRun,(void*)thrPool);//创建多个线程
    }
    //printf("end call %s-----\n",__FUNCTION__);
}
//摧毁线程池
void destroy_threadpool(ThreadPool *pool)
{
    pool->shutdown = 1;//开始自爆
    pthread_cond_broadcast(&pool->not_empty_task);//唤醒因not_empty_task阻塞的线程，让其执行自己内部的回收函数

    //如果没有设置线程分离属性则需要使用pthread_join回收
    /*int i = 0;
    for(i = 0; i < pool->thr_num ; i++)
	{
        pthread_join(pool->threads[i],NULL);
    }*/ 

    pthread_cond_destroy(&pool->not_empty_task);
    pthread_cond_destroy(&pool->empty_task);
    pthread_mutex_destroy(&pool->pool_lock);

    free(pool->tasks);
    free(pool->threads);
    free(pool);
}

//添加任务到线程池
void addtask(ThreadPool *pool)
{
    //printf("begin call %s-----\n",__FUNCTION__);
    pthread_mutex_lock(&pool->pool_lock);//加锁

	//实际任务总数大于最大任务个数则阻塞等待(等待任务被处理)
    while(pool->max_job_num <= pool->job_num)
	{
        pthread_cond_wait(&pool->empty_task,&pool->pool_lock);
    }

    int taskpos = (pool->job_push++)%pool->max_job_num;
    //printf("add task %d  tasknum===%d\n",taskpos,beginnum);
    pool->tasks[taskpos].tasknum = beginnum++;
    pool->tasks[taskpos].arg = (void*)&pool->tasks[taskpos];
    pool->tasks[taskpos].task_func = taskRun;
    pool->job_num++;

    pthread_mutex_unlock(&pool->pool_lock);

    pthread_cond_signal(&pool->not_empty_task);//通知包身工
    //printf("end call %s-----\n",__FUNCTION__);
}

//任务回调函数
void taskRun(void *arg)
{
    PoolTask *task = (PoolTask*)arg;
    int num = task->tasknum;
    printf("task %d is runing %lu\n",num,pthread_self());

    sleep(1);
    printf("task %d is done %lu\n",num,pthread_self());
}


int main()
{
    create_threadpool(3,20);
    int i = 0;
    for(i = 0;i < 50 ; i++)
	{
        addtask(thrPool);//模拟添加任务
    }

    sleep(20);
    destroy_threadpool(thrPool);

    return 0;
}

```



### 2.2 复杂版本线程池

头文件：

```c
#ifndef __THREADPOOL_H_
#define __THREADPOOL_H_

typedef struct threadpool_t threadpool_t;

/**
 * @function threadpool_create
 * @descCreates a threadpool_t object.
 * @param thr_num  thread num
 * @param max_thr_num  max thread size
 * @param queue_max_size   size of the queue.
 * @return a newly created thread pool or NULL
 */
threadpool_t *threadpool_create(int min_thr_num, int max_thr_num, int queue_max_size);

/**
 * @function threadpool_add
 * @desc add a new task in the queue of a thread pool
 * @param pool     Thread pool to which add the task.
 * @param function Pointer to the function that will perform the task.
 * @param argument Argument to be passed to the function.
 * @return 0 if all goes well,else -1
 */
int threadpool_add(threadpool_t *pool, void*(*function)(void *arg), void *arg);

/**
 * @function threadpool_destroy
 * @desc Stops and destroys a thread pool.
 * @param pool  Thread pool to destroy.
 * @return 0 if destory success else -1
 */
int threadpool_destroy(threadpool_t *pool);

/**
 * @desc get the thread num
 * @pool pool threadpool
 * @return # of the thread
 */
int threadpool_all_threadnum(threadpool_t *pool);

/**
 * desc get the busy thread num
 * @param pool threadpool
 * return # of the busy thread
 */
int threadpool_busy_threadnum(threadpool_t *pool);

#endif

```



具体实现：

```c
#include <stdlib.h>
#include <pthread.h>
#include <unistd.h>
#include <assert.h>
#include <stdio.h>
#include <string.h>
#include <signal.h>
#include <errno.h>
#include "threadpool.h"

#define DEFAULT_TIME 10                 /*10s检测一次*/
#define MIN_WAIT_TASK_NUM 10            /*如果queue_size > MIN_WAIT_TASK_NUM 添加新的线程到线程池*/ 
#define DEFAULT_THREAD_VARY 10          /*每次创建和销毁线程的个数*/
#define true 1
#define false 0

typedef struct 
{
    void *(*function)(void *);          /* 函数指针，回调函数 */
    void *arg;                          /* 上面函数的参数 */
} threadpool_task_t;                    /* 各子线程任务结构体 */

/* 描述线程池相关信息 */
struct threadpool_t 
{
    pthread_mutex_t lock;               /* 用于锁住本结构体 */    
    pthread_mutex_t thread_counter;     /* 记录忙状态线程个数de琐 -- busy_thr_num */

    pthread_cond_t queue_not_full;      /* 当任务队列满时，添加任务的线程阻塞，等待此条件变量 */
    pthread_cond_t queue_not_empty;     /* 任务队列里不为空时，通知等待任务的线程 */

    pthread_t *threads;                 /* 存放线程池中每个线程的tid。数组 */
    pthread_t adjust_tid;               /* 存管理线程tid */
    threadpool_task_t *task_queue;      /* 任务队列(数组首地址) */

    int min_thr_num;                    /* 线程池最小线程数 */
    int max_thr_num;                    /* 线程池最大线程数 */
    int live_thr_num;                   /* 当前存活线程个数 */
    int busy_thr_num;                   /* 忙状态线程个数 */
    int wait_exit_thr_num;              /* 要销毁的线程个数 */

    int queue_front;                    /* task_queue队头下标 */
    int queue_rear;                     /* task_queue队尾下标 */
    int queue_size;                     /* task_queue队中实际任务数 */
    int queue_max_size;                 /* task_queue队列可容纳任务数上限 */

    int shutdown;                       /* 标志位，线程池使用状态，true或false */
};

void *threadpool_thread(void *threadpool);

void *adjust_thread(void *threadpool);

int is_thread_alive(pthread_t tid);
int threadpool_free(threadpool_t *pool);

//threadpool_create(3,100,100);  
threadpool_t *threadpool_create(int min_thr_num, int max_thr_num, int queue_max_size)
{
    int i;
    threadpool_t *pool = NULL;
    do 
	{
        if((pool = (threadpool_t *)malloc(sizeof(threadpool_t))) == NULL) 
		{  
            printf("malloc threadpool fail");
            break;                                      /*跳出do while*/
        }

        pool->min_thr_num = min_thr_num;
        pool->max_thr_num = max_thr_num;
        pool->busy_thr_num = 0;
        pool->live_thr_num = min_thr_num;               /* 活着的线程数 初值=最小线程数 */
        pool->wait_exit_thr_num = 0;
        pool->queue_size = 0;                           /* 有0个产品 */
        pool->queue_max_size = queue_max_size;
        pool->queue_front = 0;
        pool->queue_rear = 0;
        pool->shutdown = false;                         /* 不关闭线程池 */

        /* 根据最大线程上限数， 给工作线程数组开辟空间, 并清零 */
        pool->threads = (pthread_t *)malloc(sizeof(pthread_t)*max_thr_num); 
        if (pool->threads == NULL) 
		{
            printf("malloc threads fail");
            break;
        }
        memset(pool->threads, 0, sizeof(pthread_t)*max_thr_num);

        /* 队列开辟空间 */
        pool->task_queue = (threadpool_task_t *)malloc(sizeof(threadpool_task_t)*queue_max_size);
        if (pool->task_queue == NULL) 
		{
            printf("malloc task_queue fail\n");
            break;
        }

        /* 初始化互斥琐、条件变量 */
        if (pthread_mutex_init(&(pool->lock), NULL) != 0
                || pthread_mutex_init(&(pool->thread_counter), NULL) != 0
                || pthread_cond_init(&(pool->queue_not_empty), NULL) != 0
                || pthread_cond_init(&(pool->queue_not_full), NULL) != 0)
        {
            printf("init the lock or cond fail\n");
            break;
        }

		//启动工作线程
		pthread_attr_t attr;
		pthread_attr_init(&attr);
		pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);
        for (i = 0; i < min_thr_num; i++) 
		{
            pthread_create(&(pool->threads[i]), &attr, threadpool_thread, (void *)pool);/*pool指向当前线程池*/
            printf("start thread 0x%x...\n", (unsigned int)pool->threads[i]);
        }

		//创建管理者线程
        pthread_create(&(pool->adjust_tid), &attr, adjust_thread, (void *)pool);

        return pool;

    } while (0);

	/* 前面代码调用失败时,释放poll存储空间 */
    threadpool_free(pool);

    return NULL;
}

/* 向线程池中 添加一个任务 */
//threadpool_add(thp, process, (void*)&num[i]);   /* 向线程池中添加任务 process: 小写---->大写*/

int threadpool_add(threadpool_t *pool, void*(*function)(void *arg), void *arg)
{
    pthread_mutex_lock(&(pool->lock));

    /* ==为真，队列已经满， 调wait阻塞 */
    while ((pool->queue_size == pool->queue_max_size) && (!pool->shutdown)) 
	{
        pthread_cond_wait(&(pool->queue_not_full), &(pool->lock));
    }

    if (pool->shutdown) 
	{
        pthread_cond_broadcast(&(pool->queue_not_empty));
        pthread_mutex_unlock(&(pool->lock));
        return 0;
    }

    /* 清空 工作线程 调用的回调函数 的参数arg */
    if (pool->task_queue[pool->queue_rear].arg != NULL) 
	{
        pool->task_queue[pool->queue_rear].arg = NULL;
    }

    /*添加任务到任务队列里*/
    pool->task_queue[pool->queue_rear].function = function;
    pool->task_queue[pool->queue_rear].arg = arg;
    pool->queue_rear = (pool->queue_rear + 1) % pool->queue_max_size;       /* 队尾指针移动, 模拟环形 */
    pool->queue_size++;

    /*添加完任务后，队列不为空，唤醒线程池中 等待处理任务的线程*/
    pthread_cond_signal(&(pool->queue_not_empty));
    pthread_mutex_unlock(&(pool->lock));

    return 0;
}

/* 线程池中各个工作线程 */
void *threadpool_thread(void *threadpool)
{
    threadpool_t *pool = (threadpool_t *)threadpool;
    threadpool_task_t task;

    while (true) 
	{
        /* Lock must be taken to wait on conditional variable */
        /*刚创建出线程，等待任务队列里有任务，否则阻塞等待任务队列里有任务后再唤醒接收任务*/
        pthread_mutex_lock(&(pool->lock));

        /*queue_size == 0 说明没有任务，调 wait 阻塞在条件变量上, 若有任务，跳过该while*/
        while ((pool->queue_size == 0) && (!pool->shutdown)) 
		{  
            printf("thread 0x%x is waiting\n", (unsigned int)pthread_self());
            pthread_cond_wait(&(pool->queue_not_empty), &(pool->lock));//暂停到这

            /*清除指定数目的空闲线程，如果要结束的线程个数大于0，结束线程*/
            if (pool->wait_exit_thr_num > 0) 
			{
                pool->wait_exit_thr_num--;

                /*如果线程池里线程个数大于最小值时可以结束当前线程*/
                if (pool->live_thr_num > pool->min_thr_num) 
				{
                    printf("thread 0x%x is exiting\n", (unsigned int)pthread_self());
                    pool->live_thr_num--;
                    pthread_mutex_unlock(&(pool->lock));
					//pthread_detach(pthread_self());
                    pthread_exit(NULL);
                }
            }
        }

        /*如果指定了true，要关闭线程池里的每个线程，自行退出处理---销毁线程池*/
        if (pool->shutdown) 
		{
            pthread_mutex_unlock(&(pool->lock));
            printf("thread 0x%x is exiting\n", (unsigned int)pthread_self());
            //pthread_detach(pthread_self());
            pthread_exit(NULL);     /* 线程自行结束 */
        }

        /*从任务队列里获取任务, 是一个出队操作*/
        task.function = pool->task_queue[pool->queue_front].function;
        task.arg = pool->task_queue[pool->queue_front].arg;

        pool->queue_front = (pool->queue_front + 1) % pool->queue_max_size;       /* 出队，模拟环形队列 */
        pool->queue_size--;

        /*通知可以有新的任务添加进来*/
        pthread_cond_broadcast(&(pool->queue_not_full));

        /*任务取出后，立即将 线程池琐 释放*/
        pthread_mutex_unlock(&(pool->lock));

        /*执行任务*/ 
        printf("thread 0x%x start working\n", (unsigned int)pthread_self());
        pthread_mutex_lock(&(pool->thread_counter));                            /*忙状态线程数变量琐*/
        pool->busy_thr_num++;                                                   /*忙状态线程数+1*/
        pthread_mutex_unlock(&(pool->thread_counter));

        (*(task.function))(task.arg);                                           /*执行回调函数任务*/
        //task.function(task.arg);                                              /*执行回调函数任务*/

        /*任务结束处理*/ 
        printf("thread 0x%x end working\n", (unsigned int)pthread_self());
        pthread_mutex_lock(&(pool->thread_counter));
        pool->busy_thr_num--;                                       /*处理掉一个任务，忙状态数线程数-1*/
        pthread_mutex_unlock(&(pool->thread_counter));
    }

    pthread_exit(NULL);
}

/* 管理线程 */
void *adjust_thread(void *threadpool)
{
    int i;
    threadpool_t *pool = (threadpool_t *)threadpool;
    while (!pool->shutdown) 
	{

        sleep(DEFAULT_TIME);                                    /*定时 对线程池管理*/

        pthread_mutex_lock(&(pool->lock));
        int queue_size = pool->queue_size;                      /* 关注 任务数 */
        int live_thr_num = pool->live_thr_num;                  /* 存活 线程数 */
        pthread_mutex_unlock(&(pool->lock));

        pthread_mutex_lock(&(pool->thread_counter));
        int busy_thr_num = pool->busy_thr_num;                  /* 忙着的线程数 */
        pthread_mutex_unlock(&(pool->thread_counter));

        /* 创建新线程 算法： 任务数大于最小线程池个数, 且存活的线程数少于最大线程个数时 如：30>=10 && 40<100*/
        if (queue_size >= MIN_WAIT_TASK_NUM && live_thr_num < pool->max_thr_num) 
		{
            pthread_mutex_lock(&(pool->lock));  
            int add = 0;

            /*一次增加 DEFAULT_THREAD 个线程*/
            for (i = 0; i < pool->max_thr_num && add < DEFAULT_THREAD_VARY
                    && pool->live_thr_num < pool->max_thr_num; i++) 
			{
                if (pool->threads[i] == 0 || !is_thread_alive(pool->threads[i])) 
				{
                    pthread_create(&(pool->threads[i]), NULL, threadpool_thread, (void *)pool);
                    add++;
                    pool->live_thr_num++;
                }
            }

            pthread_mutex_unlock(&(pool->lock));
        }

        /* 销毁多余的空闲线程 算法：忙线程X2 小于 存活的线程数 且 存活的线程数 大于 最小线程数时*/
        if ((busy_thr_num * 2) < live_thr_num  &&  live_thr_num > pool->min_thr_num) 
		{
            /* 一次销毁DEFAULT_THREAD个线程, 隨機10個即可 */
            pthread_mutex_lock(&(pool->lock));
            pool->wait_exit_thr_num = DEFAULT_THREAD_VARY;      /* 要销毁的线程数 设置为10 */
            pthread_mutex_unlock(&(pool->lock));

            for (i = 0; i < DEFAULT_THREAD_VARY; i++) 
			{
                /* 通知处在空闲状态的线程, 他们会自行终止*/
                pthread_cond_signal(&(pool->queue_not_empty));
            }
        }
    }

    return NULL;
}

int threadpool_destroy(threadpool_t *pool)
{
    int i;
    if (pool == NULL) 
	{
        return -1;
    }
    pool->shutdown = true;

    /*先销毁管理线程*/
    //pthread_join(pool->adjust_tid, NULL);

    for (i = 0; i < pool->live_thr_num; i++) 
	{
        /*通知所有的空闲线程*/
        pthread_cond_broadcast(&(pool->queue_not_empty));
    }

    /*for (i = 0; i < pool->live_thr_num; i++) 
	{
        pthread_join(pool->threads[i], NULL);
    }*/

    threadpool_free(pool);

    return 0;
}

int threadpool_free(threadpool_t *pool)
{
    if (pool == NULL) 
	{
        return -1;
    }

    if (pool->task_queue) 
	{
        free(pool->task_queue);
    }

    if (pool->threads) 
	{
        free(pool->threads);
        pthread_mutex_lock(&(pool->lock));
        pthread_mutex_destroy(&(pool->lock));
        pthread_mutex_lock(&(pool->thread_counter));
        pthread_mutex_destroy(&(pool->thread_counter));
        pthread_cond_destroy(&(pool->queue_not_empty));
        pthread_cond_destroy(&(pool->queue_not_full));
    }

    free(pool);
    pool = NULL;

    return 0;
}

int threadpool_all_threadnum(threadpool_t *pool)
{
    int all_threadnum = -1;

    pthread_mutex_lock(&(pool->lock));
    all_threadnum = pool->live_thr_num;
    pthread_mutex_unlock(&(pool->lock));

    return all_threadnum;
}

int threadpool_busy_threadnum(threadpool_t *pool)
{
    int busy_threadnum = -1;

    pthread_mutex_lock(&(pool->thread_counter));
    busy_threadnum = pool->busy_thr_num;
    pthread_mutex_unlock(&(pool->thread_counter));

    return busy_threadnum;
}

int is_thread_alive(pthread_t tid)
{
    int kill_rc = pthread_kill(tid, 0);     //发0号信号，测试线程是否存活
    if (kill_rc == ESRCH) 
	{
        return false;
    }

    return true;
}

/*测试*/ 

#if 1
/* 线程池中的线程，模拟处理业务 */
void *process(void *arg)
{
    printf("thread 0x%x working on task %d\n ",(unsigned int)pthread_self(),*(int *)arg);
    sleep(1);
    printf("task %d is end\n", *(int *)arg);

    return NULL;
}

int main(void)
{
    /*threadpool_t *threadpool_create(int min_thr_num, int max_thr_num, int queue_max_size);*/
    threadpool_t *thp = threadpool_create(3,100,100);   /*创建线程池，池里最小3个线程，最大100，队列最大100*/
    printf("pool inited");

    //int *num = (int *)malloc(sizeof(int)*20);
    int num[20], i;
    for (i = 0; i < 20; i++) 
	{
        num[i]=i;
        printf("add task %d\n",i);
        threadpool_add(thp, process, (void*)&num[i]);   /* 向线程池中添加任务 */
    }

    sleep(10);                                          /* 等子线程完成任务 */
    threadpool_destroy(thp);

    return 0;
}

#endif

```



# 七、UDP通信

## 1. 简单介绍

```c
TCP：传输控制协议, 面向连接的，稳定的，可靠的，安全的数据流传递

        稳定和可靠: 丢包重传

        数据有序: 序号和确认序号

        流量控制: 滑动窗口 

UDP：用户数据报协议 

        面向无连接的，不稳定，不可靠，不安全的数据报传递---更像是收发短信

        UDP传输不需要建立连接，传输效率更高，在稳定的局域网内环境相对可靠
```



## 2.相关函数

```c
UDP通信相关函数介绍:
ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags, struct sockaddr *src_addr, socklen_t *addrlen);
    函数说明: 接收消息
    参数说明:
        	sockfd 套接字
        	buf  要接受的缓冲区
        	len  缓冲区的长度
        	flags 标志位 一般填0
        	src_addr 原地址 传出参数 
        	addrlen  发送方地址长度 
    返回值
        成功: 返回读到的字节数 
        失败: 返回 -1 设置errno 
              调用该函数相当于TCP通信的recv+accept函数

ssize_t sendto(int sockfd, const void *buf, size_t len, int flags, const struct sockaddr *dest_addr, socklen_t addrlen);
    函数说明: 发送数据
    参数说明:
        	sockfd 套接字
        	dest_addr 目的地址
        	addrlen 目的地址长度
    返回值
        成功: 返回写入的字节数
        失败: 返回-1，设置errno 
```



## 3. UDP通信流程

![image-20210830183510600](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210830183510600.png)

```c
UDP的服务器编码流程：
	创建套接字  type=SOCK_DGRAM
	绑定ip和端口 
	while(1)
    {
收发消--recvfrom
发消息--sendto
    }
	关闭套接字--close

UDP客户端流程：
	创建套接字--socket
	while(1)
    {
收发消--recvfrom
发消息--sendto
    }
	关闭套接字--close


编写udp代码并进行测试

测试: 
多开器几个客户端经过测试表明:, udp天然支持多客户端, 这点和TCP不同, TCP需要维护连接.

使用nc命令进行测试: nc -u 127.1 8888


```



UDP服务端：

```c
//udp服务端
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <ctype.h>

int main()
{
	//创建socket
	int cfd = socket(AF_INET, SOCK_DGRAM, 0);
	if(cfd<0)
	{
		perror("socket error");
		return -1;
	}

	//绑定
	struct sockaddr_in serv;
	struct sockaddr_in client;
	bzero(&serv, sizeof(serv));
	serv.sin_family = AF_INET;
	serv.sin_port = htons(8888);
	serv.sin_addr.s_addr = htonl(INADDR_ANY);
	bind(cfd, (struct sockaddr *)&serv, sizeof(serv));

	int i;
	int n;
	socklen_t len;
	char buf[1024];
	while(1)
	{
		//读取数据
		memset(buf, 0x00, sizeof(buf));
		len = sizeof(client);
		n = recvfrom(cfd, buf, sizeof(buf), 0, (struct sockaddr *)&client, &len);

		//将大写转换为小写
		for(i=0; i<n; i++)
		{
			buf[i] = toupper(buf[i]);
		}
		printf("[%d]:n==[%d], buf==[%s]\n", ntohs(client.sin_port), n, buf);
		//发送数据
		sendto(cfd, buf, n, 0, (struct sockaddr *)&client, len);
	}

	//关闭套接字
	close(cfd);

	return 0;
}

```



UDP客户端：

```c
//udp客户端
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <ctype.h>

int main()
{
	//创建socket
	int cfd = socket(AF_INET, SOCK_DGRAM, 0);
	if(cfd<0)
	{
		perror("socket error");
		return -1;
	}

	int n;
	char buf[1024];
	struct sockaddr_in serv;
	serv.sin_family = AF_INET;
	serv.sin_port = htons(8888);
	inet_pton(AF_INET, "127.0.0.1", &serv.sin_addr.s_addr);

	while(1)
	{
		//读标准输入数据
		memset(buf, 0x00, sizeof(buf));
		n = read(STDIN_FILENO, buf, sizeof(buf));

		//发送数据
		sendto(cfd, buf, n, 0, (struct sockaddr *)&serv, sizeof(serv));

		//读取数据
		memset(buf, 0x00, sizeof(buf));
		n = recvfrom(cfd, buf, sizeof(buf), 0, NULL, NULL);
		printf("n==[%d], buf==[%s]\n", n, buf);
	}

	//关闭套接字
	close(cfd);

	return 0;
}

```

# 八、本地socket通信

## 1.基本概念

​		也叫UNIX域套接字，用于在同一台计算机上运行的进程之间的通信。UNIX域套接字仅仅复制数据，他们并不执行协议处理，不需要添加和删除网络报头，无需计算校验和，不要产生顺序号无需发送确认报文。

​		UNIX域套接字提供流和数据报两种接口，UNIX域数据服务是可靠的，既不会丢失报文也不会传递出错。



## 2.相关函数

```c
#include <sys/socket.h>
#include <sys/un.h>
int socket(int domain, int type, int protocol);
函数说明: 创建本地域socket
函数参数:	
domain: AF_UNIX or AF_LOCAL
type: SOCK_STREAM或者SOCK_DGRAM
protocol: 0 表示使用默认协议
函数返回值:
成功: 返回文件描述符.
失败: 返回-1, 并设置errno值.

```

​		创建socket成功以后, 会在内核创建缓冲区, 下图是客户端和服务端内核缓冲区示意图.

![image-20210831094941863](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210831094941863.png)

```c
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
函数说明: 绑定套接字
函数参数:
socket: 由socket函数返回的文件描述符
addr: 本地地址
addlen: 本地地址长度
函数返回值:
成功: 返回文件描述符.
失败: 返回-1, 并设置errno值.

需要注意的是: bind函数会自动创建socket文件, 若在调用bind函数之前socket文件已经存在, 则调用bind会报错, 可以使用unlink函数在bind之前先删除文件.
struct sockaddr_un {
    sa_family_t sun_family;  /* AF_UNIX or AF_LOCAL*/
    char sun_path[108];  /* pathname */
};

```

## 3.本地套接字服务器的流程

```c
本地套接字服务器的流程：
	可以使用TCP的方式, 必须按照tcp的流程 
	也可以使用UDP的方式, 必须按照udp的流程 
 
tcp的本地套接字服务器流程：
	创建套接字  socket(AF_UNIX,SOCK_STREAM,0)
	绑定 struct sockaddr_un &强转
	侦听 listen 
	获得新连接 accept 
	循环通信 read-write 
	关闭文件描述符 close

tcp本地套接字客户端流程：
	调用socket创建套接字
	调用bind函数将socket文件描述和socket文件进行绑定.
	不是必须的, 若无显示绑定会进行隐式绑定，但服务器不知道谁连接了.
	调用connect函数连接服务端
	循环通信read-write
	关闭文件描述符 close


编写代码并进行测试
	测试客户端工具:
		man  nc 
			-U      Specifies to use UNIX-domain sockets.
		
例如: nc -U sock.s


```



```c
offsetof()函数：测试结构体中某个变量，距离开始的偏移量
size = offsetof(struct sockaddr_un, sun_path) +strlen(un.sun_path);
#define offsetof(type, member) ((int)&((type *)0)->member)
```



```c
本地socket通信服务端开发流程：
1 创建socket
    lfd = socket(AF_UNIX,SO_STREAM,0);
2 绑定
    struct sockaddr_un serv;
	bzero(&serv,sizeof(serv));
	serv.sun_family = AF_UNIX;
	strcpy(serv.sun_path(),"./serv.sock");
	bind(lfd,(struct sockaddr *)&serv,sizeof(serv));
3 监听 listen()
4 接收新的客户端连接  accept()
5 while(1)
{
    //读数据 ---read()
    if(errno)
    {
       break;
    }
    //发送数据 ---write()
}
close(cfd);
close(lfd);
```

 

代码：

```c
//本地socket通信服务端
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <sys/un.h>


int main()
{
	//创建socket
	int lfd = socket(AF_UNIX, SOCK_STREAM, 0);
	if(lfd<0)
	{
		perror("socket error");
		return -1;
	}

	//删除socket文件,避免bind失败
	unlink("./server.sock");

	//绑定bind
	struct sockaddr_un serv;
	bzero(&serv, sizeof(serv));
	serv.sun_family = AF_UNIX;
	strcpy(serv.sun_path, "./server.sock"); 
	int ret = bind(lfd, (struct sockaddr *)&serv, sizeof(serv));
	if(ret<0)
	{
		perror("bind error");
		return -1;
	}

	//监听listen
	listen(lfd, 10);

	//接收新的连接-accept
	struct sockaddr_un client;
	bzero(&client, sizeof(client));
	int len = sizeof(client);
	int cfd = accept(lfd, (struct sockaddr *)&client, &len);
	if(cfd<0)
	{
		perror("accept error");	
		return -1;
	}
	printf("client->[%s]\n", client.sun_path);

	int n;
	char buf[1024];

	while(1)
	{
		//读数据
		memset(buf, 0x00, sizeof(buf));		
		n = read(cfd, buf, sizeof(buf));
		if(n<=0)
		{
			printf("read error or client close, n==[%d]\n", n);
			break;
		}
		printf("n==[%d], buf==[%s]\n", n, buf);

		//发送数据
		write(cfd, buf, n);
	}

	close(lfd);

	return 0;
}


```

```c
//本地socket通信客户端
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <sys/un.h>


int main()
{
	//创建socket
	int cfd = socket(AF_UNIX, SOCK_STREAM, 0);
	if(cfd<0)
	{
		perror("socket error");
		return -1;
	}

	//删除socket文件,避免bind失败
	unlink("./client.sock");

	//绑定bind
	struct sockaddr_un client;
	bzero(&client, sizeof(client));
	client.sun_family = AF_UNIX;
	strcpy(client.sun_path, "./client.sock"); 
	int ret = bind(cfd, (struct sockaddr *)&client, sizeof(client));
	if(ret<0)
	{
		perror("bind error");
		return -1;
	}

	struct sockaddr_un serv;
	bzero(&serv, sizeof(serv));
	serv.sun_family = AF_UNIX;
	strcpy(serv.sun_path, "./server.sock");
	ret = connect(cfd, (struct sockaddr *)&serv, sizeof(serv));
	if(ret<0)
	{
		perror("connect error");	
		return -1;
	}

	int n;
	char buf[1024];

	while(1)
	{
		memset(buf, 0x00, sizeof(buf));
		n = read(STDIN_FILENO, buf, sizeof(buf));

		//发送数据
		write(cfd, buf, n);

		//读数据
		memset(buf, 0x00, sizeof(buf));		
		n = read(cfd, buf, sizeof(buf));
		if(n<=0)
		{
			printf("read error or client close, n==[%d]\n", n);
			break;
		}
		printf("n==[%d], buf==[%s]\n", n, buf);
	}

	close(cfd);

	return 0;
}


```





# 九、libevent框架

## 1.介绍

```c
1 事件驱动, 高性能, 轻量级, 专注于网络
2 源代码精炼, 易读 
3 跨平台
4 支持多种I/O多路复用技术, 如epoll select poll等
5 支持I/O和信号等事件
```

```c
libevent的核心实现:
在linux上, 其实质就是epoll反应堆.
libevent是事件驱动, epoll反应堆也是事件驱动, 当要监测的事件发生的时候, 就会调用事件对应的回调函数, 执行相应操作. 特别提醒: 事件回调函数是由用户开发的, 但是不是由用户显示去调用的, 而是由libevent去调用的.
```

## 2.相关注意

```c
	进入到libevent-2.0.22-stable/sample下, 可以查看一些示例源代码文件.
使用libevent库编写代码在编译程序的时候需要指定库名:
	-levent;
安装文件的libevent库文件所在路径:
	libevent-2.0.22-stable/.libs;
编写代码的时候用到event.h头文件, 或者直接参考sample目录下的源代码文件也可以.
```

## 3. libevent的地基event_base

```c
使用libevent 函数之前需要分配一个或者多个 event_base 结构体, 每个event_base结构体持有一个事件集合, 可以检测以确定哪个事件是激活的, event_base结构相当于epoll红黑树的树根节点, 每个event_base都有一种用于检测某种事件已经就绪的 “方法”(回调函数)

通常情况下可以通过event_base_new函数获得event_base结构。
```

相关函数：

```c
1 struct event_base *event_base_new(void);    //event.h的L:337
  函数说明: 获得event_base结构
  参数说明: 无
  返回值: 
成功返回event_base结构体指针;
失败返回NULL;

2 void event_base_free(struct event_base *);   //event.h的L:561
	函数说明: 释放event_base指针

3 int event_reinit(struct event_base *base);  //event.h的L:349
	函数说明: 如果有子进程, 且子进程也要使用base, 则子进程需要对event_base重新初始化, 此时需要调用event_reinit函数.
函数参数: 由event_base_new返回的执行event_base结构的指针
返回值: 成功返回0, 失败返回-1

对于不同系统而言, event_base就是调用不同的多路IO接口去判断事件是否已经被激活, 对于linux系统而言, 核心调用的就是epoll, 同时支持poll和select.

查看libevent支持的后端的方法有哪些:
const char **event_get_supported_methods(void);
函数说明: 获得当前系统(或者称为平台)支持的方法有哪些
参数: 无
返回值: 返回二维数组, 类似与main函数的第二个参数**argv.

const char * event_base_get_method(const struct event_base *base);
函数说明: 获得当前base节点使用的多路io方法
函数参数: event_base结构的base指针.
返回值: 获得当前base节点使用的多路io方法的指针

```

## 4.等待时间产生 - 循环等待event_loop

```c
libevent在地基打好之后, 需要等待事件的产生, 也就是等待事件被激活, 所以程序不能退出, 对于epoll来说, 我们需要自己控制循环, 而在libevent中也给我们提供了API接口, 类似while(1)的功能. 
```

```c
int event_base_loop(struct event_base *base, int flags);   //event.h的L:660
   函数说明: 进入循环等待事件
参数说明:
	base: 由event_base_new函数返回的指向event_base结构的指针
	flags的取值：
	#define EVLOOP_ONCE	0x01
				只触发一次, 如果事件没有被触发, 阻塞等待
	#define EVLOOP_NONBLOCK	0x02
				非阻塞方式检测事件是否被触发, 不管事件触发与否, 都会					立即返回.
这个函数一般不用, 而大多数都调用libevent给我们提供的另外一个API：
    
int event_base_dispatch(struct event_base *base);   //event.h的L:364
函数说明: 进入循环等待事件
参数说明:由event_base_new函数返回的指向event_base结构的指针
调用该函数, 相当于没有设置标志位的event_base_loop。程序将会一直运行, 直到没有需要检测的事件了, 或者被结束循环的API终止。
    
int event_base_loopexit(struct event_base *base, const struct timeval *tv);
int event_base_loopbreak(struct event_base *base);
struct timeval {
	long    tv_sec;                    
	long    tv_usec;            
};
两个函数的区别是如果正在执行激活事件的回调函数, 那么event_base_loopexit将在事件回调执行结束后终止循环（如果tv时间非NULL, 那么将等待tv设置的时间后立即结束循环）, 而event_base_loopbreak会立即终止循环。

```

## 5.使用libevent库的步骤

```c
1 创建根节点--event_base_new
    
2 设置监听事件和数据可读可写的事件的回调函数
设置了事件对应的回调函数以后, 当事件产生的时候会自动调用回调函数
    
3 事件循环--event_base_dispatch
相当于while(1), 在循环内部等待事件的发生,  若有事件发生则会触发事件对应的回调函数。
    
4 释放根节点--event_base_free
  释放由event_base_new和event_new创建的资源, 分别调用event_base_free
和event_free函数.
```

## 6.状态转换

![image-20210831185823810](E:\typroa_pic\image-20210831185823810.png)

```c
主要几个状态：
无效的指针: 此时仅仅是定义了 struct event *ptr；
非未决：相当于创建了事件, 但是事件还没有处于被监听状态, 类似于我们使用epoll的时候定义了struct epoll_event ev并且对ev的两个字段进行了赋值, 但是此时尚未调用epoll_ctl对事件上树.
未决：就是对事件开始监听, 暂时未有事件产生。相当于调用epoll_ctl对要监听的事件上树, 但是没有事件产生.
激活：代表监听的事件已经产生, 这时需要处理, 相当于调用epoll_wait函数有返回, 当事件被激活以后,  libevent会调用该事件对应的回调函数.
    
libevent的事件驱动对应的结构体为struct event, 对应的函数在图上也比较清晰, 下面介绍一下主要的函数:
typedef void (*event_callback_fn)(evutil_socket_t fd, short events, void *arg);
struct event *event_new(struct event_base *base, evutil_socket_t fd, short events, event_callback_fn cb, void *arg);
函数说明: event_new负责创建event结构指针, 同时指定对应的地基base, 还有对应的文件描述符, 事件, 以及回调函数和回调函数的参数。
参数说明：
    base: 对应的根节点--地基
    fd: 要监听的文件描述符
    events:要监听的事件
    #define  EV_TIMEOUT    0x01   //超时事件
    #define  EV_READ       0x02    //读事件
    #define  EV_WRITE      0x04    //写事件
    #define  EV_SIGNAL     0x08    //信号事件
    #define  EV_PERSIST     0x10    //周期性触发
    #define  EV_ET         0x20    //边缘触发, 如果底层模型支持设置则有效, 若不支持则无效.
    若要想设置持续的读事件则： EV_READ | EV_PERSIST
    cb 回调函数, 原型如下：
    typedef void (*event_callback_fn)(evutil_socket_t fd, short events, void *arg);
    注意: 回调函数的参数就对应于event_new函数的fd, event和arg

    #define evsignal_new(b, x, cb, arg)event_new((b), (x), EV_SIGNAL|EV_PERSIST, (cb), (arg))
    
int event_add(struct event *ev, const struct timeval *timeout);
函数说明: 将非未决态事件转为未决态, 相当于调用epoll_ctl函数(EPOLL_CTL_ADD), 开始监听事件是否产生, 相当于epoll的上树操作.
参数说明：
	ev: 调用event_new创建的事件
timeout: 限时等待事件的产生, 也可以设置为NULL, 没有限时。

int event_del(struct event *ev);
函数说明: 将事件从未决态变为非未决态, 相当于epoll的下树（epoll_ctl调用			EPOLL_CTL_DEL操作）操作。
参数说明: ev指的是由event_new创建的事件.

void event_free(struct event *ev);
函数说明: 释放由event_new申请的event节点。
```

## 7. libevent服务端程序思路

```c
编写一个基于event实现的tcp服务器
1 创建socket---socket()
2 设置端口复用---setsockopt(lfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(int))
3 绑定--bind()
4 设置监听--listen()
5 创建地基
	struct event_base *base = event_base_new()
6 创建lfd对应的事件
	struct event *ev = event_new(base, lfd, EV_READ|EV_PERSIST, conncb, base);
7 上event_base地基
	event_add(ev, NULL);
8 进入事件循环
	event_base_dispatch(base);
	
9 释放资源
	event_base_free(base);
	event_free(ev);

typedef void (*event_callback_fn)(evutil_socket_t fd, short events, void *arg);
//监听文件描述符对应的事件回调函数
void conncb(evutil_socket_t fd, short events, void *arg)
{
	struct event_base *base = (struct event_base *)arg;
	//接受新的连接
	int cfd = accept(fd, NULL, NULL);
	if(cfd>0)
	{
		//创建一个新的事件
		struct event *ev = event_new(base, cfd, EV_READ|EV_PERSIST, readcb, NULL);
		event_add(ev, NULL);
	}
}

//读客户端数据对应的回调函数
void readcb(evutil_socket_t fd, short events, void *arg)
{	
	//读数据
	n = read(fd, buf, sizeof(buf));
	if(n<=0)
	{
		//从base地基上删除该事件
		close(fd);
		event_del(ev);
		event_free(ev);
	}
	//发送数据给对方
	write(fd, buf, n);
}
```

代码实现：

此代码中`connev`为全局变量，每次指向最后一个连接，关闭其他连接时，会导致其关闭错误的连接。

改进方案：

创建一个文件描述符和事件一对一映射的结构体，再创建一个该结构体数组，每次关闭连接时遍历数组，找到要关闭的文件描述符。

```c
//编写libevent服务端
#include <stdio.h>
#include <stdlib.h>
#include <sys/socket.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <string.h>
#include <event2/event.h>

struct event *connev = NULL;

//typedef void (*event_callback_fn)(evutil_socket_t fd, short events, void *arg);
void readcb(evutil_socket_t fd, short events, void *arg)
{
	int n;
	char buf[1024];
	memset(buf, 0x00, sizeof(buf));
	n = read(fd, buf, sizeof(buf));
	if(n<=0)
	{
		close(fd);
		//将通信文件描述符对应的事件从base地基上删除
		event_del(connev);
	}
	else
	{
		write(fd, buf, n);
	}
}

void conncb(evutil_socket_t fd, short events, void *arg)
{
	struct event_base *base = (struct event_base *)arg;

	//接受新的客户端连接
	int cfd = accept(fd, NULL, NULL);
	if(cfd>0)
	{
		//创建通信文件描述符对应的事件并设置回调函数为readcb
		connev = event_new(base, cfd, EV_READ|EV_PERSIST, readcb, NULL);
		if(connev==NULL)
		{
			//退出循环
			event_base_loopexit(base, NULL);
		}
		
		//将通信文件描述符对应的事件上event_base地基
		event_add(connev, NULL);	
	}	
}

int main()
{
	//创建socket
	int lfd = socket(AF_INET, SOCK_STREAM, 0);

	//设置端口复用
	int opt = 1;
	setsockopt(lfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(opt));

	//绑定
	struct sockaddr_in serv;
	bzero(&serv, sizeof(serv));
	serv.sin_addr.s_addr = htonl(INADDR_ANY);
	serv.sin_port = htons(8888);
	serv.sin_family = AF_INET;
	bind(lfd, (struct sockaddr*)&serv, sizeof(serv));

	//监听
	listen(lfd, 120);

	//创建地基
	struct event_base *base = event_base_new();
	if(base==NULL)
	{
		printf("event_base_new error\n");
		return -1;
	}

	//创建监听文件描述符对应的事件
	//struct event *event_new(struct event_base *base, evutil_socket_t fd, short events, event_callback_fn cb, void *arg);
	struct event *ev = event_new(base, lfd, EV_READ|EV_PERSIST, conncb, base);
	if(ev==NULL)
	{
		printf("event_new error\n");
		return -1;
	}

	//将新的事件节点上base地基
	event_add(ev, NULL);

	//进入事件循环等待
	event_base_dispatch(base);

	//释放资源
	event_base_free(base);
	event_free(ev);

	close(lfd);
	return 0;
}
```



## 8.`bufferevent`--自带buffer的事件

`bufferevent`实际上也是一个`event` ，只不过比普通的`event`高级一些, 它的内部有两个缓冲区, 以及一个文件描述符（网络套接字）。一个网络套接字有读和写两个缓冲区, `bufferevent`同样也带有两个缓冲区, 还有就是`libevent`事件驱动的核心回调函数, 那么四个缓冲区以及触发回调的关系如下：

![image-20210901090027926](E:\typroa_pic\image-20210901090027926.png)

从图中可以得知, 一个`bufferevent`对应两个缓冲区, 三个回调函数, 分别是写回调, 读回调和事件回调.

`bufferevent`有三个回调函数：

​	读回调 – 当`bufferevent`将底层读缓冲区的数据读到自身的读缓冲区时触发读事件回调.需要注意的是: 数据有内核到`bufferevent`的过程不是用户程序执行的, 是由`bufferevent`内部操作的.

​		写回调 – 当`bufferevent`将自身写缓冲的数据写到底层写缓冲区的时候触发写事件回调, 由于数据最终是写入了内核的写缓冲区中, 应用程序已经无法控制, 这个事件对于应用程序来说基本没什么用, 只是通知功能.

​		事件回调 – 当`bufferevent`绑定的socket连接, 断开或者异常的时候触发事件回调.

### 8.1主要函数

```c++
主要使用的函数如下：
struct bufferevent *bufferevent_socket_new(struct event_base *base, evutil_socket_t fd, int options);
函数说明: bufferevent_socket_new 对已经存在socket创建bufferevent事件, 可用于后面讲到的连接监听器的回调函数中.
参数说明：
	base :对应根节点
	fd   :文件描述符
	options : bufferevent的选项
		BEV_OPT_CLOSE_ON_FREE  -- 释放bufferevent自动关闭底层接口(当bufferevent被释放以后, 文件描述符也随之被close)    
		BEV_OPT_THREADSAFE  -- 使bufferevent能够在多线程下是安全的

int bufferevent_socket_connect(struct bufferevent *bev, struct sockaddr *serv, int socklen);
函数说明: 该函数封装了底层的socket与connect接口, 通过调用此函数, 可以将bufferevent事件与通信的socket进行绑定, 参数如下：
	bev – 需要提前初始化的bufferevent事件
	serv – 对端(一般指服务端)的ip地址, 端口, 协议的结构指针
	socklen – 描述serv的长度
说明: 调用此函数以后, 通信的socket与bufferevent缓冲区做了绑定, 后面调用了bufferevent_setcb函数以后, 会对bufferevent缓冲区的读写操作的事件设置回调函数, 当往缓冲区中写数据的时候会触发写回调函数, 当数据从socket的内核缓冲区读到bufferevent读缓冲区中的时候会触发读回调函数.

void bufferevent_free(struct bufferevent *bufev);
函数说明: 释放bufferevent

void bufferevent_setcb(struct bufferevent *bufev,
    			bufferevent_data_cb readcb, bufferevent_data_cb writecb,
bufferevent_event_cb eventcb, void *cbarg);
函数说明: bufferevent_setcb用于设置bufferevent的回调函数, readcb, writecb,eventcb分别对应了读回调, 写回调, 事件回调, cbarg代表回调函数的参数。
    
回调函数的原型：
typedef void (*bufferevent_data_cb)(struct bufferevent *bev, void *ctx);
typedef void (*bufferevent_event_cb)(struct bufferevent *bev, short what, void *ctx);
What 代表 对应的事件
BEV_EVENT_EOF--遇到文件结束指示
BEV_EVENT_ERROR--发生错误
BEV_EVENT_TIMEOUT--发生超时
BEV_EVENT_CONNECTED--请求的过程中连接已经完成
    
int bufferevent_write(struct bufferevent *bufev, const void *data, size_t size);
	bufferevent_write是将data的数据写到bufferevent的写缓冲区

int bufferevent_write_buffer(struct bufferevent *bufev, struct evbuffer *buf);
	bufferevent_write_buffer 是将数据写到写缓冲区另外一个写法, 实际上bufferevent的内部的两个缓冲区结构就是struct evbuffer。

size_t bufferevent_read(struct bufferevent *bufev, void *data, size_t size);
	bufferevent_read 是将bufferevent的读缓冲区数据读到data中, 同时将读到的数据从bufferevent的读缓冲清除。

int bufferevent_read_buffer(struct bufferevent *bufev, struct evbuffer *buf);
	bufferevent_read_buffer 将bufferevent读缓冲数据读到buf中, 接口的另外一种。

int bufferevent_enable(struct bufferevent *bufev, short event);

int bufferevent_disable(struct bufferevent *bufev, short event);
	bufferevent_enable与bufferevent_disable是设置事件是否生效, 如果设置为disable, 事件回调将不会被触发。

```

### 8.2代码实现

![image-20210901104413213](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210901104413213.png)

```c++
//bufferevent建立客户端的过程
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <errno.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <event.h>
#include <event2/bufferevent.h>
#include <event2/buffer.h>
#include <event2/util.h>

int tcp_connect_server(const char* server_ip, int port);
void cmd_msg_cb(int fd, short events, void* arg);
void server_msg_cb(struct bufferevent* bev, void* arg);
void event_cb(struct bufferevent *bev, short event, void *arg);

int main(int argc, char** argv)
{
    if( argc < 3 )
    {
        //两个参数依次是服务器端的IP地址、端口号
        printf("please input 2 parameter\n");
        return -1;
    }
    //创建根节点
    struct event_base *base = event_base_new();
    //创建并且初始化buffer缓冲区
    struct bufferevent* bev = bufferevent_socket_new(base, -1,
                                                     BEV_OPT_CLOSE_ON_FREE);

    //监听终端输入事件 设置标准输入的监控,设置回调是 cmd_msg_cb 
    struct event* ev_cmd = event_new(base, STDIN_FILENO,
                                     EV_READ | EV_PERSIST,
                                     cmd_msg_cb, (void*)bev);
    //上树 开始监听标准输入的读事件
    event_add(ev_cmd, NULL);
    
    struct sockaddr_in server_addr;
    memset(&server_addr, 0, sizeof(server_addr) );
    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(atoi(argv[2]));
    //将ip地址转换为网络字节序
    inet_aton(argv[1], &server_addr.sin_addr);

    //连接到 服务器ip地址和端口 初始化了 socket文件描述符 socket+connect 
    bufferevent_socket_connect(bev, (struct sockaddr *)&server_addr,
                               sizeof(server_addr));
    //设置buffer的回调函数 主要设置了读回调 server_msg_cb ,传入参数是标准输入的读事件
    bufferevent_setcb(bev, server_msg_cb, NULL, event_cb, (void*)ev_cmd);
    bufferevent_enable(bev, EV_READ | EV_PERSIST);

    event_base_dispatch(base);//循环等待
    event_free(ev_cmd);
    bufferevent_free(bev);
    event_base_free(base);
    printf("finished \n");
    return 0;
}
//终端输入回调
void cmd_msg_cb(int fd, short events, void* arg)
{
    char msg[1024];

    int ret = read(fd, msg, sizeof(msg));
    if( ret < 0 )
    {
        perror("read fail ");
        exit(1);
    }
    //得到bufferevent指针,目的是为了写到bufferevent的写缓冲区
    struct bufferevent* bev = (struct bufferevent*)arg;

    //把终端的消息发送给服务器端
    bufferevent_write(bev, msg, ret);
}

void server_msg_cb(struct bufferevent* bev, void* arg)
{
    char msg[1024];

    size_t len = bufferevent_read(bev, msg, sizeof(msg));
    msg[len] = '\0';

    printf("recv %s from server\n", msg);
}

void event_cb(struct bufferevent *bev, short event, void *arg)
{

    if (event & BEV_EVENT_EOF)
        printf("connection closed\n");
    else if (event & BEV_EVENT_ERROR)
        printf("some other error\n");
    else if( event & BEV_EVENT_CONNECTED)
    {
        printf("the client has connected to server\n");
        return ;
    }

    //这将自动close套接字和free读写缓冲区
    bufferevent_free(bev);
    //释放event事件 监控读终端
    struct event *ev = (struct event*)arg;
    event_free(ev);
}
```

## 9.链接监听器

​		链接监听器封装了底层的socket通信相关函数, 比如socket, bind, listen, accept这几个函数。链接监听器创建后实际上相当于调用了socket, bind, listen, 此时等待新的客户端连接到来, 如果有新的客户端连接, 那么内部先进行调用accept处理, 然后调用用户指定的回调函数。

### 9.1 函数原型

```c++
函数声明所在的头文件:  event2/listener.h
struct evconnlistener *evconnlistener_new_bind(struct event_base *base,evconnlistener_cb cb, void *ptr, unsigned flags, int backlog,const struct sockaddr *sa, int socklen);
函数说明: 
是在当前没有套接字的情况下对链接监听器进行初始化, 看最后2个参数实际上就是bind使用的关键参数, backlog是listen函数的关键参数（略有不同的是, 如果backlog是-1, 那么监听器会自动选择一个合适的值, 如果填0, 那么监听器会认为listen函数已经被调用过了）, ptr是回调函数的参数, cb是有新连接之后的回调函数, 但是注意这个回调函数触发的时候, 链接器已经处理好新连接了, 并将与新连接通信的描述符交给回调函数。flags 需要参考几个值：
	LEV_OPT_LEAVE_SOCKETS_BLOCKING   文件描述符为阻塞的
	LEV_OPT_CLOSE_ON_FREE            关闭时自动释放
	LEV_OPT_REUSEABLE                端口复用
	LEV_OPT_THREADSAFE               分配锁, 线程安全

struct evconnlistener *evconnlistener_new(struct event_base *base,evconnlistener_cb cb, void *ptr, unsigned flags, int backlog,evutil_socket_t fd);
evconnlistener_new函数与前一个函数不同的地方在与后2个参数, 使用本函数时, 认为socket已经初始化好, 并且bind完成, 甚至也可以做完listen, 所以大多数时候, 我们都可以使用第一个函数。

两个函数的回调函数
typedef void (*evconnlistener_cb)(struct evconnlistener *evl, evutil_socket_t fd, struct sockaddr *cliaddr, int socklen, void *ptr);
回调函数fd参数是与客户端通信的描述符, 并非是等待连接的监听的那个描述符, 所以cliaddr对应的也是新连接的对端地址信息, 已经是accept处理好的。

void evconnlistener_free(struct evconnlistener *lev);
函数说明: 释放链接监听器

int evconnlistener_enable(struct evconnlistener *lev);
函数说明: 使链接监听器生效

int evconnlistener_disable(struct evconnlistener *lev);
函数说明: 使链接监听器失效

```

## 10.`libevent`代码分析

![image-20210901101054652](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210901101054652.png)

```c++
/*
  This exmple program provides a trivial server program that listens for TCP
  connections on port 9995.  When they arrive, it writes a short message to
  each client connection, and closes each connection once it is flushed.

  Where possible, it exits cleanly in response to a SIGINT (ctrl-c).
*/

#include <string.h>
#include <errno.h>
#include <stdio.h>
#include <signal.h>
#ifndef WIN32
#include <netinet/in.h>
# ifdef _XOPEN_SOURCE_EXTENDED
#  include <arpa/inet.h>
# endif
#include <sys/socket.h>
#endif

#include <event2/bufferevent.h>
#include <event2/buffer.h>
#include <event2/listener.h>
#include <event2/util.h>
#include <event2/event.h>
#include <ctype.h>

static const char MESSAGE[] = "Hello, World!\n";

static const int PORT = 9995;

static void listener_cb(struct evconnlistener *, evutil_socket_t,
    struct sockaddr *, int socklen, void *);
static void conn_writecb(struct bufferevent *, void *);
static void conn_readcb(struct bufferevent *, void *);
static void conn_eventcb(struct bufferevent *, short, void *);
static void signal_cb(evutil_socket_t, short, void *);

int
main(int argc, char **argv)
{
	struct event_base *base;   //地基
	struct evconnlistener *listener; //链接监听器
	struct event *signal_event;  //信号事件

	struct sockaddr_in sin;
#ifdef WIN32
	WSADATA wsa_data;
	WSAStartup(0x0201, &wsa_data);
#endif

	//创建地基---相当于epoll的树根(epoll_create)
	base = event_base_new();
	if (!base) {
		fprintf(stderr, "Could not initialize libevent!\n");
		return 1;
	}

	memset(&sin, 0, sizeof(sin));
	sin.sin_family = AF_INET;
	sin.sin_port = htons(PORT);

	//创建链接监听器--socket-bind-listen-accept
	//listener_cb: 回调函数
	//LEV_OPT_REUSEABLE|LEV_OPT_CLOSE_ON_FREE: 设置端口复用, 当链接监听器释放的时候关闭套接字(监听文件描述符)
	listener = evconnlistener_new_bind(base, listener_cb, (void *)base,
	    LEV_OPT_REUSEABLE|LEV_OPT_CLOSE_ON_FREE, -1,
	    (struct sockaddr*)&sin,
	    sizeof(sin));

	if (!listener) {
		fprintf(stderr, "Could not create a listener!\n");
		return 1;
	}

	//设置SIGINT信号的事件回调
	signal_event = evsignal_new(base, SIGINT, signal_cb, (void *)base);

	if (!signal_event || event_add(signal_event, NULL)<0) {
		fprintf(stderr, "Could not create/add a signal event!\n");
		return 1;
	}

	//进入等待事件循环---相当于while(1)
	event_base_dispatch(base);

	//释放资源
	evconnlistener_free(listener);
	event_free(signal_event);
	event_base_free(base);

	printf("done\n");
	return 0;
}


//listener: 链接监听器
//fd: 通信文件描述符
//sa和socklen: 客户端IP地址信息
//user_data: 参数
static void
listener_cb(struct evconnlistener *listener, evutil_socket_t fd,
    struct sockaddr *sa, int socklen, void *user_data)
{
	struct event_base *base = user_data;
	struct bufferevent *bev;

	//创建bufferevent缓冲区
	//BEV_OPT_CLOSE_ON_FREE: bufferevent释放的时候自动关闭通信文件描述符
	bev = bufferevent_socket_new(base, fd, BEV_OPT_CLOSE_ON_FREE);
	if (!bev) {
		fprintf(stderr, "Error constructing bufferevent!");
		event_base_loopbreak(base);//退出循环,程序结束
		return;
	}
	//设置回调函数: 读回调, 写回调和事件回调
	bufferevent_setcb(bev, conn_readcb, conn_writecb, conn_eventcb, NULL);
	//使bufferevent设置生效
	bufferevent_enable(bev, EV_WRITE);
	bufferevent_enable(bev, EV_READ);
	
	//使bufferevent设置无效
	//bufferevent_disable(bev, EV_READ);

	//bufferevent_write(bev, MESSAGE, strlen(MESSAGE));
}

static void
conn_readcb(struct bufferevent *bev, void *user_data)
{
	//size_t bufferevent_read(struct bufferevent *bufev, void *data, size_t size);
	char buf[1024];
	memset(buf, 0x00, sizeof(buf));
	//从bufferevent读数据
	int n = bufferevent_read(bev, buf, sizeof(buf));
	printf("n=[%d],buf==[%s]\n", n, buf);
	
	int i=0;
	for(i=0; i<n; i++)
	{
		buf[i] = toupper(buf[i]);
	}
	//往bufferevent的写缓冲区写数据
	//int bufferevent_write(struct bufferevent *bufev, const void *data, size_t size);
	bufferevent_write(bev, buf, n);//写bufferevent缓冲区会触发写事件回调
}

static void
conn_writecb(struct bufferevent *bev, void *user_data)
{
	struct evbuffer *output = bufferevent_get_output(bev);
	if (evbuffer_get_length(output) == 0) {
		printf("flushed answer\n");
		//bufferevent_free(bev);不注销掉的话，会删除通信文件描述符
	}
}

static void
conn_eventcb(struct bufferevent *bev, short events, void *user_data)
{
	if (events & BEV_EVENT_EOF) {
		printf("Connection closed.\n");
	} else if (events & BEV_EVENT_ERROR) {
		printf("Got an error on the connection: %s\n",
		    strerror(errno));/*XXX win32*/
	}
	/* None of the other events can happen here, since we haven't enabled
	 * timeouts */
	bufferevent_free(bev);
}

static void
signal_cb(evutil_socket_t sig, short events, void *user_data)
{
	struct event_base *base = user_data;
	struct timeval delay = { 2, 0 };

	printf("Caught an interrupt signal; exiting cleanly in two seconds.\n");

	event_base_loopexit(base, &delay);
}
```

# 十、`Html`语言基础

##   1.`Html`简介

```html
 Html的组成可以分为如下部分:

1.    <!doctype html> 声明文档类型,可以不写

2.    <html> 开始 和</html> 结束,属于html的根标签

3.    <head></head> 头部标签,头部标签内一般有 <title></title>

4.    <body></body> 主体标签,一般用于显示内容

例如:

<html> 

<head>

<title>这是一个标题</title>

</head>


<body>

<font color="red" size="5">hello, world</font>

</body>

</html>

如果想要添加注释,可以使用 <!—我是注释 -->的方式.

也可以指定页面类型和字符编码,下面设置页面类型为html,并且字符编码为utf8

<meta http-equiv="content-Type" content="text/html; charset=utf8">

Html标签属性,可以双引号,单引号,或者不写
```



## 2.Html标签介绍

### 1.题目标签

```html
共有6种,<h1>,<h2>,…<h6>,其中<h1>最大,<h6>最小
```

### 2.文本标签

```html
 <font>标签,可以设置颜色和字体大小属性

颜色表示方法(可以参考网站: http://tool.oschina.net/commons?type=3):

1.    英文单词 red green blue …

2.    使用16进制的形式表示颜色:#ffffff

3.    使用rgb(255,255,0)

字体大小可以使用size属性,大小范围为1-7,其中7最大,1最小.

有时候需要使用换行标签 ,这是一个短标签 <br/>

与之对应另外还有一个水平线也是短标签, <hr/>,水平线也可以设置颜色和大小
```



### 3.列表标签

```html
列表标签分无序列表和有序列表,分别对应<ul>和<ol>.

    无序列表的格式如下:

    <ul>

        <li>列表内容1</li>

        <li>列表内容2</li>

        …

    </ul>

    无序列表可以设置type属性:

    实心圆圈:type=disc

    空心圆圈:type=circle

    小方块: type=square

    有序列表的格式如下:

    <ol>

        <li>列表内容1</li>

        <li>列表内容2</li>

        …

    </ol>

    有序列表同样可以设置type属性

    数字:type=1,也是默认方式

    英文字母:type=a或type=A

    罗马数字:type=i或type=I
```



### 4. 图片标签

```html
图片标签使用<img>,内部需要设置若干属性,可以不必写结束标签
属性:

1.    src=”3.gif” 图片来源,必写

2.    alt=”小岳岳” 图片不显示时,显示的内容

3.    title=”我的天呐” 鼠标移动到图片上时显示的文字

4.    width=”600” 图片显示的宽度

5.    height=”400” 图片显示的高度

例如:

<img src="3.gif" alt="小岳岳" title="我的天呐！" width="300" height="200" /> 

     注意:当图片未定义宽高,图片百分百比例显示,如果只改变图片宽度或者高度,会等比例缩放
```



### 5.超链接标签

```html
超链接标签使用<a>,同样需要设置属性表明要链接到哪里.

属性:
1.    href=”http://www.itcast.cn”,前往地址,必填,注意要写http://

2.    title=”前往传智” 鼠标移动到链接上时显示的文字

3.    target=”_self”或者”_blank”,_self是默认值,在自身页面打开,_blank是新开页面前往连接地址 

示例:

<a href="http://www.itcast.cn" title="去往传智" target="_self" >来传智</a>

当我们访问某个网站的时候,当请求的资源不存在,经常会给我们报告一个错误,显示为404错误,一般会给请求用户返回一个错误页,大家可以自行尝试一下编写一个我们自己的错误页.    
```

# 十一、`http`超文本传输协议

## 1.`http`请求消息格式说明

```http
GET /aaa.txt HTTP/1.1
Accept: text/html, application/xhtml+xml, image/jxr, */*
Accept-Language: zh-CN
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; rv:11.0) like Gecko
Host: 192.168.26.69:9999
Connection: Keep-Alive
Accept-Encoding: gzip, deflate

请求消息分为四部分内容:
1.	请求行 说明请求类型,要访问的资源,以及使用的http版本
2.	请求头  说明服务器使用的附加信息,都是键值对,比如表明浏览器类型
3.	空行  不能省略-而且是\r\n,包括请求行和请求头都是以\r\n结尾
4.	请求数据  表明请求的特定数据内容,可以省略-如登陆时,会将用户名和密码内容作为请求数据

http请求消息格式说明:
1 请求行:如GET /aaa.txt HTTP/1.1
2 请求头:健值对
3 \r\n
4 请求内容
```

## 2.请求类型`get`和`post`的区别

`http`协议有很多种请求类型,对我们来说常见的用的最多的是`get`和`post`请求。常见的请求类型如下：

1.	Get 请求指定的页面信息，并返回实体主体
2.	Post  向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。
3.	get 和 post 请求都是请求资源,而且都会提交数据,如果提交密码信息用get请求,就会明文显示,而post则不会显示出涉密信息.

get和post的区别:
当提交表单的时候, 使用post方式不显示提交的数据
使用get方式会显示如用户名和密码之类的数据.
使用post比get方式安全.

## 3.`http`响应消息格式说明

响应消息是代表服务器收到请求消息后,给浏览器做的反馈,所以响应消息是服务器发送给浏览器的,响应消息也分为四部分:

1. 状态行  包括`http`版本号,状态码,状态信息

2. 消息报头  说明客户端要使用的一些附加信息,也是键值对

3. 空行 \r\n  同样不能省略

4. 响应正文   服务器返回给客户端的文本信息

```http
http响应消息格式说明:
1 响应行: HTTP/1.1 200 OK 包括http版本号,状态码,状态信息
2 响应头: 健值对   说明客户端要使用的一些附加信息,也是键值对
		content-type
		Conten-Length: 要有就必须是正确的文件大小, 要么写0
3 空行: \r\n   同样不能省略
4 响应消息正文:  服务器返回给客户端的文本信息	
```

## 4.`http`常见状态码

```tex
http状态码由三位数字组成,第一个数字代表响应的类别,有五种分类:
1.	1xx  指示信息--表示请求已接收，继续处理
2.	2xx  成功--表示请求已被成功接收、理解、接受
3.	3xx  重定向--要完成请求必须进行更进一步的操作
4.	4xx  客户端错误--请求有语法错误或请求无法实现
5.	5xx  服务器端错误--服务器未能实现合法的请求
常见的状态码如下:
	200 OK  客户端请求成功
	301  Moved Permanently 重定向
	400 Bad Request       客户端请求有语法错误，不能被服务器所理解
	401 Unauthorized      请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用 
	403 Forbidden            服务器收到请求，但是拒绝提供服务
	404 Not Found           请求资源不存在，eg：输入了错误的URL
	500 Internal Server Error     服务器发生不可预期的错误
	503 Server Unavailable        服务器当前不能处理客户端的请求，一段时间后可能恢复正常

```

## 5.`http`常见文件类型分类

```c
http与浏览器交互时,为使浏览器能够识别文件信息,所以需要传递文件类型,这也是响应消息必填项,常见的类型如下:
	普通文件:  text/plain; charset=utf-8
	*.html:    text/html; charset=utf-8
	*.jpg:     image/jpeg
	*.gif:     image/gif
	*.png:     image/png
	*.wav:     audio/wav
	*.avi:     video/x-msvideo
	*.mov:     video/quicktime
	*.mp3:     audio/mpeg
特别说明 
charset=iso-8859-1    西欧的编码，说明网站采用的编码是英文；
charset=gb2312         说明网站采用的编码是简体中文；
charset=utf-8              代表世界通用的语言编码；可以用到中文、韩文、日文等世界上所有语言编码上
charset=euc-kr          说明网站采用的编码是韩文；
charset=big5             说明网站采用的编码是繁体中文；
```

# 十二、Web服务器

## 1.开发流程

```c
web服务器开发流程:
1 创建socket, 得到监听文件描述符lfd----socket()
2 设置端口复用----setsockopt()
3 绑定----bind()
4 设置监听-----listen()
5 创建epoll树, 得到树根文件描述符epfd---epoll_create()
6 将监听文件描述符lfd上树---		     				              epoll_ctl(epfd,EPOLL_CTL_ADD...);     
7 
while(1)
{
	//等待事件的发生
	nready = epoll_wait();
	if(nready<0)
	{
		if(errno==EINTR)
		{
			continue;
		}
		break;
	}
	
	//下面是有事件发生, 循环处理每一个文件描述符
	for(i=0; i<nready; i++)
	{
		sockfd = event[i].data.fd;
		//有客户端连接请求到来
		if(sockfd==lfd)
		{
			//接受新的客户端连接请求
			cfd = accept();
			
			//将新的cfd上epoll树
			epoll_ctl(epfd, EPOLL_CTL_ADD...);
		}
		//有数据发来的情况
		else
		{
			//接受数据并进行处理
			http_request(cfd);
		}
	}
}

int http_request(int cfd)
{
	//读取请求行
	Readline();
	
	//分析请求行, 得到要请求的资源文件名file
		如: GET /hanzi.c /HTTP1.1
		
	//循环读完剩余的内核缓冲区的数据
	while((n = Readline())>0);	
		
	//判断文件释放存在
	stat();
	
	1 文件不存在
		返回错误页
			组织应答信息: http响应格式消息+错误页正文内容
	2 文件存在
		判断文件类型:
			2.1 普通文件
				组织应答信息: http响应格式消息+消息正文
			2.2 目录文件
				组织应答消息: http响应格式消息+html格式文件内容
}
```

