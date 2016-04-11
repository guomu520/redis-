# redisproxy之Nutcracker
Nutcracker（Twemproxy）

1、什么是Nutcracker

Nutcracker，又称Twemproxy（读音："two-em-proxy"）是支持memcached和redis协议的快速、轻量级代理；
它的建立旨在减少后端缓存服务器上的连接数量；再结合管道技术（pipelining*）、及分片技术可以横向扩展分布式缓存架构；

2、特点

（1）快 √

（2）轻量级 √

（3）减少与第三方的直接连接数 √

（1）保持与第三方的长连接

（2）可配置代理与后台每个第三方分片连接的数目

（4）支持管道技术

（5）支持平行多服务器代理，防止单点 √

（6）支持多个服务器池 √

（7）支持memcached ASCII和Redis协议 √

（8）通过一个简单的YAML文件来实现服务器池的配置 √

（9）支持多种hash模式，包括最常用的一致性hash模式，可以设置后端实例的权重 √

（10）支持配置一定次数失败剔出节点 √

（11）支持状态监控 √

（12）跨平台

√ 为我们用它的原因，或者目前已经在用的


3、缺点

（1）不支持Redis的事物操作

（2）不支持部分批量操作，如mset（支持但是变成非原子性操作） （0.2版本以上已经支持，不支持原子性）

（3）出错提示不够完善，不利于追查问题

4、配置参数  
 ```
web:
  #代理监听端口
  #listen: 0.0.0.0:1221   ##ip端口形式
  listen: /home/work/nutcracker/twemproxy1  #socket形式
  #使用md5对key进行hash
  hash: md5
  #节点使用一致性哈希构建hash环
  distribution: ketama
  #代理启动时直接与Memcache建立长链接
  preconnect: true
  #自动剔除故障节点
  auto_eject_hosts: true
  #节点故障时重试间隔
  server_retry_timeout: 300000
  #单个Memcache节点最大长链接限制
  server_connections: 50
  #失败限制，超过限制值时认为该节点不可用
  server_failure_limit: 30
  timeout: 500
  servers:
    - 172.16.2.80:11211:1
    - 172.16.0.226:11211:1
    - 172.16.0.54:11211:1
```    
5、启动以及监控说明
先看下各参数说明
-h 帮助
-V 版本
-t 测试配置文件
-d 作为守护进程运行
-D 打印统计描述
-v 打印日志级别
-o 设置日志文件
-c 配置文件
-s 监控端口
-a 设置统计监测
-i 在毫秒设定统计聚集间隔（默认：30000毫秒）
-p 设置PID文件
-m buf大小
如下例如
/home/service/nutcracker/bin/nutcracker -s 4204 -c /home/work/conf/nut/nutcracker4.yml -p /home/work/nutcracker/nutcracker4.pid -o /home/work/nutcracker/logs/nutcracker4.log -v 4
查看当前状态命令 
nc 127.0.0.1 4201
返回值说明
{
    "service": "nutcracker",
    "source": "dfz-goods-php-01.meilishuo.com",#hostname
    "version": "0.2.4",
    "uptime": 1687141,#服务已经启动的时间（单位：秒）√
    "timestamp": 1448939464,#当前时间戳 √
    "web": {#配置名称
        "client_eof": 10552370,
        "client_err": 0,#客户端连接错误次数
        "client_connections": 2,#当前活跃的客户端连接数
        "server_ejects": 0,#后端服务被踢出次数 √
        "forward_error": 1,#转发错误次数 √
        "fragments": 157341762,
        "10.5.12.67": {
            "server_eof": 0,
            "server_err": 0,#服务端连接错误次数
            "server_timedout": 0,#因连接超时的服务端错误次数
            "server_connections": 200,#当前活跃的服务端连接数
            "requests": 15151080,#已请求次数
            "request_bytes": 922022844,#已请求字节数
            "responses": 15151080,#已响应次数
            "response_bytes": 15929562310,#已相应字节数
            "in_queue": 0,
            "in_queue_bytes": 0,
            "out_queue": 0,
            "out_queue_bytes": 0
        },
        "10.5.12.68": {
            "server_eof": 0,
            "server_err": 0,
            "server_timedout": 0,
            "server_connections": 200,
            "requests": 13543421,
            "request_bytes": 843329330,
            "responses": 13543421,
            "response_bytes": 11472952692,
            "in_queue": 0,
            "in_queue_bytes": 0,
            "out_queue": 0,
            "out_queue_bytes": 0
        },
        ...
    }
}


