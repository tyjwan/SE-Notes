# Jupyter 使用
***
```sh
# python2
$ pip install --upgrade pip
$ pip install jupyter

# python3
$ brew install python3  # macOS
$ pip3 install --upgrade pip
$ pip3 install jupyter
```

```sh
# python3 add to jupyter
python3 -m pip install ipykernel
python3 -m ipykernel install --user
```

```sh
jupyter netobook
http://localhost:8888/tree
```

#### Why I can't access remote Jupyter Notebook server?
```sh
jupyter notebook –generate-config –allow-root
jupyter notebook password

vi /root/.jupyter/jupyter_notebook_config.py
c.NotebookApp.allow_origin = '*' #allow all origins
c.NotebookApp.ip = '0.0.0.0' # listen on all IPs

jupyter notebook
```


## 参考链接
- [在Jupyter同時使用Python2 + Python3](https://carsonwah.github.io/15213187969431.html)
- [Why I can't access remote Jupyter Notebook server?](https://stackoverflow.com/questions/42848130/why-i-cant-access-remote-jupyter-notebook-server)
- [设置 jupyter notebook 可远程访问](https://blog.csdn.net/simple_the_best/article/details/77005400)