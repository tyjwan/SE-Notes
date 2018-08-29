# FTP 编写 3：同时为多个客户端服务（多线程）
***
&ensp;&ensp;&ensp;&ensp;在上篇文章中写了一个能连接的FTP，但是它只能为一个客户端进行服务，而我们知道在现实生活中我们见到的大部分网络应用都能同时为多个用户提供服务，所以接下来我们的目标是使用多线程来使服务端能同时为多个客户端进行服务。

&ensp;&ensp;&ensp;&ensp;C++ 多线程这个不是网络编程的内容，所以在这里不会详细讲解（其实我也不懂啊，刚看的书就来瞎写试试了），并且在这里使用的也不会太复杂（应该）。如果各位有对多线程有疑惑或者有兴趣，如果是计算机专业的并且学过操作系统的可以去看这本关于 C++ 多线程的书：《C++ Concurrency in Action》，这本书从浅入深地进行讲解，很详细，有了操作系统关于线程的知识就容易看懂，上手也比较容易，我就是看了这本书才有胆子试试水的。我也不知道如果没有看过操作系统看这个容易不，你们也可以去试一试，如果感觉困难的话看一看操作系统的线程部分，然后再看。

&ensp;&ensp;&ensp;&ensp;好了，也就不废话了，下面开始编写

### 服务端
&ensp;&ensp;&ensp;&ensp;首先对服务端进行编写，在上一篇的代码中进行修改添加。添加的部分是多线程部分。

&ensp;&ensp;&ensp;&ensp;首先我们需要把多线程部分和 vector 容器的头文件添加进来：

```c++
#include <thread>
#include <vector>
```

&ensp;&ensp;&ensp;&ensp;添加相应的变量，考虑到每有一个客户端进行连接我们就需要新开一个线程专门对其进行服务，是动态变化的，所以我们使用指针,并且将线程在后台进行运行，这样我们就不用再对其进行管理，它自个按照代码运行就行了。而考虑到指针的内存释放问题，我们使用 vector 容器将每个新建的线程指针进行保存，在程序结束的时候释放到其占用的内存，相应的代码如下：

```c++
//定义多线程指针，用于创建线程
std::thread* t;
//用于线程的管理，保存创建的多线程指针，程序结束时释放占用的内存
std::vector<std::thread*> tManage;

//创建新的线程，并加入容器中，并将线程后台运行
t = new std::thread(server, sAccept);//server是线程运行函数，sAccept是已经与客户端连接的套接字
tManage.push_back(t);
t->detach();
```

&ensp;&ensp;&ensp;&ensp;接下来是线程运行函数，就是上面线程创建中的 server：其主要的内容是在第一次连接时候发送连接的信息给客户端，就想上一篇文章中的那样，然后我们用一个无限循环，在循环里面接收客户端发送来的信息并打印出来，具体代码如下：

```c++
//多线程函数，创建的多线程运行此函数
void server(SOCKET s) {
    SOCKET socket = s;
    struct sockaddr_in ser, cli;//网络地址
    int iSend, iRecv;
    char buf[1024] = "I am a server";

    //显示客户端的 IP 信息
    char clibuf[20] = { '\0' };
    inet_ntop(AF_INET, (void*)&cli.sin_addr, clibuf, 16);
    std::cout << "Accept client IP:" << clibuf << ":" << ntohs(cli.sin_port) << std::endl;

    //发送信息给客户端
    iSend = send(socket, buf, sizeof(buf), 0);
    if (iSend == SOCKET_ERROR) {
        std::cout << "send() Failed\n";
    }
    else if (iSend == 0) {
        std::cout << "send() Zero\n";
    }
    else {
        std::cout << "Send byte:" << iSend << std::endl;
        std::cout << "----------------------------------\n";
    }

    //使用循环不断接受客户端发送来的信息并显示
    while (true) {
        iRecv = recv(socket, buf, sizeof(buf), 0);
        if (iRecv == 0) {
            //std::cout << "recv() Zero\n";
        }
        else if (iRecv == SOCKET_ERROR) {
            std::cout << "recv() Failed\n";
        }
        else {
            std::cout << "recv() data from server:" << buf << std::endl;
        }
    }
}
```

### 客户端
&ensp;&ensp;&ensp;&ensp;客户端改动的相应较少，也不需要多线程。在上一篇的程序中客户端接收完服务端发送来的信息以后就关闭了套接字，断开了连接，结束了程序。而我们要测试一下我们的多线程是否有用就不能让它接收完消息后就退出了，所以在接收消息的后面我们加了一个循环让其不断的发送消息：我们定义一个数字，这个数字在循环中不断的递增并发送给服务端。而但我们在不同的时间启动多个客户端程序时，这个数字是不一样的，如果在服务端显示正确，那么我们的多线程就是可以使用的。代码如下：

```c++
    //对服务端发送指令或者数据
    int index = 0;//此数不断递增，然后发送给服务端，用于验证多线程是否有用
    while (true) {
        //输入相应的数据发送给服务端
        //std::cout << ">>";
        //std::cin >> buf;

        //将数字转换为字符串
        _itoa_s(index, buf, 10);
        ++index;

        //发送数据到服务端
        iSend = send(sClient, buf, sizeof(buf), 0);
        if (iSend == SOCKET_ERROR) {
            std::cout << "send() Failed\n";
        }
        else if (iSend == 0) {
            std::cout << "send() Zero\n";
        }
        else {
            std::cout << "Send byte:" << iSend << std::endl;
            std::cout << "----------------------------------\n";
        }
    }
```

### 完整代码如下：
#### 服务端：
```c++
#include <Winsock2.h>
#include <Ws2tcpip.h>
#include <iostream>
#include <thread>
#include <vector>
#pragma comment(lib,"ws2_32.lib")

//多线程函数，创建的多线程运行此函数
void server(SOCKET s);

void main()
{
    //定义相关的数据
    int iPort = 5050;//定义其端口
    WSADATA wsaData;//Winsock 的启动参数
    SOCKET sListen, sAccept;//套接口关键字,分别用于监听和接收连接
    int iLen;
    int iSend;
    char buf[] = "I am a server";
    struct sockaddr_in ser, cli;//网络地址
    //定义多线程指针，用于创建线程
    std::thread* t;
    //用于线程的管理，保存创建的多线程指针，程序结束时释放占用的内存
    std::vector<std::thread*> tManage;


    std::cout << "----------------------------\n";
    std::cout << "Server waitting\n";
    std::cout << "----------------------------\n";

    //启动winSocket
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) {
        std::cout << "Failed to load Winsock.\n";
        return;
    }

    //创建Socket
    sListen = socket(AF_INET, SOCK_STREAM, 0);
    if (sListen == INVALID_SOCKET) {
        std::cout << "socket() Failed:" << WSAGetLastError() <<"\n";
        return;
    }

    //绑定IP地址
    ser.sin_family = AF_INET;
    ser.sin_port = htons(iPort);
    ser.sin_addr.s_addr = htonl(INADDR_ANY);
    if (bind(sListen, (LPSOCKADDR)&ser, sizeof(ser)) == SOCKET_ERROR) {
        std::cout << "bind() Failed\n";
        return;
    }

    //监听
    if (listen(sListen, 5) == SOCKET_ERROR) {
        std::cout << "listen() Failed\n";
        return;
    }

    iLen = sizeof(cli);//获取客户端网络地址的长度

    //接受连接和发送欢迎信息
    //用循环使程序一直运行
    while (true) {
        //接收连接
        sAccept = accept(sListen, (struct sockaddr*)&cli, &iLen);
        if (sAccept == INVALID_SOCKET) {
            std::cout << "accept() Failed\n";
            break;
        }

        //创建新的线程，并加入容器中，并将线程后台运行
        t = new std::thread(server, sAccept);
        tManage.push_back(t);
        t->detach();
    }

    //释放指针占用的内存
    for (int i = 0; i < tManage.size(); i++) {
        delete(tManage[i]);
    }

    //关闭监听
    closesocket(sListen);
    //关闭 Winsock
    WSACleanup();
}

//多线程函数，创建的多线程运行此函数
void server(SOCKET s) {
    SOCKET socket = s;
    struct sockaddr_in ser, cli;//网络地址
    int iSend, iRecv;
    char buf[1024] = "I am a server";

    //显示客户端的 IP 信息
    char clibuf[20] = { '\0' };
    inet_ntop(AF_INET, (void*)&cli.sin_addr, clibuf, 16);
    std::cout << "Accept client IP:" << clibuf << ":" << ntohs(cli.sin_port) << std::endl;

    //发送信息给客户端
    iSend = send(socket, buf, sizeof(buf), 0);
    if (iSend == SOCKET_ERROR) {
        std::cout << "send() Failed\n";
    }
    else if (iSend == 0) {
        std::cout << "send() Zero\n";
    }
    else {
        std::cout << "Send byte:" << iSend << std::endl;
        std::cout << "----------------------------------\n";
    }

    //使用循环不断接受客户端发送来的信息并显示
    while (true) {
        iRecv = recv(socket, buf, sizeof(buf), 0);
        if (iRecv == 0) {
            //std::cout << "recv() Zero\n";
        }
        else if (iRecv == SOCKET_ERROR) {
            std::cout << "recv() Failed\n";
        }
        else {
            std::cout << "recv() data from server:" << buf << std::endl;
        }
    }
}
```

#### 客户端
```c++
#include <Winsock2.h>
#include <WS2tcpip.h>
#include <iostream>
#pragma comment(lib,"ws2_32.lib")

void main()
{
    //定义相应的数据
    WSADATA wsaData;
    SOCKET sClient;
    int iPort = 5050;//对应的服务端的端口
    int iLen, iSend;
    char buf[1024];
    struct sockaddr_in ser;

    //启动 winSocket
    memset(buf, 0, sizeof(buf));
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) {
        std::cout << "Failed to load Winsock\n";
        system("pause");
        return;
    }

    //char addr[20] = { '\0' };
    //std::cin >> addr;//输入服务端 IP 地址
    char addr[20] = "192.168.1.119";

    //创建Socket
    ser.sin_family = AF_INET;
    ser.sin_port = htons(iPort);
    inet_pton(AF_INET, addr, (void*)&ser.sin_addr.s_addr);
    sClient = socket(AF_INET, SOCK_STREAM, 0);
    if (sClient == INVALID_SOCKET) {
        std::cout << "socket() Failed\n";
        system("pause");
        return;
    }

    //连接并进行简单的操作
    if (connect(sClient, (struct sockaddr*)&ser, sizeof(ser)) == INVALID_SOCKET) {
        std::cout << "connect() Failed\n";
        system("pause");
        return;
    }
    else {
        //接收服务端发送的数据
        iLen = recv(sClient, buf, sizeof(buf), 0);
        if (iLen == 0) {
            system("pause");
            return;
        }
        else if (iLen == SOCKET_ERROR) {
            std::cout << "recv() Failed\n";
            system("pause");
            return;
        }
        std::cout << "recv() data from server:" << buf << std::endl;
    }

    //对服务端发送指令或者数据
    int index = 0;//此数不断递增，然后发送给服务端，用于验证多线程是否有用
    while (true) {
        //输入相应的数据发送给服务端
        //std::cout << ">>";
        //std::cin >> buf;

        //将数字转换为字符串
        _itoa_s(index, buf, 10);
        ++index;

        //发送数据到服务端
        iSend = send(sClient, buf, sizeof(buf), 0);
        if (iSend == SOCKET_ERROR) {
            std::cout << "send() Failed\n";
        }
        else if (iSend == 0) {
            std::cout << "send() Zero\n";
        }
        else {
            std::cout << "Send byte:" << iSend << std::endl;
            std::cout << "----------------------------------\n";
        }
    }

    //关闭连接并退出
    closesocket(sClient);
    //关闭 Winsock
    WSACleanup();
    
    system("pause");
}
```

### 运行
&ensp;&ensp;&ensp;&ensp;首先启动服务端，然后启动多个客户端，因为启动的时间不一样，所以各个客户端在服务端显示的数字是相差还是比较大的