# 压测工具 superbenchmarker 安装使用
***
## 安装
记得使用管理员进行运行，不然没有权限进行安装

```bash
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

choco install superbenchmarker
```

## 使用
- c：并发数
- n：单个并发的次数
- N：测试持续时间

```bash
sb -u http://192.168.101.104:81/group1 -c 20 -N 120
```