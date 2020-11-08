# Go设置使用记录
***
## 常用编译命令
```bash
go run xxx.go

go build

go mod init xxxx
```

## 国内源切换
Go 1.13 及以上（推荐）
打开你的终端并执行

```bash
$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn,direct
```

## 参考链接
- [Goproxy 中国](https://github.com/goproxy/goproxy.cn/blob/master/README.zh-CN.md)