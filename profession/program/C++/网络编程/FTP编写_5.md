# FTP 编程 5 文件传输与目录切换
***
&ensp;&ensp;&ensp;&ensp;接下来说最后一个 FTP 实现的功能点了，文件传输加上前面几篇文件介绍的功能实现组合起来基本就能实现一个 FTP 程序了，但这个只是简单的实现，和现实中能使用的还有很多要完善，但现在的时间比较紧张，没有太多时间弄了，还有好多的东西要学。所以如果各位有兴趣的话，可以自己去完善，反正要用的基本就是这些，和其他组合一下，自己摸索完善一下，比如弄个图像界面之类的。

### 文件传输
&ensp;&ensp;&ensp;&ensp;本文以客户端上传文件为例，但客户端下载原理基本雷同，会其一就能写出另外一个的。文件上传的大概如下：

- l.首先客户端向服务端发送命令说明自己要想服务端上传文件了
- 2.服务端收到客户端的命令，在本地创建相应的文件
- 3.客户端再向服务端发送文件的大小信息，这个信息是非常有用的，用于服务端如何判断文件传输是否完毕 
- 4.服务端收到文件大小信息，用于统计
- 5.客服端打开相应的文件进行读取传输，用一个循环，在循环里面每次读取1024字节并传输，也就是分段传输，不是一次性读取完并传完
- 6.服务端用一个循环接收文件信息，每次接收1024字节（与客户端同步），并统计相应的剩下的文件大小，当文件大小等于小于0时传输完毕
- 7.客户端读取完毕，关闭文件，等待下一命令
- 8.服务端接收完毕，关闭并保存文件，等待客户端的下一个命令

&ensp;&ensp;&ensp;&ensp;具体的代码如下：

#### 客户端文件上传代码
```c++

bool Client::uploadFile(std::string filePath)
{
    //打开文件
    char* workPath = _getcwd(NULL, 0);
    if (workPath == NULL) {
        std::cout << "Get current work path failed\n";
    }
    else {
        std::cout << workPath << std::endl;
    }
    std::string absolutePath = workPath;
    absolutePath += "\\";
    absolutePath += filePath;
    //使用文件I/O进行读取，这样适用于所以类型文件
    std::ifstream file(absolutePath.c_str(), std::ios_base::binary);

    if (!file) {
        std::cout << "file open failed.\n";
        return false;
    }
    else {
        std::cout << "open successful\n";
    }

    //获取文件大小
    long fileBegin = file.tellg();
    file.seekg(0, std::ios_base::end);
    long fileEnd = file.tellg();
    int fileSize = fileEnd - fileBegin;
    std::cout << "File size is " << fileEnd-fileBegin << " bytes" << std::endl;
    //将文件大小发送给服务端
    _itoa_s(fileSize, buf, 10);
    send(client, buf, 1024, 0);

    //读取文件内容
    std::cout << "开始读取文件内容" << std::endl;
    file.seekg(0, std::ios_base::beg);
    int sendCount = 0;
    while(!file.eof()) {
        memset(buf, 0, 1024);
        file.read(buf, 1024);

        int len = send(client, buf, maxSize, 0);
        if (len == SOCKET_ERROR) {
            std::cout << "Sending occur error\n";
        }

        sendCount += 1024;
        std::cout << "Send " << sendCount << "/" << fileSize << " bytes\n";
    }

    //关闭文件
    file.close();
    std::cout << "Readed file succesful\n";

    return false;
}
```

#### 服务端文件上传接收代码
```c++

bool Server::receiveFile(std::string filename)
{
    //创建与上次文件名相同的文件
    std::string filePath = storePath + filename;
    std::ofstream storeFile(filePath, std::ios_base::binary);
    if (!storeFile) {
        std::cout << "Create file failed\n";
    }

    //先接收文件大小信息
    memset(buf, 0, maxSize);
    recv(server, buf, 1024, 0);
    int fileSize = atoi(buf);
    std::cout << "File's size is:" << fileSize << std::endl;

    int len = 1;
    int recvCount = 0;
    //统计文件传输剩下的大小
    int remain = fileSize;
    while (true) {
        memset(buf, 0, maxSize);
        len = recv(server, buf, 1024, 0);
        storeFile.write(buf, 1024);
        if (len == SOCKET_ERROR) {
            std::cout << "Receive occur error\n";
        }

        //如果剩下的小于等于0，说明文件传输完毕，退出循环
        remain -= 1024;
        if(remain <= 0) {
        //if (strlen(buf) == 0) {
            break;
        }

        recvCount += strlen(buf);
        std::cout << "Receive " << recvCount << " bytes\n";
    }

    //关闭并保存文件
    storeFile.close();
    std::cout << "Write file successful\n";

    return false;
}
```

### 目录切换
&ensp;&ensp;&ensp;&ensp;这个这里大概提一下就行，其实就是传输一些文字信息而已，就socket方面而已是没有多大难道的，只要在客户端和服务端建立相应的路径变量，进行相应的同步和保存即可。获取文件目录的信息需要用到Windows的目录处理，把处理后的目录信息存到字符串中，再发送到客户端，客户端再进行处理和显示就行

### 最后
&ensp;&ensp;&ensp;&ensp;FTP的技术点大概就这些，祝各位玩的愉快

### 源码地址
[源码地址](https://github.com/lw1243925457/FTP)
