# redisproxy之Nutcracker
Nutcracker（Twemproxy）
1、什么是Nutcracker
Nutcracker，又称Twemproxy（读音："two-em-proxy"）是支持memcached和redis协议的快速、轻量级代理；
它的建立旨在减少后端缓存服务器上的连接数量；
再结合管道技术（pipelining*）、及分片技术可以横向扩展分布式缓存架构；
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
  #listen: 0.0.0.0:1221
  listen: /home/work/nutcracker/twemproxy1
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
