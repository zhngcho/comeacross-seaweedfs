[toc]



# Welcome to Come Across [Seaweedfs](https://github.com/chrislusf/seaweedfs)

This is a note of [seaweedfs](https://github.com/chrislusf/seaweedfs), consisting of

- What is seaweedfs
- [How to use it](./HowToUseSeaweedfs.md)
- [How it works](./HowSeaweedfsWorks.md)

# What is Seaweedfs

[Seaweedfs](https://github.com/chrislusf/seaweedfs)， 简称 weed， 是一个 golang 开发的可伸缩的分布式文件系统， 可以非常高效的存储数十亿的小文件。此外，它还支持 Filer,  S3 API, FUSE mount, 兼容 hadoop, 对于 warm data 支持 Rack-Aware Erasure Coding。weed 是 Facebook 的 Haystack 的开源实现（[Facebook's Haystack design paper](./papers/Beaver.pdf)）, Erasure Coding 部分参考了 Facebook 的 paper [Facebook's Warm BLOB Storage System](./papers/osdi14-paper-muralidhar.pdf) 。





# seaweed 节点逻辑拓扑图

![seaweed architecture](/home/zhngcho/mddddddddd/comeacross-seaweedfs/figures/seaweed_architecture.png)



## master server 节点的功能

### [master server API](https://github.com/chrislusf/seaweedfs/wiki/Master-Server-API)

- GET (/dir/assign) 写文件前为文件分配 volume server 和 fid
- GET (/dir/lookup) 通过 volume id 查询 volume URL
- GET (/vol/vacuum) 强制进行 gc
- GET (/vol/grow) 提前分配多个 volumes 用以增加并发，每个 volume 为序列化写入
- GET (/col/delete) 删除 collection
- GET (/cluster/status) 查看集群状态
- GET (/dir/status) 查看逻辑拓扑状态

### master server 的内部功能

- 维护 volume servers 的心跳信息、和各个 volume servers 的 keep-connected 信息，维护 volumes 的信息在 各个 volume servers 中的同步
- 定期发送 vacumm 指令给 volume server
- 定期执行一些配置文件中指定的指令

## volume server 节点的功能

### [volume server API](https://github.com/chrislusf/seaweedfs/wiki/Volume-Server-API)

- POST (/{fid}) 写文件
- DELETE(/{fid}) 删除文件
- GET (/status) 查看 volume 信息
- GET (/{fid}) 读文件

### volume server 的内部功能

- 定期向 master 发送心跳包，把 volumes 信息发送到 master
- 响应 master server， 包括更新 volumes 的信息， 进行 vacuum 等



# 相关链接

- [How to use it](./HowToUseSeaweedfs.md)
- [How it works](./HowSeaweedfsWorks.md)



