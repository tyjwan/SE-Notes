# vs2010中git配置
***
关键字： *VS* *git*

## 简单步骤
### 下载安装  git 相关插件
&ensp;&ensp;&ensp;&ensp;这个安装没什么难度，安装的相应设置一路默认即可。稍微记住程序的安装路径。

[git for windows](https://www.baidu.com/link?url=MNY53iQSgfqi9R7_k5Q_Q0M5QZF5QUBWGKT36rEmqF47g-jBTEibUFbgw_3St66cVDFJS_1edDNSaxf7gx6cbQDuAIoYjvBnu63KnHkJqu7&wd=&eqid=bdd6fae90007896a0000000357a1a64b)

[git extensions](https://sourceforge.net/projects/gitextensions/)

[git for vs2010](https://visualstudiogallery.msdn.microsoft.com/63a7e40d-4d71-4fbb-a23b-d262124b8f4c/file/29105/46/GitSccProvider.vsix)

### 相关设置
&ensp;&ensp;&ensp;&ensp;首先设置自己 git 的用户名和邮箱（没有的去申请一个），简单的在桌面上右键就会在菜单选项中看到看到 Git Bash Here,选择打开 git bash，如下图：

![](./photo/gitVS2010/1.png)

&ensp;&ensp;&ensp;&ensp;然后配置你的用户名和邮箱，输入下面的命令：

    git config --global user.name "这里输入你的用户名"
    git config --global user.email "这里输入你邮箱"

&ensp;&ensp;&ensp;&ensp;如下图所示：

![](./photo/gitVS2010/2.png)

&ensp;&ensp;&ensp;&ensp;接下来是配置 Git Extensions，右键菜单中有 Git Extensions 选择设置，弹出如下图的设置界面，按照相应的提示去设置即可；然后点击界面中的 SSH选择 “指定的SSH客户端”选择 OpenSSH，OK。

![](./photo/gitVS2010/3.png)

&ensp;&ensp;&ensp;&ensp;最后是 VS2010 的配置，进入VS的选项配置界面，选择 Source Control，选择当前源代码管理插件为 Git Source Control Provider，如下图；然后选择 Source Control 下的 Git Source Control Provider(如果没有，则可能需要重启一下VS),然后按照相应的提示进行配置，如下图所示：

![](./photo/gitVS2010/4.png)
![](./photo/gitVS2010/5.png)

### 新建仓库及推送至远程仓库
&ensp;&ensp;&ensp;&ensp;首先需要初始化项目仓库：选择相应项目后右键菜单中有 Create Git Repository，点击后便会初始化一个项目仓库，如下图：

![](./photo/gitVS2010/6.png)

&ensp;&ensp;&ensp;&ensp;接下来如果项目中有文件就可以提交到本地仓库和远程仓库中：选中项目右键菜单中有 Git（如果提交过或克隆过项目的话后面后有当前分支的名称），后面的选项中就有 Commit 这个选项，点中后进入添加提交界面，如下图所示：

![](./photo/gitVS2010/7.png)

&ensp;&ensp;&ensp;&ensp;在上图中左上角是项目没有进行添加跟踪的文件（即没有提交的文件），当然开始时时里面许多文件读书不必要的，可以通过设置 Git 的一个配置文件来过滤一些永远不会添加的文件，文件太多的时候这个就比较有用，现在文件少就将就了（其实我现在也不怎么用得好）。目前就手动选择需要添加的文件，比如那些自己写的 .h .cpp 文件还有 .sln 工程配置文件（上传这三个其实就可以了，别人有这些文件就可以打开你的项目了),点击载入把上面的这些文件添加上去，如下图所示：

![](./photo/gitVS2010/8.png)

&ensp;&ensp;&ensp;&ensp;接下来填写提交信息（这个提交信息必须填写，不然提交不了），这个提交在恢复代码时候比较有用的，所以提交的时候尽量填写一些有用的信息，比如这次提交完成了什么功能或有哪些变化，怎样在恢复代码的时候可以根据这些信息选择恢复到什么时候的代码样子。填写完提交信息就可以选择提交了（提交就是提交到本地的项目仓库中，恢复好像也是从这个里面恢复，不知道远程仓库中可不可以，毕竟玩的世界也不长，嘿嘿），推送就是提交到远程仓库，第一次就点击提交到提交并推送吧。
&ensp;&ensp;&ensp;&ensp;点击完后会先进行本地的提交，然后出现远程仓库推送界面，如下图所示：

![](./photo/gitVS2010/9.png)

&ensp;&ensp;&ensp;&ensp;我们先点击管理远程来添加我们的远程仓库地址,如下图，其中的名称随便填，网站填写项目的网站，然后保存更改。

![](./photo/gitVS2010/10.png)

&ensp;&ensp;&ensp;&ensp;下面的分支我也接触不多，毕竟平时玩的时候是自己一个人，这个分支的意义大概是：每个人是需要一个不同的分支的，修改编辑完后可以退送到自己的分支也可以推送到主分支。下面的拉取可以把远程仓库的文件复制到本地来。比如远程仓库中有一个新的类文件你没有，你使用拉取就可以把它复制到本地来，但你还是有自己手动添加这个类文件（这个比较麻烦，我没找到自动添加的方法）。推送就是把你本地的代码与远程的代码进行合并，让远程的代码和你本地的保持同步，推送成功就大概如下图：

![](./photo/gitVS2010/11.jpg)

&ensp;&ensp;&ensp;&ensp;这时刷新GitHub中的页面可以看到项目已经添加到GitHub中了：

![](./photo/gitVS2010/12.jpg)

### 从GitHub拉代码到本地
&ensp;&ensp;&ensp;&ensp;打开VS2010，点击Git菜单的Clone repository：

![](./photo/gitVS2010/13.jpg)

&ensp;&ensp;&ensp;&ensp;在弹出的克隆对话框中输入相应的信息,然后代码就拉倒了相应的盘中，可以打开VS的 .sln文件进行加载项目使用了

![](./photo/gitVS2010/14.jpg)

## 使用过程中遇到的问题及解决办法
### 不能push的问题解决：
    但可能你不能提交项目，接下来需要从下面的方法去尝试解决：
    1.配置你的git，按照下面的博客进行设置：
    git bash 按照上面我建议的安装后在桌面右键后会有
    [http://blog.csdn.net/hustpzb/article/details/8230454/](http://blog.csdn.net/hustpzb/article/details/8230454/)
    2.配置好以后你可能也不行，那就可能是你在新建项目时候自动生成的README.md造成的，那你
    就需要先pull一下，这个在vs的push是上面，下面是参考源：
    [http://jingyan.baidu.com/article/f3e34a12a25bc8f5ea65354a.html](http://jingyan.baidu.com/article/f3e34a12a25bc8f5ea65354a.html)

## 参考链接
[在VS2010中使用Git(转)](http://www.cnblogs.com/smallfa/p/4984547.html)