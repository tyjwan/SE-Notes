# openvas
***
## 官方文档阅读
- gvm-cli：205
- gvm-tool：243

## 启动使用
&ensp;&ensp;&ensp;&ensp;openvas采用docker方式进行运行，首先运行下面的命令启动其相应的docker后进行docker。

&ensp;&ensp;&ensp;&ensp;进入docker以后运行相应的命令启动相应的服务：

```
openvassd -a 0.0.0.0 -p 9392
openvasmd -a 0.0.0.0 -p 9393
```

## 
omp -h 127.0.0.1 -p 9390 -u admin -w admin -C --name=Scan6 --config=daba56c8-73ec-11df-a475-002264764cea --target=e27f9e93-7c56-4d0b-8c4a-c4e3cda46f73
omp -h 127.0.0.1 -p 9390 -u admin -w admin -S 2a3877a8-b576-4ac6-ba68-320d879f5353

## 参考链接
[Automation of Vulnerability Assessments with OpenVAS](https://elasticsecurity.wordpress.com/2013/07/18/automation-of-vulnerability-assessments-with-openvas/)
[所下载的docker镜像地址](https://cr.console.aliyun.com/?spm=5176.1972343.0.2.Pkpn66#/imageDesc/cn-hangzhou/secends/openvas/detail)
[openvas 操作英文文档](http://docs.greenbone.net/#api_documentation)
[OMP: OpenVAS Management Protocol](http://www.openvas.org/omp-1-0.html)
[Automating Vulnerability Scans](https://isc.sans.edu/forums/diary/Automating+Vulnerability+Scans/20685/)
[Performing OpenVAS Vulnerability Scans: Managing Infrastructure with RapidIdentity, Part 5](http://blog.identityautomation.com/managing-infrastructure-with-rapididentity-part-5-performing-openvas-vulnerability-scans)
[openvas docker官方下载地址](https://hub.docker.com/r/atomicorp/openvas/)
[阿里云docker仓库 ](https://blog.csdn.net/qq_30259339/article/details/52400673)
[项目所用的docker源](https://dev.aliyun.com/detail.html?spm=5176.1972343.2.4.Etc3Mr&repoId=6635)
[OpenVAS image for Docker](https://github.com/mikesplain/openvas-docker)