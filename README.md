# FasterDeploy

> Docker部署Mysql、FasterRunner、FasterWeb；

## 部署前准备
1、创建本地数据目录 
```
$ sudo mkdir -p /data/poratiner /data/var/lib/mysql /opt/autotest
```
2、创建集群主节点
```
注：Windows系统主机IP为虚拟机IP，linux、Mac系统主机IP为本机IP
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
5、拉取代码
```
$ cd /opt/autotest
$ git clone https://github.com/yinquanwang/FasterRunner.git
$ git clone https://github.com/yinquanwang/FasterWeb.git
$ git clone https://github.com/AiparkCity/FasterDeploy.git
```
6、构建fasterrunner的Docker镜像
```
$ cd FasterRunner
修改数据库配置:
$ vim FasterRunner/settings.py
构建镜像：
$ sudo docker build -t fasterrunner:latest .
```
6、构建fasterweb的Docker镜像
```
$ cd FasterWeb
修改配置：
第一步：修改default.conf配置文件 server_name的ip, 注意为当前docker服务宿主机的ip地址
第二步：修改/src/restful/api.js baseUrl地址, 即为fastrunner容器运行的宿主机地址
第三步：执行npm install, npm run build # 生成生产环境包
构建镜像：
$ sudo docker build -t fasterweb:latest .
```
## 部署基础服务
1、启动portainer、mysql
```
注：portainer为Docker图形化管理工具，可以选择安装
$ cd FasterDeploy
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
1、访问Docker图形化管理工具portainer
```
http://localhost:9000
```
2、访问fasterweb
```
http://localhost:8082/#/fastrunner/register 
```
