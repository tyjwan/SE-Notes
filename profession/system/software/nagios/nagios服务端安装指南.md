# nagios4 服务端安装指南（Ubuntu16.04）
***
## 一、依赖安装
```
sudo apt-get install wget build-essential apache2 php apache2-mod-php7.0 php-gd libgd-dev sendmail unzip
```

## 二、Nagios 4 安装
```
sudo useradd nagios
sudo groupadd nagcmd
sudo usermod -a -G nagcmd nagios
apt-get update
apt-get install build-essential libgd2-xpm-dev openssl libssl-dev unzip

cd ~
curl -L -O https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.3.4.tar.gz
tar zxf nagios-*.tar.gz
cd nagios-*
./configure --with-nagios-group=nagios --with-command-group=nagcmd
make all
sudo make install
sudo make install-commandmode
sudo make install-init
sudo make install-config
sudo /usr/bin/install -c -m 644 sample-config/httpd.conf /etc/apache2/sites-available/nagios.conf
sudo usermod -G nagcmd www-data
```

## 三、check_nrpe Plugin 安装（用户检测远程客户端）
```
cd ~
curl -L -O https://github.com/NagiosEnterprises/nrpe/releases/download/nrpe-3.2.1/nrpe-3.2.1.tar.gz
tar zxf nrpe-*.tar.gz
cd nrpe-*
./configure
make check_nrpe
sudo make install-plugin
```

## 四、插件安装
```
cd ~
curl -L -O http://nagios-plugins.org/download/nagios-plugins-2.2.1.tar.gz
tar zxf nagios-plugins-*.tar.gz
cd nagios-plugins-*
./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl
make
sudo make install
```

## 五、Nagios配置文件设置
编辑文件 /usr/local/nagios/etc/nagios.cfg，将下面的语句前面的#号去掉


```
...
#cfg_dir=/usr/local/nagios/etc/servers
...
```

改为：

```
cfg_dir=/usr/local/nagios/etc/servers
```

手动建立相应的目录

```
sudo mkdir /usr/local/nagios/etc/servers
```

编辑文件 sudo nano /usr/local/nagios/etc/objects/commands.cfg，在最后面加入下面的内容：

```
...
define command{
        command_name check_nrpe
        command_line $USER1$/check_nrpe -H $HOSTADDRESS$ -c $ARG1$
}
```

接下来运行下面的命令，重启相应的服务

```
sudo a2enmod rewrite
sudo a2enmod cgi
```

运行下面的第一天命令后输入相应的密码，命令最后面为账号名称，后面用于网页登录

```
sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
sudo ln -s /etc/apache2/sites-available/nagios.conf /etc/apache2/sites-enabled/
```

新建文件 /etc/systemd/system/nagios.service，写入下面的内容

```
[Unit]
Description=Nagios
BindTo=network.target

[Install]
WantedBy=multi-user.target

[Service]
Type=simple
User=nagios
Group=nagios
ExecStart=/usr/local/nagios/bin/nagios /usr/local/nagios/etc/nagios.cfg
```

首先启动Apache2后在启动nagios

```
sudo systemctl restart apache2
sudo systemctl enable /etc/systemd/system/nagios.service
sudo systemctl start nagios
```

输入下网址 http://nagios_server_public_ip/nagios 即可查看效果

## 六、DBI和DBD安装
在Ubuntu16.04中进行相应的搜索dbi和dbd后选择包含mysql字段的进行安装即可

## 七、ndo2db的安装
安装之前请确保MySQL的正确安装

```
cd /tmp
wget -O ndoutils.tar.gz https://github.com/NagiosEnterprises/ndoutils/releases/download/ndoutils-2.1.3/ndoutils-2.1.3.tar.gz
tar xzf ndoutils.tar.gz
cd /tmp/ndoutils-2.1.3/
./configure
make all
make install

cd db/
./installdb -u 'ndoutils' -p 'ndoutils_password' -h 'localhost' -d nagios
cd .. 
```

修改文件/usr/local/nagios/etc/ndo2db.cfg的数据库配置

```
db_user=ndoutils
db_pass=ndoutils_password
```

```
make install-config
mv /usr/local/nagios/etc/ndo2db.cfg-sample /usr/local/nagios/etc/ndo2db.cfg
sed -i 's/^db_user=.*/db_user=ndoutils/g' /usr/local/nagios/etc/ndo2db.cfg
sed -i 's/^db_pass=.*/db_pass=ndoutils_password/g' /usr/local/nagios/etc/ndo2db.cfg
mv /usr/local/nagios/etc/ndomod.cfg-sample /usr/local/nagios/etc/ndomod.cfg
```

启动安装脚本不知道为什么不能运行了，只能在startup文件夹中运行default-init了

接下来运行Apache2，在运行ndo2db，最后运行nagios

使用下面的命令查看数据库有数据既为成功：

```
echo 'select * from nagios.nagios_logentries;' | mysql -u ndoutils -p'ndoutils_password'
```

## 参考链接
- [如何在Ubuntu 16.04上安装Nagios服务器监控](https://www.howtoing.com/ubuntu-nagios/)
- [ndoutils download](https://sourceforge.net/projects/nagios/files/ndoutils-2.x/)
- [NDOUTILS Documentation Version 2.x](https://assets.nagios.com/downloads/nagioscore/docs/ndoutils/NDOUtils.pdf)
- [nagios 抓取数据插件ndoutils 学习](https://www.cnblogs.com/jiang-dh/p/6409390.html)
- [NDOUtils - Installing NDOUtils](https://support.nagios.com/kb/article/ndoutils-installing-ndoutils-406.html)