# Vue 环境搭建运行
***
## VSCode相应的插件
- ESLint
- Vetur


## vue-cli构建
*只能本地进行访问问题：在根目录下面的/config/index.js文件 修改host 为ip地址即可*
```
npm install -g vue-cli
vue init webpack my-project
cd my-project
npm install
npm run dev
```

## vue flask 的跨域问题
一共设置两个地方，不知道是哪些地方起了作用

### vue部分
```
main.js

import axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, axios)
```

```
config/index.js

dev: {

    // Paths
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {
      '/getFundList': {
        target: 'http://192.168.31.68:8090',
        changeOrigin: true,
        pathRewrite: {
          '^/getFundList': '/getFundList'
        }
      }
    },
```

```
template

this.axios.get('/getFundList').then((response) => {
    //   console.log(response.data.fund_list)
      var fund_list = response.data.fund_list
      for(var i=0; i<fund_list.length; i++) {
          console.log(fund_list[i])
      }
    }, (response) => {
      console.log(response)
    })
```

### flask
```
from flask_cors import CORS

from Crawler.MongoDBUtil import MongoDBUtil

app = Flask(__name__)
api = flask_restful.Api(app)

CORS(app, resources=r'/*')
CORS(app, supports_credentials=True)
```

```
def get(self):
    MongoDBUtil.connect()
    ret = MongoDBUtil.query(
    	{"fund_name": "list"})
    MongoDBUtil.close()

    fund_list = []
    for fund in ret[0]["data"]:
        fund_list.append(fund[1])

    res = jsonify({"fund_list": fund_list})
    res = make_response(res)
    res.headers["Access-Control-Allow-Origin"] = "*"
    res.headers['Access-Control-Allow-Methods'] = 'OPTIONS,HEAD,GET,POST'
    res.headers['Access-Control-Allow-Headers'] = 'x-requested-with'
    return res
```

## 参考链接
- [vue项目构建与实战](https://juejin.im/post/591be64f128fe1005ceb81f1)
- [vue项目环境搭建及一个简单的vue例子](https://blog.csdn.net/qq_33036599/article/details/79656597)
- [在Vue项目中使用Echarts(一): 在Vue中引入Echarts](https://www.jianshu.com/p/fa42e697665d)
- [Vue-cli proxyTable 解决开发环境的跨域问题](https://www.jianshu.com/p/95b2caf7e0da)
- [Flask解决跨域的问题](https://blog.csdn.net/lluozh2015/article/details/78553604)
- [python flask与ajax交互中的跨域问题以及解决方案](https://blog.csdn.net/qq_41134708/article/details/80658175)
- [vue使用axios进行ajax请求](https://www.jianshu.com/p/aa989357846c)
- [Elements in iteration expect to have 'v-bind:key' directives](https://blog.csdn.net/Young_Gao/article/details/79812159)
- [使用Visual Studio Code编写Vue的札记]https://cloud.tencent.com/developer/article/1004923)