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
```

## 参考链接
- [Linux通过iptables端口转发访问内网服务器上的内网服务](http://hi.ktsee.com/635.html)