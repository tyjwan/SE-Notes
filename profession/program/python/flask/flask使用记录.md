# Flask 使用记录
***
## 运行与部署
### 开发环境部署
&ensp;&ensp;&ensp;&ensp;简单使用pycharm即可

### 生产环境部署
&ensp;&ensp;&ensp;&ensp;使用supervisor无法使用flask命令启动相应的服务，必须要使用其他的方式才行,使用下面的方式必须进入到工程的根目录中才行

```
pip install gunicorn
gunicorn -w 4 -b 127.0.0.1:5000 run:app
```