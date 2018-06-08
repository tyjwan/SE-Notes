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

## markdown插件配置
- OmniMarkupPreviewer：Markdown文件浏览和转换

## 参考链接
- [Sublime Text 3 配置和使用方法](https://www.zybuluo.com/king/note/47271)