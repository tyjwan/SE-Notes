# Windows10 powershell 中使用vim
***
## 步骤
- 1.安装vim，记住安装的路径
- 2.打开powershell，输入下面的命令：

```sh
Set-ExecutionPolicy RemoteSigned
new-item -path $profile -itemtype file -force
```

- 3.打开第二步显示的文件夹路径下的Microsoft.PowerShell_profile.ps1之类的文件，进行编辑，输入下面的内容：（之前配置过终端自动补全的话，将下面的东西添加到最后即可）

```bash
set-alias vim "F:\Software\Vim\vim82\vim.exe" # 此处为vim的安装路径
 
Function Edit-Profile
{
    vim $profile
}
 
Function Edit-Vimrc
{
    vim $HOME\_vimrc
}
```

- 4.重启sh后即可使用