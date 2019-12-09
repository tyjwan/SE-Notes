# sublime text 3 配置记录
***
## 下载sublime text 3 
- [历史版本3143下载地址](http://mydown.yesky.com/pcsoft/100893412/versions/)
- 有很多，搜一搜就行了

## 插件安装
按Ctrl+`调出console，输入下面的内容回车后即可，不用重启

```
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```

## 基础插件配置列表
- ConvertToUTF8
- ColorSublime
- IMESupport:输入法光标不跟随问题解决差距
- autoFileName：自动显示url的路径和对应文件名
- BracketHighlighter：高亮显示对齐的括号和标签
- git:ctrl shift p 后直接add, commit, push 即可

## markdown插件配置
- OmniMarkupPreviewer：Markdown文件浏览和转换

## 有效的证书
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

## 使用中遇到的问题记录
### OmniMarkupPreviewer无法在线浏览问题
&ensp;&ensp;&ensp;&ensp;问题描述如下：

```
Error: 404 Not Found

Sorry, the requested URL 'http://127.0.0.1:51004/view/31' caused an error:

'buffer_id(31) is not valid (closed or unsupported file format)'

**NOTE:** If you run multiple instances of Sublime Text, you may want to adjust
the `server_port` option in order to get this plugin work again.
```

&ensp;&ensp;&ensp;&ensp;解决办法,可以先尝试改变端口 Sublime Text > Preferences > Package Settings > OmniMarkupPreviewer > Settings，不行再进行下面的尝试：

```
Sublime Text > Preferences > Package Settings > OmniMarkupPreviewer > Settings - User

{
    "renderer_options-MarkdownRenderer": {
        "extensions": ["tables", "fenced_code", "codehilite"]
    }
}
```


## 参考链接
- [Sublime Text 3 配置和使用方法](https://www.zybuluo.com/king/note/47271)
- [LICENSE KEY FOR SUBLIME TEXT 3 BUILD 3143](https://gist.github.com/hygull/6cdf0fa8a1184693a234a7a73cbdd52e)
- [Sublime 40个插件分享](https://blog.csdn.net/gamelife1314/article/details/50616513)