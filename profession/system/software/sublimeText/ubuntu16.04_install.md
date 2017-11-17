# Ubuntu16.04 下的 sublime text 2配置记录
***

## 一、安装sublime
&ensp;&ensp;&ensp;&ensp;通过命令行进行安装，这样比较方便,其默认安装在/opt/下面：

```
sudo add-apt-repository ppa:webupd8team/sublime-text-2
sudo apt-get update
sudo apt-get install sublime-text
```

&ensp;&ensp;&ensp;&ensp;有的人在第二步会特别费时间，这时候可以在软件安装源中去掉不必要的源

## 二、解决中文不能输入的问题
- 1.首先在安装的目录下新建一个sublime_imfix.c文件，并将下面的代码复制到里面：

```c
/*
sublime-imfix.c
Use LD_PRELOAD to interpose some function to fix sublime input method support for linux.
By Cjacker Huang <jianzhong.huang at i-soft.com.cn>

gcc -shared -o libsublime-imfix.so sublime_imfix.c  `pkg-config --libs --cflags gtk+-2.0` -fPIC
LD_PRELOAD=./libsublime-imfix.so sublime_text
*/
#include <gtk/gtk.h>
#include <gdk/gdkx.h>
typedef GdkSegment GdkRegionBox;

struct _GdkRegion
{
  long size;
  long numRects;
  GdkRegionBox *rects;
  GdkRegionBox extents;
};

GtkIMContext *local_context;

void
gdk_region_get_clipbox (const GdkRegion *region,
            GdkRectangle    *rectangle)
{
  g_return_if_fail (region != NULL);
  g_return_if_fail (rectangle != NULL);

  rectangle->x = region->extents.x1;
  rectangle->y = region->extents.y1;
  rectangle->width = region->extents.x2 - region->extents.x1;
  rectangle->height = region->extents.y2 - region->extents.y1;
  GdkRectangle rect;
  rect.x = rectangle->x;
  rect.y = rectangle->y;
  rect.width = 0;
  rect.height = rectangle->height; 
  //The caret width is 2; 
  //Maybe sometimes we will make a mistake, but for most of the time, it should be the caret.
  if(rectangle->width == 2 && GTK_IS_IM_CONTEXT(local_context)) {
        gtk_im_context_set_cursor_location(local_context, rectangle);
  }
}

//this is needed, for example, if you input something in file dialog and return back the edit area
//context will lost, so here we set it again.

static GdkFilterReturn event_filter (GdkXEvent *xevent, GdkEvent *event, gpointer im_context)
{
    XEvent *xev = (XEvent *)xevent;
    if(xev->type == KeyRelease && GTK_IS_IM_CONTEXT(im_context)) {
       GdkWindow * win = g_object_get_data(G_OBJECT(im_context),"window");
       if(GDK_IS_WINDOW(win))
         gtk_im_context_set_client_window(im_context, win);
    }
    return GDK_FILTER_CONTINUE;
}

void gtk_im_context_set_client_window (GtkIMContext *context,
          GdkWindow    *window)
{
  GtkIMContextClass *klass;
  g_return_if_fail (GTK_IS_IM_CONTEXT (context));
  klass = GTK_IM_CONTEXT_GET_CLASS (context);
  if (klass->set_client_window)
    klass->set_client_window (context, window);

  if(!GDK_IS_WINDOW (window))
    return;
  g_object_set_data(G_OBJECT(context),"window",window);
  int width = gdk_window_get_width(window);
  int height = gdk_window_get_height(window);
  if(width != 0 && height !=0) {
    gtk_im_context_focus_in(context);
    local_context = context;
  }
  gdk_window_add_filter (window, event_filter, context); 
}
```

- 2.安装相应的编译环境

```
sudo apt-get install build-essential
sudo apt-get install libgtk2.0-dev
```

- 3.进行编译

```
gcc -shared -o libsublime-imfix.so sublime_imfix.c  `pkg-config --libs --cflags gtk+-2.0` -fPIC
```

- 4.运行解决

```
LD_PRELOAD=./libsublime-imfix.so subl
```

*注意命令的运行都是在sublime的安装目录下进行的*

## 三、配置Markdown编辑
&ensp;&esnp;&ensp;&ensp;在Ubuntu中的Markdown编辑也是有很多问题，其中就是浏览器不能自动启动的问题，这个我只能临时提供一个解决方案了：在点击浏览快捷键以后使用Ctrl+`调出控制台，里面有相应的网页地址，只能手动输入进去查看了，也行吧，不是不能接收

## 参考链接
[http://blog.csdn.net/u011982340/article/details/45843413](http://blog.csdn.net/u011982340/article/details/45843413)

[https://www.findhao.net/res/291](https://www.findhao.net/res/291)

[http://blog.csdn.net/youngdze/article/details/21125719](http://blog.csdn.net/youngdze/article/details/21125719)