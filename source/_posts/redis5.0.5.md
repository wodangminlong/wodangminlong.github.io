---
title: Centos7安装redis5.0.5单机版设置远程访问和开机启动
tags:
  - redis
categories:
  - redis  
---
每次增加新的环境或服务器时需要安装一大堆基础服务,记录一下redis安装步骤,以后不用百度啦~

### 一、安装gcc
``` bash
# yum install gcc
```
### 二、下载redis
``` bash
# wget http://download.redis.io/releases/redis-5.0.5.tar.gz
```
### 三、解压
``` bash
# tar -zxvf redis-5.0.5.tar.gz
```
### 四、进入解压后的文件夹并编译
``` bash
# cd redis-5.0.5
# make
```
### 五、第四步正常会显示 It's a good idea to run 'make test' . 继续安装
``` bash
# make install prefix=/usr/local/redis
```
### 六、成功后启动redis
``` bash
# /usr/local/redis/bin/redis-server
```
### 七、修改redis配置文件
``` bash
# vim /usr/local/redis/redis.conf
```
#### 找到配置文件中 bind 127.0.0.1 注掉或更改为0.0.0.0
#### 找到配置文件中 protected-mode 更改为 yes
#### 找到配置文件中 daemonize 更改为 yes
#### 保存退出. 重启后测试远程访问(记得打开防火墙端口)
### 八、设置开机自启动. redis5.0.5自带添加服务的脚本,进入redis目录下的utils目录
``` bash
# cd /usr/local/redis/utils/
# ./install_server.sh
```
#### 选择默认配置，按回车键
### 九、重启系统测试是否开机自启动
