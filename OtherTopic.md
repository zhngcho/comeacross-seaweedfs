



## Replica

### Data Center



### Rack



### Data Node



## other topics

#### master 节点初始化操作

- 解析命令行参数，加载配置
- 初始化 topology
- 注册 http 的 router
- 开启 goroutine,  保持和 leader 的连接和 volume 信息的接受
- 开启 goroutine,  (如果是 leader node) 定时获取所有 volume info 并进行维护
- 开启 goroutine,  (如果是 leader node) 定时进行 vacuum, 默认是15分钟检查一次
- 开启 goroutine,  (如果是 leader node) 定时执行一些配置文件中的配置好的weed shell script

#### volume 节点初始化操作

- 解析命令行参数，加载配置
- 注册 http 的 router
- 开启 goroutine, 发送属于本节点的所有 volumes 的心跳信息



#### master 节点内 goroutine 模型图



#### volume 节点内 goroutine 模型图



### Filer