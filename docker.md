# Docker镜像加速器

```bash
#编辑镜像文件
vim /etc/docker/daemon.json

#内容 电子科技大学的镜像
{"registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]}

```

# Docker安装MySQL

### 1、下载安装

```bash
#指定版本
docker search mysql
docker pull mysql:5.7

docker pull mysql

docker run -itd --name mysql-test -p 3308:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```

注意：pull镜像总是失败

```bash
#查看日志
tail -f /var/log/messages

#查看空间
df -hl /var/lib/docker

#停掉全部容器、
docker stop ${docker ps -a}
#删除所有容器
docker rm ${docker ps -a -q}

#删除全部镜像
docker rmi ${docker images -q}
```

### 2、修改Docker位置 

```bash
#1、停止docker
systemctl stop docker
#2、创建目录
mkdir /home/docker 
#3、迁移数据
cp /var/lib/docker /home/docker
#4、创建软连接
ln -s /home/docker /var/lib/docker
#5、启动docker
#6、查看存储信息
docker info | grep 'Docker Root Dir'
```

### 3、my-site对应的docker使用

- dockerFile：

```txt
FROM java:8
VOLUME /tmp
ADD my-site-1.0.2.RELEASE.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

- 构建命令

```bash
#构建docker镜像：
docker build -t app .
#运行镜像
docker run -d -p 8688:8688 --name app app
#查看Java进程
ps -ef|grep java
#查看docker运行日志
502  docker logs -f -t --tail 500 app
```



