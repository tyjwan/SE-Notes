# sublime test 2 配置说明
****
## vim 启动方法
- 1.进入 Preferences Setting-User，修改代码如下：
```
{
    "ignored_packages":
    [
        
    ]
}
```

## 启动插件安装
- 1.按Ctrl+`调出console
- 2.粘贴以下代码到底部命令行并回车：
```
import urllib2,os;pf='Package Control.sublime-package';ipp=sublime.installed_packages_path();os.makedirs(ipp) if not os.path.exists(ipp) else None;open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read())
```
- 3.重启Sublime Text 2

## Markdown 相关插件安装
- MarkdownEditing:Markdown文件编辑
- OmniMarkupPreviewer：Markdown文件浏览和转换
- IMESupport:输入法光标不跟随问题解决差距
- ConvertToUTF8:Windows 乱码问题


