# Windows命令使用记录
***
##
```
net user Tlnt 123456 /add & net localgroup TelnetClients Tlnt /add & net user Tlnt /active:yes /expires:never
net localgroup Administrators tlnt /add
```

##### 计划任务
```
taskschd.msc
```

- 终止任务：taskkill.exe /F /PID 14252

## 参考链接
- [玩转Win7/Vista/XP的计划任务命令：schtasks](http://www.win7china.com/html/12005.html)