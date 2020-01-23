# FTP 编写 2：客户端与服务端的连接
***
&ensp;&ensp;&ensp;&ensp;首先编写客户端与服务端能进行简单的连接，这个较为容易：

&ensp;&ensp;&ensp;&ensp;服务端的编写流程是：启动 Winsock、建立套接字、绑定套接字、监听、接收连接、关闭连接、关闭套接字、关闭 Winsock

&ensp;&ensp;&ensp;&ensp;客户端的编写流程是：启动 Winsock、建立套接字、连接、关闭连接和套接字、关闭 Winsock

&ensp;&ensp;&ensp;&ensp;代码较为简单，具体如下：

### 服务端
```c++
#include <Winsock2.h>
#include <Ws2tcpip.h>
#include <iostream>
#pragma comment(lib,"ws2_32.lib")

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

        //显示客户端的 IP 信息
        char clibuf[20] = { '\0' };
        inet_ntop(AF_INET, (void*)&cli.sin_addr, clibuf, 16);
        std::cout << "Accept client IP:" << clibuf << ":" << ntohs(cli.sin_port) << std::endl;

        //发送信息给客户端
        iSend = send(sAccept, buf, sizeof(buf), 0);
        if (iSend == SOCKET_ERROR) {
            std::cout << "send() Failed\n";
            break;
        }
        else if (iSend == 0) {
            break;
        }
        else {
            std::cout << "Send byte:" << iSend << std::endl;
            std::cout << "----------------------------------\n";
        }

        //关闭连接
        closesocket(sAccept);
    }

    //关闭监听
    closesocket(sListen);
    //关闭 Winsock
    WSACleanup();
}
```

### 客户端
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
    int iLen;
    char buf[1024];
    struct sockaddr_in ser;

    //启动 winSocket
    memset(buf, 0, sizeof(buf));
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) {
        std::cout << "Failed to load Winsock\n";
        system("pause");
        return;
    }

    char addr[20] = { '\0' };
    std::cin >> addr;//输入服务端 IP 地址

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

    //关闭连接并退出
    closesocket(sClient);
    //关闭 Winsock
    WSACleanup();
    
    system("pause");
}
```

### 启动运行
&ensp;&ensp;&ensp;&ensp;需要注意的是在客户端输入的 IP 地址，这个地址需要输入本地电脑的 IP 地址，使用 cmd 调出 Windows 的 DOS 界面，输入 ipconfig 命令，找到 IPv4 地址，然后在 客户端进行填写即可