# FasterDeploy

> 快速部署Mysql、FasterRunner、FasterWeb

## 部署前准备
1、创建本地数据目录 
```
mkdir -p /data/poratiner /data/var/lib/mysql
```
2、创建集群主节点
注意这里Windows系统主机IP为虚拟机IP
```
docker swarm init --advertise-addr <主机IP> 
```
3、创建集群网络
```
docker network create -d overlay --subnet 10.0.0.1/24 --attachable swarm_net 
```
4、查看集群
```
docker node ls 
```
如果需要退出集群，则执行：
```
docker swarm leave 
```
## 部署基础服务

1、启动portainer、mysql
```
docker stack deploy --compose-file fasterdeploy-base-compose.yml base 
```
2、连接数据库，创建FasterRunner库，编码选择utf8

## 部署业务服务
1、启动服务
```
docker stack deploy --compose-file fasterdeploy-service-compose.yml busi 
```

2、应用数据库表
```
先查找fasterrunner的容器ID
$docker ps
CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS               NAMES
b3d0ef2ac77b        hub.c.163.com/wydydq/fasterrunner:latest   "python manage.py ru…"   13 minutes ago      Up 13 minutes       8000/tcp            mars_fasterrunner-service.1.8g393df3w86vhloiujr10d3v3

然后进入容器
$docker exec -it b3d0ef2ac77b /bin/sh

再执行命令：
#python manage.py makemigrations fastrunner fastuser
#python manage.py migrate fastrunner
#python manage.py migrate fastuser
```

## 访问应用
1、访问集群管理工具
```
http://localhost:9000
```
2、访问fasterweb
```
http://localhost:8082/#/fastrunner/register 
```
