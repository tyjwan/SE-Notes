# wxPython 学习
****
想用Python做一个任务管理小软件，就学习了一下这个

## 有以下五大模块
- Windows 包含应用的各种封装窗口，如面板、对话框、框架、或者滚动窗口
- GDI 系统图形设备接口，如字体、颜色、刷子、笔和图片
- Core 包含基本的类，如object，尺寸（Sizers）、事件（Events）、点、矩形
- Misc 其他的类和模块函数，有用于日志记录的、应用配置的、系统配置的、工作方式的
- Controls 包含图形界面的基本图形组件

## Widgets
### Base Widgets
抽象组件

![base Widgest](./picture/widgets/base.jpg)

### Top level Widgets
能单独存在的组件


![toplevel Widgest](./picture/widgets/toplevel.jpg)

### Containers
能够包含其他的组件

![containers Widgest](./picture/widgets/containers.jpg)

### Dynamic Widgets
这些组件能被用户编辑

![dynamic Widgest](./picture/widgets/dynamic.jpg)

### Static Widgets
这些组件用于展示信息，不能被编辑

![static Widgest](./picture/widgets/static.jpg)

### Other Widgets
菜单栏、工具栏、状态栏

![bar Widgest](./picture/widgets/bars.jpg)

## 简单示例
### 创建窗口
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

# center.py

import wx

class Example(wx.Frame):
  
    def __init__(self, parent, title):
        super(Example, self).__init__(parent, title=title, 
            size=(300, 200))
            
        self.Centre()
        self.Show()


if __name__ == '__main__':
  
    app = wx.App()
    Example(None, title='Center')
    app.MainLoop()
```

## 创建菜单
### Simple menu
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

'''
ZetCode wxPython tutorial

This example shows a simple menu.

author: Jan Bodnar
website: www.zetcode.com
last modified: September 2011
'''

import wx

class Example(wx.Frame):
    
    def __init__(self, *args, **kwargs):
        super(Example, self).__init__(*args, **kwargs) 
            
        self.InitUI()
        
    def InitUI(self):    

        menubar = wx.MenuBar()
        fileMenu = wx.Menu()
        fitem = fileMenu.Append(wx.ID_EXIT, 'Quit', 'Quit application')
        menubar.Append(fileMenu, '&File')
        self.SetMenuBar(menubar)
        
        self.Bind(wx.EVT_MENU, self.OnQuit, fitem)

        self.SetSize((300, 200))
        self.SetTitle('Simple menu')
        self.Centre()
        self.Show(True)
        
    def OnQuit(self, e):
        self.Close()

def main():
    
    ex = wx.App()
    Example(None)
    ex.MainLoop()    


if __name__ == '__main__':
    main()
```

### Icons and shortcuts
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

'''
ZetCode wxPython tutorial

In this example, we manually create 
a menu item.

author: Jan Bodnar
website: www.zetcode.com
last modified: September 2011
'''

import wx

APP_EXIT = 1

class Example(wx.Frame):
    
    def __init__(self, *args, **kwargs):
        super(Example, self).__init__(*args, **kwargs) 
            
        self.InitUI()
        
    def InitUI(self):

        menubar = wx.MenuBar()
        fileMenu = wx.Menu()
        qmi = wx.MenuItem(fileMenu, APP_EXIT, '&Quit\tCtrl+Q')
        qmi.SetBitmap(wx.Bitmap('exit.png'))
        fileMenu.AppendItem(qmi)

        self.Bind(wx.EVT_MENU, self.OnQuit, id=APP_EXIT)

        menubar.Append(fileMenu, '&File')
        self.SetMenuBar(menubar)

        self.SetSize((250, 200))
        self.SetTitle('Icons and shortcuts')
        self.Centre()
        self.Show(True)
        
    def OnQuit(self, e):
        self.Close()

def main():
    
    ex = wx.App()
    Example(None)
    ex.MainLoop()    


if __name__ == '__main__':
    main()
```

### Submenus and separators
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

'''
ZetCode wxPython tutorial

In this example, we create a submenu and a menu
separator.

author: Jan Bodnar
website: www.zetcode.com
last modified: September 2011
'''

import wx


class Example(wx.Frame):
    
    def __init__(self, *args, **kwargs):
        super(Example, self).__init__(*args, **kwargs) 
            
        self.InitUI()
        
    def InitUI(self):

        menubar = wx.MenuBar()

        fileMenu = wx.Menu()
        fileMenu.Append(wx.ID_NEW, '&New')
        fileMenu.Append(wx.ID_OPEN, '&Open')
        fileMenu.Append(wx.ID_SAVE, '&Save')
        fileMenu.AppendSeparator()

        imp = wx.Menu()
        imp.Append(wx.ID_ANY, 'Import newsfeed list...')
        imp.Append(wx.ID_ANY, 'Import bookmarks...')
        imp.Append(wx.ID_ANY, 'Import mail...')

        fileMenu.AppendMenu(wx.ID_ANY, 'I&mport', imp)

        qmi = wx.MenuItem(fileMenu, wx.ID_EXIT, '&Quit\tCtrl+W')
        fileMenu.AppendItem(qmi)

        self.Bind(wx.EVT_MENU, self.OnQuit, qmi)

        menubar.Append(fileMenu, '&File')
        self.SetMenuBar(menubar)

        self.SetSize((350, 250))
        self.SetTitle('Submenu')
        self.Centre()
        self.Show(True)
        
    def OnQuit(self, e):
        self.Close()

def main():
    
    ex = wx.App()
    Example(None)
    ex.MainLoop()    


if __name__ == '__main__':
    main()
```

### Check menu item
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

'''
ZetCode wxPython tutorial

This example creates a checked
menu item.

author: Jan Bodnar
website: www.zetcode.com
last modified: September 2011
'''

import wx


class Example(wx.Frame):
    
    def __init__(self, *args, **kwargs):
        super(Example, self).__init__(*args, **kwargs) 
            
        self.InitUI()
        
    def InitUI(self):    

        menubar = wx.MenuBar()
        fileMenu = wx.Menu()
        viewMenu = wx.Menu()
        
        self.shst = viewMenu.Append(wx.ID_ANY, 'Show statubar', 
            'Show Statusbar', kind=wx.ITEM_CHECK)
        self.shtl = viewMenu.Append(wx.ID_ANY, 'Show toolbar', 
            'Show Toolbar', kind=wx.ITEM_CHECK)
            
        viewMenu.Check(self.shst.GetId(), True)
        viewMenu.Check(self.shtl.GetId(), True)

        self.Bind(wx.EVT_MENU, self.ToggleStatusBar, self.shst)
        self.Bind(wx.EVT_MENU, self.ToggleToolBar, self.shtl)

        menubar.Append(fileMenu, '&File')
        menubar.Append(viewMenu, '&View')
        self.SetMenuBar(menubar)

        self.toolbar = self.CreateToolBar()
        self.toolbar.AddLabelTool(1, '', wx.Bitmap('texit.png'))
        self.toolbar.Realize()

        self.statusbar = self.CreateStatusBar()
        self.statusbar.SetStatusText('Ready')

        self.SetSize((350, 250))
        self.SetTitle('Check menu item')
        self.Centre()
        self.Show(True)
        
        
    def ToggleStatusBar(self, e):
        
        if self.shst.IsChecked():
            self.statusbar.Show()
        else:
            self.statusbar.Hide()

    def ToggleToolBar(self, e):
        
        if self.shtl.IsChecked():
            self.toolbar.Show()
        else:
            self.toolbar.Hide()        

def main():
    
    ex = wx.App()
    Example(None)
    ex.MainLoop()    


if __name__ == '__main__':
    main()
```

### Context menu
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

'''
ZetCode wxPython tutorial

In this example, we create a context menu.

author: Jan Bodnar
website: www.zetcode.com
last modified: September 2011
'''

import wx

class MyPopupMenu(wx.Menu):
    
    def __init__(self, parent):
        super(MyPopupMenu, self).__init__()
        
        self.parent = parent

        mmi = wx.MenuItem(self, wx.NewId(), 'Minimize')
        self.AppendItem(mmi)
        self.Bind(wx.EVT_MENU, self.OnMinimize, mmi)

        cmi = wx.MenuItem(self, wx.NewId(), 'Close')
        self.AppendItem(cmi)
        self.Bind(wx.EVT_MENU, self.OnClose, cmi)


    def OnMinimize(self, e):
        self.parent.Iconize()

    def OnClose(self, e):
        self.parent.Close()
        

class Example(wx.Frame):
    
    def __init__(self, *args, **kwargs):
        super(Example, self).__init__(*args, **kwargs) 
            
        self.InitUI()
        
    def InitUI(self):

        self.Bind(wx.EVT_RIGHT_DOWN, self.OnRightDown)

        self.SetSize((250, 200))
        self.SetTitle('Context menu')
        self.Centre()
        self.Show(True)
        
    def OnRightDown(self, e):
        self.PopupMenu(MyPopupMenu(self), e.GetPosition())

def main():
    
    ex = wx.App()
    Example(None)
    ex.MainLoop()    


if __name__ == '__main__':
    main()
```

### Toolbars
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

'''
ZetCode wxPython tutorial

This example creates a simple toolbar.

author: Jan Bodnar
website: www.zetcode.com
last modified: September 2011
'''

import wx

class Example(wx.Frame):
    
    def __init__(self, *args, **kwargs):
        super(Example, self).__init__(*args, **kwargs) 
            
        self.InitUI()
        
    def InitUI(self):    

        toolbar = self.CreateToolBar()
        qtool = toolbar.AddLabelTool(wx.ID_ANY, 'Quit', wx.Bitmap('texit.png'))
        toolbar.Realize()

        self.Bind(wx.EVT_TOOL, self.OnQuit, qtool)

        self.SetSize((250, 200))
        self.SetTitle('Simple toolbar')
        self.Centre()
        self.Show(True)
        
    def OnQuit(self, e):
        self.Close()

def main():
    
    ex = wx.App()
    Example(None)
    ex.MainLoop()    


if __name__ == '__main__':
    main()
```

If we want to create more than one toolbars, we must do it differently.

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

'''
ZetCode wxPython tutorial

In this example, we create two horizontal 
toolbars. 

author: Jan Bodnar
website: www.zetcode.com
last modified: September 2011
'''

import wx

class Example(wx.Frame):
    
    def __init__(self, *args, **kwargs):
        super(Example, self).__init__(*args, **kwargs) 
            
        self.InitUI()
        
    def InitUI(self):    

        vbox = wx.BoxSizer(wx.VERTICAL)

        toolbar1 = wx.ToolBar(self)
        toolbar1.AddLabelTool(wx.ID_ANY, '', wx.Bitmap('tnew.png'))
        toolbar1.AddLabelTool(wx.ID_ANY, '', wx.Bitmap('topen.png'))
        toolbar1.AddLabelTool(wx.ID_ANY, '', wx.Bitmap('tsave.png'))
        toolbar1.Realize()

        toolbar2 = wx.ToolBar(self)
        qtool = toolbar2.AddLabelTool(wx.ID_EXIT, '', wx.Bitmap('texit.png'))
        toolbar2.Realize()

        vbox.Add(toolbar1, 0, wx.EXPAND)
        vbox.Add(toolbar2, 0, wx.EXPAND)

        self.Bind(wx.EVT_TOOL, self.OnQuit, qtool)
        
        self.SetSizer(vbox)

        self.SetSize((300, 250))
        self.SetTitle('Toolbars')
        self.Centre()
        self.Show(True)
        
    def OnQuit(self, e):
        self.Close()

def main():
    
    ex = wx.App()
    Example(None)
    ex.MainLoop()    


if __name__ == '__main__':
    main()
```

#### Enable, disable
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

'''
ZetCode wxPython tutorial

In this example, we create two horizontal 
toolbars. 

author: Jan Bodnar
website: www.zetcode.com
last modified: September 2011
'''

import wx

class Example(wx.Frame):
    
    def __init__(self, *args, **kwargs):
        super(Example, self).__init__(*args, **kwargs) 
            
        self.InitUI()
        
    def InitUI(self):    

        self.count = 5

        self.toolbar = self.CreateToolBar()
        tundo = self.toolbar.AddLabelTool(wx.ID_UNDO, '', wx.Bitmap('tundo.png'))
        tredo = self.toolbar.AddLabelTool(wx.ID_REDO, '', wx.Bitmap('tredo.png'))
        self.toolbar.EnableTool(wx.ID_REDO, False)
        self.toolbar.AddSeparator()
        texit = self.toolbar.AddLabelTool(wx.ID_EXIT, '', wx.Bitmap('texit.png'))
        self.toolbar.Realize()

        self.Bind(wx.EVT_TOOL, self.OnQuit, texit)
        self.Bind(wx.EVT_TOOL, self.OnUndo, tundo)
        self.Bind(wx.EVT_TOOL, self.OnRedo, tredo)

        self.SetSize((250, 200))
        self.SetTitle('Undo redo')
        self.Centre()
        self.Show(True)
        
    def OnUndo(self, e):
        if self.count > 1 and self.count <= 5:
            self.count = self.count - 1

        if self.count == 1:
            self.toolbar.EnableTool(wx.ID_UNDO, False)

        if self.count == 4:
            self.toolbar.EnableTool(wx.ID_REDO, True)

    def OnRedo(self, e):
        if self.count < 5 and self.count >= 1:
            self.count = self.count + 1

        if self.count == 5:
            self.toolbar.EnableTool(wx.ID_REDO, False)

        if self.count == 2:
            self.toolbar.EnableTool(wx.ID_UNDO, True)
   
        
    def OnQuit(self, e):
        self.Close()

def main():
    
    ex = wx.App()
    Example(None)
    ex.MainLoop()    


if __name__ == '__main__':
    main()
```

## 布局
### Using sizers
- wx.BoxSizer
- wx.StaticBoxSizer
- wx.GridSizer
- wx.FlexGridSizer
- wx.GridBagSizer

The following example uses a built-in sizer.

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

# sizer.py

import wx

class Example(wx.Frame):
  
    def __init__(self, parent, title):
        super(Example, self).__init__(parent, title=title, 
            size=(260, 180))
            
        self.InitUI()
        self.Centre()
        self.Show()     
        
    def InitUI(self):
    
        menubar = wx.MenuBar()
        filem = wx.Menu()
        editm = wx.Menu()
        helpm = wx.Menu()

        menubar.Append(filem, '&File')
        menubar.Append(editm, '&Edit')
        menubar.Append(helpm, '&Help')
        self.SetMenuBar(menubar)

        wx.TextCtrl(self)


if __name__ == '__main__':
  
    app = wx.App()
    Example(None, title='')
    app.MainLoop()
```

### wx.BoxSizer
应该只是单排单列吧

```python
box = wx.BoxSizer(integer orient)
box.Add(wx.Window window, integer proportion=0, integer flag = 0, integer border = 0)
```

第二个参数与什么方向相关的，有0、1、2但并不理解有啥具体作用；第三个也是不理解；第四天是定义组件之间的间距的，有下面的几个参数wx.LEFT、wx.RIGHT、wx.BOTTOM、wx.TOP、wx.ALL

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

# border.py

import wx

class Example(wx.Frame):
  
    def __init__(self, parent, title):
        super(Example, self).__init__(parent, title=title, 
            size=(260, 180))
            
        self.InitUI()
        self.Centre()
        self.Show()     
        
    def InitUI(self):
    
        panel = wx.Panel(self)

        panel.SetBackgroundColour('#4f5049')
        vbox = wx.BoxSizer(wx.VERTICAL)

        midPan = wx.Panel(panel)
        midPan.SetBackgroundColour('#ededed')

        vbox.Add(midPan, 1, wx.EXPAND | wx.ALL, 20)
        panel.SetSizer(vbox)


if __name__ == '__main__':
  
    app = wx.App()
    Example(None, title='Border')
    app.MainLoop()
```

来看一下复杂的用法

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

# gotoclass.py

import wx

class Example(wx.Frame):
  
    def __init__(self, parent, title):
        super(Example, self).__init__(parent, title=title, 
            size=(390, 350))
            
        self.InitUI()
        self.Centre()
        self.Show()     
        
    def InitUI(self):
    
        panel = wx.Panel(self)

        font = wx.SystemSettings_GetFont(wx.SYS_SYSTEM_FONT)
        font.SetPointSize(9)

        vbox = wx.BoxSizer(wx.VERTICAL)

        hbox1 = wx.BoxSizer(wx.HORIZONTAL)
        st1 = wx.StaticText(panel, label='Class Name')
        st1.SetFont(font)
        hbox1.Add(st1, flag=wx.RIGHT, border=8)
        tc = wx.TextCtrl(panel)
        hbox1.Add(tc, proportion=1)
        vbox.Add(hbox1, flag=wx.EXPAND|wx.LEFT|wx.RIGHT|wx.TOP, border=10)

        vbox.Add((-1, 10))

        hbox2 = wx.BoxSizer(wx.HORIZONTAL)
        st2 = wx.StaticText(panel, label='Matching Classes')
        st2.SetFont(font)
        hbox2.Add(st2)
        vbox.Add(hbox2, flag=wx.LEFT | wx.TOP, border=10)

        vbox.Add((-1, 10))

        hbox3 = wx.BoxSizer(wx.HORIZONTAL)
        tc2 = wx.TextCtrl(panel, style=wx.TE_MULTILINE)
        hbox3.Add(tc2, proportion=1, flag=wx.EXPAND)
        vbox.Add(hbox3, proportion=1, flag=wx.LEFT|wx.RIGHT|wx.EXPAND, 
            border=10)

        vbox.Add((-1, 25))

        hbox4 = wx.BoxSizer(wx.HORIZONTAL)
        cb1 = wx.CheckBox(panel, label='Case Sensitive')
        cb1.SetFont(font)
        hbox4.Add(cb1)
        cb2 = wx.CheckBox(panel, label='Nested Classes')
        cb2.SetFont(font)
        hbox4.Add(cb2, flag=wx.LEFT, border=10)
        cb3 = wx.CheckBox(panel, label='Non-Project classes')
        cb3.SetFont(font)
        hbox4.Add(cb3, flag=wx.LEFT, border=10)
        vbox.Add(hbox4, flag=wx.LEFT, border=10)

        vbox.Add((-1, 25))

        hbox5 = wx.BoxSizer(wx.HORIZONTAL)
        btn1 = wx.Button(panel, label='Ok', size=(70, 30))
        hbox5.Add(btn1)
        btn2 = wx.Button(panel, label='Close', size=(70, 30))
        hbox5.Add(btn2, flag=wx.LEFT|wx.BOTTOM, border=5)
        vbox.Add(hbox5, flag=wx.ALIGN_RIGHT|wx.RIGHT, border=10)

        panel.SetSizer(vbox)


if __name__ == '__main__':
  
    app = wx.App()
    Example(None, title='Go To Class')
    app.MainLoop()
```

### wx.GridSizer
表格布局，这个是每个组件的大小是相同固定的

```python
wx.GridSizer(int rows=1, int cols=0, int vgap=0, int hgap=0)
```

```python
# 自动填充?可以自己设定吗？
#!/usr/bin/python
# -*- coding: utf-8 -*-

# calculator.py

import wx

class Example(wx.Frame):
  
    def __init__(self, parent, title):
        super(Example, self).__init__(parent, title=title, 
            size=(300, 250))
            
        self.InitUI()
        self.Centre()
        self.Show()     
        
    def InitUI(self):
    
        menubar = wx.MenuBar()
        fileMenu = wx.Menu()
        menubar.Append(fileMenu, '&File')
        self.SetMenuBar(menubar)

 
        vbox = wx.BoxSizer(wx.VERTICAL)
        self.display = wx.TextCtrl(self, style=wx.TE_RIGHT)
        vbox.Add(self.display, flag=wx.EXPAND|wx.TOP|wx.BOTTOM, border=4)
        gs = wx.GridSizer(5, 4, 5, 5)

        gs.AddMany( [(wx.Button(self, label='Cls'), 0, wx.EXPAND),
            (wx.Button(self, label='Bck'), 0, wx.EXPAND),
            (wx.StaticText(self), wx.EXPAND),
            (wx.Button(self, label='Close'), 0, wx.EXPAND),
            (wx.Button(self, label='7'), 0, wx.EXPAND),
            (wx.Button(self, label='8'), 0, wx.EXPAND),
            (wx.Button(self, label='9'), 0, wx.EXPAND),
            (wx.Button(self, label='/'), 0, wx.EXPAND),
            (wx.Button(self, label='4'), 0, wx.EXPAND),
            (wx.Button(self, label='5'), 0, wx.EXPAND),
            (wx.Button(self, label='6'), 0, wx.EXPAND),
            (wx.Button(self, label='*'), 0, wx.EXPAND),
            (wx.Button(self, label='1'), 0, wx.EXPAND),
            (wx.Button(self, label='2'), 0, wx.EXPAND),
            (wx.Button(self, label='3'), 0, wx.EXPAND),
            (wx.Button(self, label='-'), 0, wx.EXPAND),
            (wx.Button(self, label='0'), 0, wx.EXPAND),
            (wx.Button(self, label='.'), 0, wx.EXPAND),
            (wx.Button(self, label='='), 0, wx.EXPAND),
            (wx.Button(self, label='+'), 0, wx.EXPAND) ])

        vbox.Add(gs, proportion=1, flag=wx.EXPAND)
        self.SetSizer(vbox)


if __name__ == '__main__':
  
    app = wx.App()
    Example(None, title='Calculator')
    app.MainLoop()
```

### wx.FlexGridSizer
这个和上面是很相似，但组件的大小不是相同固定的

```python
# 并不是很明白
#!/usr/bin/python
# -*- coding: utf-8 -*-

# review.py

import wx

class Example(wx.Frame):
  
    def __init__(self, parent, title):
        super(Example, self).__init__(parent, title=title, 
            size=(300, 250))
            
        self.InitUI()
        self.Centre()
        self.Show()     
        
    def InitUI(self):
    
        panel = wx.Panel(self)

        hbox = wx.BoxSizer(wx.HORIZONTAL)

        fgs = wx.FlexGridSizer(3, 2, 9, 25)

        title = wx.StaticText(panel, label="Title")
        author = wx.StaticText(panel, label="Author")
        review = wx.StaticText(panel, label="Review")

        tc1 = wx.TextCtrl(panel)
        tc2 = wx.TextCtrl(panel)
        tc3 = wx.TextCtrl(panel, style=wx.TE_MULTILINE)

        fgs.AddMany([(title), (tc1, 1, wx.EXPAND), (author), 
            (tc2, 1, wx.EXPAND), (review, 1, wx.EXPAND), (tc3, 1, wx.EXPAND)])

        fgs.AddGrowableRow(2, 1)
        fgs.AddGrowableCol(1, 1)

        hbox.Add(fgs, proportion=1, flag=wx.ALL|wx.EXPAND, border=15)
        panel.SetSizer(hbox)


if __name__ == '__main__':
  
    app = wx.App()
    Example(None, title='Review')
    app.MainLoop()
```

### wx.GridBagSizer
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

# rename.py

import wx

class Example(wx.Frame):
  
    def __init__(self, parent, title):
        super(Example, self).__init__(parent, title=title, 
            size=(320, 130))
            
        self.InitUI()
        self.Centre()
        self.Show()     
        
    def InitUI(self):
      
        panel = wx.Panel(self)
        sizer = wx.GridBagSizer(4, 4)

        text = wx.StaticText(panel, label="Rename To")
        sizer.Add(text, pos=(0, 0), flag=wx.TOP|wx.LEFT|wx.BOTTOM, border=5)

        tc = wx.TextCtrl(panel)
        sizer.Add(tc, pos=(1, 0), span=(1, 5), 
            flag=wx.EXPAND|wx.LEFT|wx.RIGHT, border=5)

        buttonOk = wx.Button(panel, label="Ok", size=(90, 28))
        buttonClose = wx.Button(panel, label="Close", size=(90, 28))
        sizer.Add(buttonOk, pos=(3, 3))
        sizer.Add(buttonClose, pos=(3, 4), flag=wx.RIGHT|wx.BOTTOM, border=5)

        sizer.AddGrowableCol(1)
        sizer.AddGrowableRow(2)
        panel.SetSizerAndFit(sizer)
    

if __name__ == '__main__':
  
    app = wx.App()
    Example(None, title='Rename')
    app.MainLoop()
```

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

# newclass.py

import wx

class Example(wx.Frame):

    def __init__(self, parent, title):    
        super(Example, self).__init__(parent, title=title, 
            size=(450, 350))

        self.InitUI()
        self.Centre()
        self.Show()     

    def InitUI(self):
      
        panel = wx.Panel(self)
        
        sizer = wx.GridBagSizer(5, 5)

        text1 = wx.StaticText(panel, label="Java Class")
        sizer.Add(text1, pos=(0, 0), flag=wx.TOP|wx.LEFT|wx.BOTTOM, 
            border=15)

        icon = wx.StaticBitmap(panel, bitmap=wx.Bitmap('exec.png'))
        sizer.Add(icon, pos=(0, 4), flag=wx.TOP|wx.RIGHT|wx.ALIGN_RIGHT, 
            border=5)

        line = wx.StaticLine(panel)
        sizer.Add(line, pos=(1, 0), span=(1, 5), 
            flag=wx.EXPAND|wx.BOTTOM, border=10)

        text2 = wx.StaticText(panel, label="Name")
        sizer.Add(text2, pos=(2, 0), flag=wx.LEFT, border=10)

        tc1 = wx.TextCtrl(panel)
        sizer.Add(tc1, pos=(2, 1), span=(1, 3), flag=wx.TOP|wx.EXPAND)

        text3 = wx.StaticText(panel, label="Package")
        sizer.Add(text3, pos=(3, 0), flag=wx.LEFT|wx.TOP, border=10)

        tc2 = wx.TextCtrl(panel)
        sizer.Add(tc2, pos=(3, 1), span=(1, 3), flag=wx.TOP|wx.EXPAND, 
            border=5)

        button1 = wx.Button(panel, label="Browse...")
        sizer.Add(button1, pos=(3, 4), flag=wx.TOP|wx.RIGHT, border=5)

        text4 = wx.StaticText(panel, label="Extends")
        sizer.Add(text4, pos=(4, 0), flag=wx.TOP|wx.LEFT, border=10)

        combo = wx.ComboBox(panel)
        sizer.Add(combo, pos=(4, 1), span=(1, 3), 
            flag=wx.TOP|wx.EXPAND, border=5)

        button2 = wx.Button(panel, label="Browse...")
        sizer.Add(button2, pos=(4, 4), flag=wx.TOP|wx.RIGHT, border=5)

        sb = wx.StaticBox(panel, label="Optional Attributes")

        boxsizer = wx.StaticBoxSizer(sb, wx.VERTICAL)
        boxsizer.Add(wx.CheckBox(panel, label="Public"), 
            flag=wx.LEFT|wx.TOP, border=5)
        boxsizer.Add(wx.CheckBox(panel, label="Generate Default Constructor"),
            flag=wx.LEFT, border=5)
        boxsizer.Add(wx.CheckBox(panel, label="Generate Main Method"), 
            flag=wx.LEFT|wx.BOTTOM, border=5)
        sizer.Add(boxsizer, pos=(5, 0), span=(1, 5), 
            flag=wx.EXPAND|wx.TOP|wx.LEFT|wx.RIGHT , border=10)

        button3 = wx.Button(panel, label='Help')
        sizer.Add(button3, pos=(7, 0), flag=wx.LEFT, border=10)

        button4 = wx.Button(panel, label="Ok")
        sizer.Add(button4, pos=(7, 3))

        button5 = wx.Button(panel, label="Cancel")
        sizer.Add(button5, pos=(7, 4), span=(1, 1),  
            flag=wx.BOTTOM|wx.RIGHT, border=5)

        sizer.AddGrowableCol(2)
        
        panel.SetSizer(sizer)


if __name__ == '__main__':
  
    app = wx.App()
    Example(None, title="Create Java Class")
    app.MainLoop()
```



