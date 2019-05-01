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
```

## 参考链接
- [Linux通过iptables端口转发访问内网服务器上的内网服务](http://hi.ktsee.com/635.html)
- [centos下配置防火墙端口失败](https://blog.csdn.net/taiyang1987912/article/details/40189371)