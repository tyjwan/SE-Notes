# iptables 端口转发和映射
***
```sh
; 添加
iptables -t nat -A PREROUTING -p tcp -m tcp --dport 40001 -j DNAT --to-destination 172.19.104.19:27017
iptables -t nat -A POSTROUTING -d 172.19.104.19/32 -p tcp -j SNAT --to-source 172.19.104.28

; 删除
iptables -t nat -D PREROUTING -p tcp -m tcp --dport 40001 -j DNAT --to-destination 172.19.104.19:27017
iptables -t nat -D POSTROUTING -d 172.19.104.19/32 -p tcp -j SNAT --to-source 172.19.104.28

; 保存并生效
service iptables save
service iptables restart

b.不同端口转发
; 添加
iptables -t nat -A PREROUTING -p tcp -i enp6s0 --dport 443 -j DNAT --to 172.19.104.28:27017
iptables -t nat -A POSTROUTING -j MASQUERADE
service iptables save
service iptables restart

; 删除
iptables -t nat -D PREROUTING -p tcp -i enp6s0 --dport 443 -j DNAT --to 172.19.104.28:27017
iptables -t nat -D POSTROUTING -j MASQUERADE
service iptables save
service iptables restart

; 保存并生效
service iptables save
service iptables restart

; 查看规则
cat /etc/sysconfig/iptables

yum install iptables-services
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT

iptables -I INPUT -s 185.10.68.91 -j DROP
185.10.68.91
iptables -I INPUT -s master.clminer.ru -j DROP
service iptables save
service iptables restart

-A IN_public_allow -p tcp -m tcp --dport 22 -m conntrack --ctstate NEW -j ACCEPT
-A IN_public_allow -p tcp -m tcp --dport 29001 -m conntrack --ctstate NEW -j ACCEPT
-A IN_public_allow -p tcp -m tcp --dport 29002 -m conntrack --ctstate NEW -j ACCEPT

应该在默认的22端口这条规则的下面添加端口新规则，然后重启防火墙才能生效。如下：
```

## 参考链接
- [Linux通过iptables端口转发访问内网服务器上的内网服务](http://hi.ktsee.com/635.html)
- [centos下配置防火墙端口失败](https://blog.csdn.net/taiyang1987912/article/details/40189371)
- [linux下用iptables做本机端口转发方法](https://blog.csdn.net/zzhongcy/article/details/42738285)
- [CentOS 7 使用iptables 开放端口](https://blog.csdn.net/kongfanyu/article/details/80579493)
- [将规则添加到防火墙中，总是端口无法开启](https://blog.csdn.net/taiyang1987912/article/details/40189371)