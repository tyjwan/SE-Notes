# Websocket Server
***
## Code
```python
from websocket_server import WebsocketServer


def new_client(client, server):
    server.send_message_to_all("Hey all, a new client has joined us")


server = WebsocketServer(port=1234, host="0.0.0.0")
server.set_fn_new_client(new_client)
server.run_forever()
```

## 参考链接
- [websocket-server 0.4](https://pypi.org/project/websocket-server/)
- [python-websocket-server](https://github.com/Pithikos/python-websocket-server)