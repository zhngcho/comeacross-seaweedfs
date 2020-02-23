[toc]

## [seaweed 的环境搭建](https://github.com/chrislusf/seaweedfs#example-usage)

```shell
#!/bin/bash

# 第一步
# 下载对应版本的二进制文件(https://github.com/chrislusf/seaweedfs/releases/latest)
# 或者下载源码并进行编译（https://github.com/chrislusf/seaweedfs）

# 第二步
# 启动一个或多个 master server
./weed master

# 第三步
# 启动一个或者多个 volume server
./weed volume -dir="/tmp/data1" -max=5  -mserver="localhost:9333" -port=8080 &
./weed volume -dir="/tmp/data2" -max=10 -mserver="localhost:9333" -port=8081 &
```



## [seaweed API 使用](https://github.com/chrislusf/seaweedfs#example-usage)

### 写文件

1. 向 master server 发起 GET 请求获取 fid 和 volume server 信息（事实上 POST, PUT 请求也可以 ）

   ```shell
   curl http://localhost:9333/dir/assign
   # 请求结果为
   # {"count":1,"fid":"3,01637037d6","url":"127.0.0.1:8080","publicUrl":"localhost:8080"}
   ```

2. 向 volume server 发起 multi-part POST 请求

   ```shell
   curl -F file=@/home/chris/myphoto.jpg http://127.0.0.1:8080/3,01637037d6
   # 请求结果为
   # {"name":"myphoto.jpg","size":43234,"eTag":"1cc0118e"}
   ```

3. 保存 fid 或者直接保存 URL(http://{volume server ip}:{volume server port}/{fid})

**ps :  fid 由三部分组成 {volume id},{real fid}{8字节的cookie用来防止fid猜测}**

### 读文件

1. 根据 fid 向 master server 发起 GET 请求，获取 volume server 信息

   ```shell
   curl http://localhost:9333/dir/lookup?volumeId=3
   # 请求结果为
   # {"volumeId":"3","locations":[{"publicUrl":"localhost:8080","url":"localhost:8080"}]}
   ```

2. 根据 fid 向 volume server 发起 GET 请求，获取文件

   ```shell
   curl http://localhost:8080/3,01637037d6
   ```

### 删除文件

1. 根据 fid 向 master server 发起 GET 请求获取 fid 和 volume server 信息（事实上 POST, PUT 请求也可以 ）

   ```shell
   curl http://localhost:9333/dir/assign
   # 请求结果为
   # {"count":1,"fid":"3,01637037d6","url":"127.0.0.1:8080","publicUrl":"localhost:8080"}
   ```

2. 根据 fid 向 volume server 发起 DELETE 请求

   ```shell
   curl -X DELETE http://127.0.0.1:8080/3,01637037d6
   ```

### 更新文件

1. 根据 fid 向 master server 发起 GET 请求，获取 volume server 信息

   ```shell
   curl http://localhost:9333/dir/lookup?volumeId=3
   # 请求结果为
   # {"volumeId":"3","locations":[{"publicUrl":"localhost:8080","url":"localhost:8080"}]}
   ```


2. 向 volume server 发起 multi-part POST 请求

   ```shell
   curl -F file=@/home/chris/myNEWphoto.jpg http://127.0.0.1:8080/3,01637037d6
   # 请求结果为
   # {"name":"myNEWphoto.jpg","size":43234,"eTag":"1cc0118e"}
   ```

**ps: 更新文件其实就是重新执行一次写文件的第二步，上传新的文件内容**

### 设置 TTL

1. 向 master server 发起 GET 请求获取 fid 和 volume server 信息（事实上 POST, PUT 请求也可以 ）, ttl 参数表示需要分配一个 ttl = 3min 的volume

   ```shell
   curl http://localhost:9333/dir/assign?ttl=3m
   # 请求结果为
   # {"count":1,"fid":"3,01637037d6","url":"127.0.0.1:8080","publicUrl":"localhost:8080"}
   ```

2. 向 volume server 发起 multi-part POST 请求

   ```shell
   curl -F file=@/home/chris/myphoto.jpg http://127.0.0.1:8080/3,01637037d6?ttl=3m
   # 请求结果为
   # {"name":"myphoto.jpg","size":43234,"eTag":"1cc0118e"}
   ```

3. 保存 fid 或者直接保存 URL(http://{volume server ip}:{volume server port}/{fid})

**ps: ttl 支持 'm', 'h', 'd', 'w',  'M', 'y'**

### 设置 replica

- 待更新





# 相关链接

- [How it works](./HowSeaweedfsWorks.md)
- [What is seaweedfs](./index.md)
