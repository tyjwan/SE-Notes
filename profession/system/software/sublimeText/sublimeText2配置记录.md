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
- MarkdownPreviewing:在前面加[TOC]后按Ctrl+B生成带目录的HTML文件
- OmniMarkupPreviewer：Markdown文件浏览和转换
- IMESupport:输入法光标不跟随问题解决差距
- ConvertToUTF8:Windows 乱码问题
- [](https://github.com/vkocubinsky/SublimeTableEditor)：表格插件
- [sublime3](https://www.cnblogs.com/syfwhu/p/5123637.html)
- 注册码：

 ```
 —– BEGIN LICENSE —–
TwitterInc
200 User License
EA7E-890007
1D77F72E 390CDD93 4DCBA022 FAF60790
61AA12C0 A37081C5 D0316412 4584D136
94D7F7D4 95BC8C1C 527DA828 560BB037
D1EDDD8C AE7B379F 50C9D69D B35179EF
2FE898C4 8E4277A8 555CE714 E1FB0E43
D5D52613 C3D12E98 BC49967F 7652EED2
9D2D2E61 67610860 6D338B72 5CF95C69
E36B85CC 84991F19 7575D828 470A92AB
—— END LICENSE ——
```
