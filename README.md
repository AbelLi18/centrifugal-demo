## centrifuge 部署

1. [centrifuge 后端部署](./backend-centrifugo/README.md)
2. [centrifuge 前端部署](./frontend/README.md)

## QuickStart

- 安装 Centrifugo
  - 添加源

    ```bash
    curl -s https://packagecloud.io/install/repositories/FZambia/centrifugo/script.deb.sh | sudo bash
    ```

  - 安装 Centrifugo

    ```bash
    sudo apt install centrifugo
    ```

  - 生成配置文件

    ```bash
    centrifugo genconfig
    ```

  - 启动 Centrifugo

    ```bash
    centrifugo --config=config.json
    ```

  根据 config.json 的 secret 的值 生成 JWT token 给前端发送指令使用


## Q&A

1. 启动centrifugo后, 建立连接报错: "attempt to call field 'replicate_commands' (a nil value)"
    - [详细细节参考issus](https://github.com/centrifugal/centrifugo/issues/279)
    - centrifugo启动后存储引擎有两种可选方案, 内存和redis. 此bug出现在使用redis引擎的时候, centrifugo 2.x 版本依赖redis版本为 3.2.x 以上, 请检查依赖, 并升级
    - Ubuntu 16.04 默认源中的 Redis 版本是3.0版本，不是最新版，要想通过 apt-get install 的方式安装最新版，首先添加 Redis 源。可执行以下步骤升级:
        - 首先安装依赖：sudo apt-get install software-properties-common -y
        - 使用如下命令添加 Redis 镜像源：sudo add-apt-repository ppa:chris-lea/redis-server -y
        - 使用如下命令安装 Redis：sudo apt-get update && sudo apt-get install redis-server -y
