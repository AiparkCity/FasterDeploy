# FasterDeploy

> 快速部署Mysql、FasterRunner、FasterWeb

## 部署前准备
1、创建本地数据目录 
```
$ sudo mkdir -p /data/poratiner /data/var/lib/mysql
```
2、创建集群主节点
Windows系统主机IP为虚拟机IP，linux、Mac系统主机IP为本机IP
```
$ sudo docker swarm init --advertise-addr <主机IP> 
```
3、创建集群网络
```
$ sudo docker network create -d overlay --subnet 10.0.0.1/24 --attachable swarm_net 
```
查看docker网络
```
$ sudo docker network ls
```
4、查看集群
```
$ sudo docker node ls 
```
如果需要退出集群，则执行
```
$ sudo docker swarm leave 
```
5、构建fasterrunner的Docker镜像
修改settings.py DATABASES 字典相关配置
```
$ sudo docker build -t fasterrunner:latest .
```
6、构建fasterweb的Docker镜像
修改default.conf配置文件 server_name的ip, 注意为当前docker服务宿主机的ip地址
修改/src/restful/api.js baseUrl地址, 即为fastrunner容器运行的宿主机地址
执行npm install, npm run build # 生成生产环境包
```
$ sudo docker build -t fasterweb:latest .
```
## 部署基础服务

1、启动portainer、mysql
```
$ sudo docker stack deploy --compose-file fasterdeploy-base-compose.yml base 
```
2、连接数据库，创建FasterRunner库，编码选择utf8

## 部署业务服务
1、启动服务
```
$ sudo docker stack deploy --compose-file fasterdeploy-service-compose.yml busi 
```

2、应用数据库表
```
先查找fasterrunner的容器ID
$ sudo docker ps
CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS               NAMES
b3d0ef2ac77b        hub.c.163.com/wydydq/fasterrunner:latest   "python manage.py ru…"   13 minutes ago      Up 13 minutes       8000/tcp            mars_fasterrunner-service.1.8g393df3w86vhloiujr10d3v3

然后进入容器
$ sudo docker exec -it b3d0ef2ac77b /bin/sh

执行命令：
# python manage.py makemigrations fastrunner fastuser
# python manage.py migrate fastrunner
# python manage.py migrate fastuser
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
