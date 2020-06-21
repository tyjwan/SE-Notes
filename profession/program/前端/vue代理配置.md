# Vue 代理配置
***
```js
proxyTable: {
            '/api': {
                // target: 'http://192.168.0.112:8090',
                target: 'http://10.33.0.197:80/bam',
                changeOrigin: true,
                pathRewrite: {
                    '^/api': ''
                },
            },
            '/socket': {
                // target: 'http://192.168.0.112:8090',
                target: 'ws://10.33.0.197:80/bam',
                changeOrigin: true,
                pathRewrite: {
                    '^/socket': ''
                },
                ws:true,
            },
        },

const socket = new WebSocket('ws://' + location.host + '/socket/taskProgressWebSocket');
```

## 参考链接
- [vue的webpack代理websocket配置](https://blog.csdn.net/JimBo3693/article/details/100545053)