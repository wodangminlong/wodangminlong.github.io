---
title: Centos7redis5.0.5集群搭建
categories:
  - redis
tags:
  - redis
---
### 准备工作：
#### 1. 2台服务器IP分别为192.168.1.100;192.168.1.101 (IP地址为示例,根据实际情况更改即可;单台伪集群也适用)
#### 2. 分别在两台服务器上传redis-5.0.5.tar.gz包->解压->make->make install 至/usr/local/redis

安装好后开始配置集群
### 一、分别在两台服务器/usr/local/redis/ 目录下创建conf目录用于放集群文件
``` bash
# mkdir /usr/local/redis/conf
```
### 二、拷贝目录下的redis.conf至conf目录并指定名称为redis-6379.conf
``` bash
# copy /usr/local/redis/redis.conf /usr/local/redis/conf/redis-6379.conf
```
### 三、修改redis-6379.conf配置文件内容
``` bash
# vim /usr/local/redis/conf/redis-6379.conf
```
修改内容如下:</br>
<table>
    <tr>
        <th>更改前</th>
        <th>更改后</th>
    </tr>
    <tr>
        <td>bind 127.0.0.1</td>
        <td>bind 192.168.1.100(101服务器为192.168.1.101)</td>
    </tr>
    <tr>
        <td>pidfile /var/run/redis_6379.pid</td>
        <td>pidfile /usr/local/redis/conf/redis_6379.pid</td>
    </tr>
    <tr>
        <td>logfile ""</td>
        <td>logfile "/usr/local/redis/logs/redis-6379.log"</td>
    </tr>
    <tr>
        <td>appendonly no</td>
        <td>appendonly yes</td>
    </tr>
    <tr>
        <td># cluster-enabled yes</td>
        <td>cluster-enabled yes</td>
    </tr>
    <tr>
        <td># cluster-config-file nodes-6379.conf</td>
        <td>cluster-config-file /usr/local/redis/conf/nodes-6379.conf</td>
    </tr>
    <tr>
        <td># cluster-node-timeout 15000</td>
        <td>cluster-node-timeout 15000</td>
    </tr>
</table>

### 四、拷贝redis-6379.conf至3份,分别为redis-6379.conf、redis-6380.conf、redis-6381.conf
``` bash
# cp /usr/local/redis/conf/redis-6379.conf /usr/local/redis/conf/redis-6380.conf
# cp /usr/local/redis/conf/redis-6379.conf /usr/local/redis/conf/redis-6381.conf
```
### 五、修改redis-6380.conf、redis-6381.conf配置文件,全局搜索6379分别更改为6380和6381
### 六、分别启动两台服务器下的redis,各启动3个
``` bash
# /usr/local/redis/src/redis-server /usr/local/redis/conf/redis-6379.conf &
# /usr/local/redis/src/redis-server /usr/local/redis/conf/redis-6380.conf &
# /usr/local/redis/src/redis-server /usr/local/redis/conf/redis-6381.conf &
```
### 七、查看两台服务器redis启动情况,两台服务器6379、6380、6381端口都是redis表示启动成功
``` bash
# ps -ef | grep redis
```
### 八、防火墙开启对应的端口或关闭防火墙(开启端口时除了两台服务器的6379、6380、6381端口外,还需要开启16379、16380、16381端口作为集群总线端口)
### 九、使用redis-cli命令启动集群(此命令只在一台服务器执行)
``` bash
# /usr/local/redis/src/redis-cli --cluster create 192.168.1.100:6379 192.168.1.100:6380 192.168.1.100:6381 192.168.1.101:6379 192.168.1.101:6380 192.168.1.101:6381 --cluster-replicas 1
```
### 十、创建集群命令键入后可能会有提示type 'yes' to accept,输入yes,最终显示OK就完成了
### 十一、springboot连接集群时properties或yaml配置文件不再是spring.redis.host=xxx,yyy 而是spring.redis.cluster.nodes=xxx,yyy