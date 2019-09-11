## centrifuge 后端部署

### QuickStart

1. 前期开始, 配置可参考 config.json (权限放开比较严重)
2. 启动:
    ```
        centrifugo --config=config.json
    ```

### 配置说明

#### 基本配置说明
```
Centrifugo – real-time messaging server

Usage:
   [flags]
   [command]

Available Commands:
  checkconfig Check configuration file
  genconfig   Generate simple configuration file to start with
  help        Help about any command
  version     Centrifugo version information

Flags:
  -a, --address string             interface address to listen on 服务的地址
      --admin                      enable admin web interface 是否开启admin的管理界面
      --admin_insecure             use insecure admin mode – no auth required for admin socket admin安全验证, 节点: /, 可直接访问admin管理界面, 默认为false, 登录admin需要admin_password, 反之可直接登录
      --api_insecure               use insecure API mode  后台推送安全验证, 节点: /api. 默认为 false, 此时访问节点需要api_key. 当设置为 true 后, 任何人都将可以访问此节点
      --client_insecure            start in insecure client mode 客户端是否需要安全验证, 默认为false, 客户端必须拥有 JWT token 才能访问
  -c, --config string              path to config file (default "config.json")
      --debug                      enable debug endpoints 是否开启dubug节点, 开启后可访问 /debug/pprof/ 查看一些Centrifugo的网络状态
  -e, --engine string              engine to use: memory or redis (default "memory") 消息存储引擎, 默认为内存, 部署多个实例会造成数据不同步, 因此推荐使用 redis
      --grpc_api                   enable GRPC API server 是否开启grpc api, 目前我们将使用 http_api
  -h, --help                       help for this command
      --internal_port string       custom port for internal endpoints 开启自定义默认端口, 默认为8000, 开启额外的端口为admin访问
      --log_file string            optional log file - if not specified logs go to STDOUT  log输出文件
      --log_level string           set the log level: debug, info, error, fatal or none (default "info") log级别
  -n, --name string                unique node name 命名空间名称, 此空间下的属性会覆盖common部分, 但不会继承
      --pid_file string            optional path to create PID file
  -p, --port string                port to bind HTTP server to (default "8000")  服务端口号
      --prometheus                 enable Prometheus metrics endpoint
      --redis_db int               Redis database (Redis engine)
      --redis_host string          Redis host (Redis engine) (default "127.0.0.1")
      --redis_master_name string   name of Redis master Sentinel monitors (Redis engine)
      --redis_password string      Redis auth password (Redis engine)
      --redis_port string          Redis port (Redis engine) (default "6379")
      --redis_sentinels string     comma-separated list of Sentinel addresses (Redis engine)
      --redis_tls                  enable Redis TLS connection
      --redis_tls_skip_verify      disable Redis TLS host verification
      --redis_url string           Redis connection URL in format redis://:password@hostname:port/db (Redis engine)
      --tls                        enable TLS, requires an X509 certificate and a key file
      --tls_cert string            path to an X509 certificate file
      --tls_key string             path to an X509 certificate key
```

- *注意点:*
    - engine: 
        - 主要功能: 在节点之间发布消息，处理PUB / SUB代理订阅，保存/检索状态和历史数据。
        - 分类:
            - Memory: 测试学习可直接使用默认的 Memory 引擎. 使用简单, 但不允许缩放节点
            - Redis: 允许将节点进行横向拓展, 各节点通过redis共享信息
    - name: 命名空间名称
        - 名字唯一, 命名格式`^[-a-zA-Z0-9_]{2,}$`
        - 命名空间和渠道配置`没有继承`, 命名空间必须单独配置参数, 不然会使用默认值

#### 渠道配置说明

##### 渠道: 用户可以订阅渠道, 并接受该渠道推送的所有消息

- 渠道命名中的特别字符
    - `:`, 命名空间分隔符, namespace:channelName
    - `$`, 私有渠道前缀, 客户端必须正确的提供签名
    - `#`, 用户渠道分隔符, channelName#42, 代表只有用户42才能订阅此渠道, 此时需要在生成JWT token 时正确在 payload 中加入参数 "sub": "42", 多个用户可使用以下方式, channelName#42,43
- 配置
    - publish: 是否允许客户端直接推送消息到渠道, 一般情况下由后端进行消息推送
    - subscribe_to_publish: 在推送消息之前, 是否必须要订阅渠道才能推送
    - anonymous: 匿名访问, JWT token 中sub为空, 默认为false, 当设置为true时, 将默认使用空字符串为用户id
    - presence: 是否可查看当前渠道的连接信息(连接的用户)
    - join_leave: 渠道内用户是否接收渠道内订阅/取消订阅的信息推送
    - history_recover: 是否开启消息回复, 适用于在断掉连接重连时使用, 此时 history_size 和 history_lifetime 必须配置
    - history_size: 保存多少历史消息
    - history_lifetime: 历史消息的保存时长. 单位为秒
