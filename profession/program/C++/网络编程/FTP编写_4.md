# FTP 编写 4：命令解析
***
&ensp;&ensp;&ensp;&ensp;我们知道在现实生活中使用的 FTP 是应答式的，客户端和服务端按照一定的规定进行交流，不是随便弄的，在上几篇中的 FTP 没有人机交互的功能。所以这篇文章的主要内容是按照平时 FTP 的交流规则重构一下整个工程的代码，并加入命令解析功能。

### 命令解析
&ensp;&ensp;&ensp;&ensp;这部分命令解析只是简单模拟一下 FTP 的，在 FTP 的规则中还有应答返回值的规定，现在把它省略，简单定义一下服务端和客户端的交流规则

&ensp;&ensp;&ensp;&ensp;现在的交流过程是这样的：在服务端和客户端启动以后，客户端向服务端发起连接，然后服务端向客户端发送一条欢迎信息；然后等待客户端发送相应的指令，根据指令进行答复和操作

```c++
//命令解析函数：将受到的命令进行分解，用字符分割函数分成指令和参数两部分
int Client::commandParse(char * instruck, std::string & paramter)
{
    std::string string(buf);
    std::cout << string;
    std::vector<std::string> command;
    //以空格为分隔符进行分割
    Tool::splitString(string, command, std::string(" "));

    //判断传入的参数个数是否正确
    if (command.size() != 2) {
        paramter = "";
        return -1;
    }

    //返回相应指令对应的数值
    if (command[0] == "STOR") {
        paramter = command[1];
        return 1;
    }
    if (command[0] == "RETR") {
        paramter = command[1];
        return 2;
    }

    //默认返回错误指令
    return 0;
}

//字符分割函数
void Tool::splitString(const std::string & input, std::vector<std::string>& output, std::string &delim)
{
    std::string::size_type pos1, pos2;
    pos1 = 0;
    pos2 = input.find(delim);

    while (std::string::npos != pos2)
    {
        output.push_back(input.substr(pos1, pos2 - pos1));

        pos1 = pos2 + delim.size();
        pos2 = input.find(delim, pos1);
    }
    if (pos1 != input.length())
        output.push_back(input.substr(pos1));
}
```

### 服务端重构
&ensp;&ensp;&ensp;&ensp;服务端的重构主要是将服务端运行的部分抽离出来写成一个类，类中包含了服务端的所有功能函数，并且在多线程中运行，服务端中的运行函数如下：

```c++
void Server::running()
{
    //与客户端进行连接以后就发送一个欢迎信息
    welcome();

    //然后进入无限循环，等待客户端的命令进行下一步操作
    while (true) {
        if (recv(server, buf, maxLen, 0) == 0) {
            std::cout << "recv() Faied!\n";
        }

        std::string paramter;
        int cmd = commandParse(buf, paramter);

        switch (cmd) {
        case 0:
            std::cout << "Command is invalid\n";
            break;

        case 1:
            std::cout << "Start receive the file from client.\n";
            receiveFile(paramter);
            break;

        case 2:
            std::cout << "Start send file to client.\n";
            sendFile(paramter);
            break;

        default:
            std::cout << "Command is invalid\n";
        }
    }
}
```

### 客户端重构
&ensp;&ensp;&ensp;&ensp;客户端也是将功能封装到一个类里面，但不需要多线程，运行函数如下：

```c++
void Client::running()
{
    memset(buf, 0, sizeof(buf));
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) {
        std::cout << "Failed to load Winsock\n";
        system("pause");
        return;
    }

    //char addr[20] = "113.54.167.16";
    char addr[20] = "192.168.1.119";

    //创建Socket
    ser.sin_family = AF_INET;
    ser.sin_port = htons(iPort);
    inet_pton(AF_INET, addr, (void*)&ser.sin_addr.s_addr);
    client = socket(AF_INET, SOCK_STREAM, 0);
    if (client == INVALID_SOCKET) {
        std::cout << "socket() Failed\n";
        system("pause");
        return;
    }

    //连接并进行简单的操作
    if (connect(client, (struct sockaddr*)&ser, sizeof(ser)) == INVALID_SOCKET) {
        std::cout << "connect() Failed\n";
        system("pause");
        return;
    }
    else {
        //接收服务端发送的数据
        iLen = recv(client, buf, sizeof(buf), 0);
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

    while (true) {
        //输入相应的数据发送给服务端
        std::cout << ">>";
        std::cin.getline(buf, 1024);

        std::string paramter;
        int cmd = commandParse(buf, paramter);

        switch (cmd) {
        case 0:
            std::cout << "Command is invalid\n";
            break;

        case 1:
            std::cout << "Start receive the file from client.\n";
            downloadFile(paramter);
            break;

        case 2:
            std::cout << "Start send file to client.\n";
            uploadFile(paramter);
            break;

        default:
            std::cout << "Command is invalid\n";
        }
    }
}
```

### 源代码
#### 客户端
```c++
//main.cpp
#pragma comment(lib,"ws2_32.lib")

#include "Client.h"

void main()
{
    Client client;
    client.running();
    system("pause");
}

//Client.h
#pragma once
#include <WinSock2.h>
#include <string>

class Client
{
public:
    Client();
    ~Client();

    void running();
    bool uploadFile(std::string file);
    bool downloadFile(std::string file);
    int commandParse(char* instruck, std::string &paramter);

private:
    SOCKET client;
    char buf[1024];
    //定义相应的数据
    WSADATA wsaData;
    int iPort = 5050;//对应的服务端的端口
    int iLen, iSend;
    struct sockaddr_in ser;

};

//Client.cpp
#include "Client.h"
#include "Tool.h"

#include <iostream>
#include <WS2tcpip.h>

Client::Client()
{
}


Client::~Client()
{
    //关闭连接并退出
    closesocket(client);
    //关闭 Winsock
    WSACleanup();
}

void Client::running()
{
    memset(buf, 0, sizeof(buf));
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) {
        std::cout << "Failed to load Winsock\n";
        system("pause");
        return;
    }

    //char addr[20] = "113.54.167.16";
    char addr[20] = "192.168.1.119";

    //创建Socket
    ser.sin_family = AF_INET;
    ser.sin_port = htons(iPort);
    inet_pton(AF_INET, addr, (void*)&ser.sin_addr.s_addr);
    client = socket(AF_INET, SOCK_STREAM, 0);
    if (client == INVALID_SOCKET) {
        std::cout << "socket() Failed\n";
        system("pause");
        return;
    }

    //连接并进行简单的操作
    if (connect(client, (struct sockaddr*)&ser, sizeof(ser)) == INVALID_SOCKET) {
        std::cout << "connect() Failed\n";
        system("pause");
        return;
    }
    else {
        //接收服务端发送的数据
        iLen = recv(client, buf, sizeof(buf), 0);
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

    while (true) {
        //输入相应的数据发送给服务端
        std::cout << ">>";
        std::cin.getline(buf, 1024);

        std::string paramter;
        int cmd = commandParse(buf, paramter);

        switch (cmd) {
        case 0:
            std::cout << "Command is invalid\n";
            break;

        case 1:
            std::cout << "Start receive the file from client.\n";
            downloadFile(paramter);
            break;

        case 2:
            std::cout << "Start send file to client.\n";
            uploadFile(paramter);
            break;

        default:
            std::cout << "Command is invalid\n";
        }
    }
}

bool Client::uploadFile(std::string file)
{
    return false;
}

bool Client::downloadFile(std::string file)
{
    return false;
}

int Client::commandParse(char * instruck, std::string & paramter)
{
    std::string string(buf);
    std::cout << string;
    std::vector<std::string> command;
    Tool::splitString(string, command, std::string(" "));

    if (command.size() != 2) {
        paramter = "";
        return -1;
    }

    if (command[0] == "STOR") {
        paramter = command[1];
        return 1;
    }
    if (command[0] == "RETR") {
        paramter = command[1];
        return 2;
    }

    return 0;
}
```

#### 服务端
```c++
//main.cpp
#include <Winsock2.h>
#include <Ws2tcpip.h>
#include <iostream>
#include <thread>
#include <vector>
#pragma comment(lib,"ws2_32.lib")

#include "Server.h"

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
    Server* server = NULL;


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
        //t = new std::thread(server, sAccept);
        //tManage.push_back(t);
        //t->detach();
        server = new Server(sAccept);
        t = new std::thread(&Server::running, server);
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

//Server.h
#pragma once

#include <Winsock2.h>
#include <string>

class Server
{
public:
    Server(SOCKET s);
    ~Server();

    //欢迎函数
    bool welcome();
    //服务端运行函数
    void running();
    //命令解析函数
    int commandParse(char* instruck, std::string &paramter);
    //文件接收函数
    bool receiveFile(std::string filename);
    //文件发送
    bool sendFile(std::string filename);

private:
    SOCKET server;
    int maxLen;
    char buf[1024];
};

//Server.cpp
#include "Server.h"
#include "Tool.h"

#include <iostream>
#include <vector>

Server::Server(SOCKET s)
    :server(s)
{
    maxLen = 1024;
}


Server::~Server()
{
}

bool Server::welcome()
{
    char welcome[1024] = "Welcome, my friend\n";

    //发送信息给客户端
    int iSend = send(server, welcome, sizeof(welcome), 0);
    if (iSend == SOCKET_ERROR) {
        std::cout << "send() Failed\n";
        return false;
    }

    return true;
}

void Server::running()
{
    welcome();

    while (true) {
        if (recv(server, buf, maxLen, 0) == 0) {
            std::cout << "recv() Faied!\n";
        }

        std::string paramter;
        int cmd = commandParse(buf, paramter);

        switch (cmd) {
        case 0:
            std::cout << "Command is invalid\n";
            break;

        case 1:
            std::cout << "Start receive the file from client.\n";
            receiveFile(paramter);
            break;

        case 2:
            std::cout << "Start send file to client.\n";
            sendFile(paramter);
            break;

        default:
            std::cout << "Command is invalid\n";
        }
    }
}

int Server::commandParse(char* instruck, std::string &paramter)
{
    std::string string(buf);
    std::cout << string;
    std::vector<std::string> command;
    Tool::splitString(string, command, std::string(" "));
    //std::cout << command[0] << " " << command[1] << std::endl;

    if (command.size() != 2) {
        paramter = "";
        return -1;
    }

    if (command[0] == "STOR") {
        paramter = command[1];
        return 1;
    }
    if (command[0] == "RETR") {
        paramter = command[1];
        return 2;
    }

    return -1;
}

bool Server::receiveFile(std::string filename)
{
    return false;
}

bool Server::sendFile(std::string filename)
{
    return false;
}
```

#### 工具类（字符分割方法，服务端与客户端完全一样)
```c++
//Tool.h
#pragma once
#include <string>
#include <vector>

class Tool
{
public:
    Tool();
    ~Tool();

    //字符串分割函数
    static void splitString(const std::string &input, std::vector<std::string> &output, std::string &delim);
};

//Tool.cpp
#include "Tool.h"

#include <sstream>
#include <istream>


Tool::Tool()
{
}


Tool::~Tool()
{
}

void Tool::splitString(const std::string & input, std::vector<std::string>& output, std::string &delim)
{
    std::string::size_type pos1, pos2;
    pos1 = 0;
    pos2 = input.find(delim);

    while (std::string::npos != pos2)
    {
        output.push_back(input.substr(pos1, pos2 - pos1));

        pos1 = pos2 + delim.size();
        pos2 = input.find(delim, pos1);
    }
    if (pos1 != input.length())
        output.push_back(input.substr(pos1));
}
```

#### 源代码地址
[https://github.com/lw1243925457/FTP](https://github.com/lw1243925457/FTP)