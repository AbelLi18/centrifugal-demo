## centrifuge 前端部署

在后端部署完成之后

### QuickStart

1. 安装依赖, 确保本地安装有node
```
npm install
```

2. 启动服务
```
node server.js
```

3. 打开页面, 检测是否部署成功

确保本地的centrifugo服务已启动, 且端口分别为 8000, 8001, 否则需要到对应目录下手动更改端口号

```
http://127.0.0.1:1818/8000/index.html

http://127.0.0.1:1818/centrifugejs/index.html
```