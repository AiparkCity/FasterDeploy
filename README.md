# FasterDeploy

![LICENSE]
> 一键部署Mysql、FasterRunner、FasterWeb

## 集群部署服务
1、创建本地数据目录 
```
mkdir -p /data/poratiner /data/var/lib/mysql
```
2、创建集群主节点
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
5、退出集群
```
docker swarm leave 
```
6、启动服务
```
docker stack deploy --compose-file fasterdeploy-compose.yml mars 
```
7、访问服务
```
http://localhost:8082/#/fastrunner/register 
```
8、访问集群管理工具
```
http://localhost:9000
```
