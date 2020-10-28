# VSCode 使用配置记录

---

## 安装配置

_配置完后大致样子如下_

![](./picture/vscode1.png)

### 插件

-   Windows opacity:该插件提供了窗口的透明效果，该效果可能不是每个人都接受。但根据我的使用体验，这个插件除了美化，在有时我需要对照着其他页面输入数据，我能够直接透过 VSCode 的页面看到底下的数据，这样我就不用分屏或者切屏了。
-   vscode-icons:文件图片样式，好看
-   Instant Markdown:Markdown 文档浏览器查看，好用，必备
-   One Monokai Theme:好看的主题，不错
-   Vim:不用不习惯，个人选择
- settings-sync:配置备份同步
- TODO Highlight：TODO list
- WakaTime ：统计在 VS Code 里写代码的时间
- Code Time：记录编程时间，统计代码行数。
- Markdown All in One：这个插件将帮助你更高效地在 Markdown 中编写文档。
- Prettier - Code formatter：Prettier 是一个代码格式化工具，只关注格式化，但不具备校验功能。
- indent-rainbow：突出显示代码缩进
- Code Spell Checker：单词拼写错误检查

### 字体

-   [Fira Code:点击 ttf 进行安装即可，还是比较好看的](https://github.com/tonsky/FiraCode/releases/download/5.2/Fira_Code_v5.2.zip)

#### 修改字体
设置中搜索 editor.fontLigatures 然后设置


```sh
// 启用字体连字
"editor.fontLigatures": false,
// 以像素为单位控制字号。
"editor.fontSize": 14,
// 控制字体粗细。
"editor.fontWeight": "normal",
"editor.fontFamily": "Fira Code", //后边的引号中写上要设置的字体类型，个人比较喜欢Fira Code
"editor.fontLigatures": true, //这个控制是否启用字体连字，true启用，false不启用，这里选择启用
"editor.fontSize": 15, //设置字体大小，这个不多说都明白
```

### 设置背景图片
- [vscode设置背景图片](https://www.cnblogs.com/chinabin1993/p/7151361.html)

```sh
    // 设置背景图片
    "background.enabled": true,
    "background.useDefault": false,
    "background.customImages": [
        "file:///D:/Download/1.jpeg"
    ],
    "background.style": {
        "content": "''",
        "pointer-events": "none",
        "position": "absolute",
        "z-index": "99999",
        "width": "100%",
        "height": "100%",
        "background-position": "center",
        "background-repeat": "no-repeat",
        "background-size": "100%,100%",
        "opacity": 0.1
    }
```

### Ctrl等快捷键使用还原
可以通过 File -> Preference -> Settings中   vim.useCtrlKeys 选项设置为 false

## 参考链接

-   [Windows 下如何配置好看且好用的 VSCODE 环境](https://zhuanlan.zhihu.com/p/164852197)
- [Style your Windows terminal and use WSL and PowerShell like a pro](https://medium.com/@hjgraca/style-your-windows-terminal-and-wsl2-like-a-pro-9a2e1ad4c9d0)
- [JanDeDobbeleer/oh-my-posh](https://github.com/JanDeDobbeleer/oh-my-posh?WT.mc_id=-blog-scottha#installation)
- [第一次使用VS Code时你应该知道的一切配置](https://zhuanlan.zhihu.com/p/62913725)
- [如何实现Github markdown 目录/页内跳转？](https://www.zhihu.com/question/58630229/answer/191984051)