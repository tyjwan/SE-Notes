# SFML 与 VS2015 的安装配置
****
## 一、下载
&ensp;&ensp;&ensp;&ensp;在官网下载 IDE 对应版本的 SFML，本安装教程使用的 IDE 是 VS2015，下载地址是：[https://www.sfml-dev.org/tutorials/2.4/start-vc.php](https://www.sfml-dev.org/tutorials/2.4/start-vc.php)

## 二、解压安装
&ensp;&ensp;&ensp;&ensp;把下载下来的相应安装包解压至任意文件下（看你心情，想放哪就放哪，但需要记住你放的位置），本教程将其解压至 C:\Program Files\SFML，然后呢？嗯，这样就完成了，是不是很简单！因为下载的这个是已经被别人编译好的版本了，我们不用自己再重新编译了，直接用就行的。

## 三、环境变量的配置
&ensp;&ensp;&ensp;&ensp;将 C:\Program Files\SFML\SFML-2.4.2\bin 添加到系统的环境变量中，这个怎么添加网上有很多的教程，照着弄就行了。记得添加完成以后要重启或者注销计算机才行，不然也用不了

## 四、VS2015 的相应配置
- 1.新建项目以后，选中项目右击选中属性
- 2.在左上方的平台选择：X64
- 3.左边的配置属性选择：VC++ 目录，在右边方框可以看到，可执行目录、饱含目录等等
- 4.可执行目录中添加 C:\Program Files\SFML\SFML-2.4.2\bin（添加的路径已本教程 SFML 的解压路径相应来写的，看官自己的路径对照着来就行了）
- 5.包含目录添加：C:\Program Files\SFML\SFML-2.4.2\include
- 6.库目录添加：C:\Program Files\SFML\SFML-2.4.2\lib
- 7.选择连接器--输入--附加依赖项，添加：
    - sfml-graphics-d.lib
    - sfml-window-d.lib
    - sfml-audio-d.lib
    - sfml-network-d.lib
    - sfml-system-d.lib

## 五、测试是否配置成功
&ensp;&ensp;&ensp;&ensp;上面基本配置完成了，接下来用一个测试程序来运行一下成功没有，把下面的代码复制粘贴到新建的 main.cpp 文件中，直接按 Ctrl+F5 运行，如果看到一个绿色的圆就说明配置完成了

```c++
#include <SFML/Graphics.hpp>

int main()
{
    sf::RenderWindow window(sf::VideoMode(200, 200), "SFML works!");
    sf::CircleShape shape(100.f);
    shape.setFillColor(sf::Color::Green);

    while (window.isOpen())
    {
        sf::Event event;
        while (window.pollEvent(event))
        {
            if (event.type == sf::Event::Closed)
                window.close();
        }

        window.clear();
        window.draw(shape);
        window.display();
    }

    return 0;
}
```
