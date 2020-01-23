 # Nagios 客户端安装指南
***
## 一、相应命令安装
```
sudo useradd nagios
sudo apt-get update
sudo apt-get install build-essential libgd2-xpm-dev openssl libssl-dev unzip

cd ~
curl -L -O http://nagios-plugins.org/download/nagios-plugins-2.2.1.tar.gz
tar zxf nagios-plugins-*.tar.gz
cd nagios-plugins-*
./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl
make
sudo make install

cd ~
curl -L -O https://github.com/NagiosEnterprises/nrpe/releases/download/nrpe-3.2.1/nrpe-3.2.1.tar.gz
tar zxf nrpe-*.tar.gz
cd nrpe-*
./configure --enable-command-args --with-nagios-user=nagios --with-nagios-group=nagios --with-ssl=/usr/bin/openssl --with-ssl-lib=/usr/lib/x86_64-linux-gnu
make all
sudo make install
sudo make install-config
sudo make install-init
```

## 二、相应文件配置
修改文件 /usr/local/nagios/etc/nrpe.cfg，填上服务端nagios的IP地址,第二个填上客户端本机的

```
allowed_hosts=127.0.0.1,::1,your_nagios_server_private_ip
server_address=monitored_server_private_ip
```

启动和查看客户端

```
sudo ufw allow 5666/tcp 
sudo systemctl start nrpe.service
sudo systemctl status nrpe.service
```

正常的输出如下：

```
Output
...
Oct 16 07:10:00 nagios systemd[1]: Started Nagios Remote Plugin Executor.
Oct 16 07:10:00 nagios nrpe[14653]: Starting up daemon
Oct 16 07:10:00 nagios nrpe[14653]: Server listening on 0.0.0.0 port 5666.
Oct 16 07:10:00 nagios nrpe[14653]: Server listening on :: port 5666.
Oct 16 07:10:00 nagios nrpe[14653]: Listening for connections on port 5666
Oct 16 07:10:00 nagios nrpe[14653]: Allowing connections from: 127.0.0.1,::1,207.154.249.232
```

## 三、测试
在服务端输入下面命令进行测试，最后填客户端IP地址

```
/usr/local/nagios/libexec/check_nrpe -H remote_host_ip
```

正常的输出如下：

```
Output
NRPE v3.2.1
```

在服务端新建文件 /usr/local/nagios/etc/servers/your_monitored_server_host_name.cfg，命名为客户端IP地址

```
define host {
        use                             linux-server
        host_name                       your_monitored_server_host_name
        alias                           My client server
        address                         your_monitored_server_private_ip
        max_check_attempts              5
        check_period                    24x7
        notification_interval           30
        notification_period             24x7
}

define service {
        use                             generic-service
        host_name                       your_monitored_server_host_name
        service_description             CPU load
        check_command                   check_nrpe!check_load
}
```

重启nagios，访问相应的网页就可以看到新添加的主机了

```
sudo systemctl restart nagios
```

## 参考链接
- [How To Install Nagios 4 and Monitor Your Servers on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-nagios-4-and-monitor-your-servers-on-ubuntu-16-04)
- [(No output on stdout) stderr: execvp(/usr/local/nagios/libexec/check_ping) in ubuntu](https://askubuntu.com/questions/817827/no-output-on-stdout-stderr-execvp-usr-local-nagios-libexec-check-ping-in-ub)
- [Nagios (No output on stdout) stderr: /usr/local/nagios/libexec/check_nt: 1: /usr](https://community.spiceworks.com/topic/551475-nagios-no-output-on-stdout-stderr-usr-local-nagios-libexec-check_nt-1-usr)