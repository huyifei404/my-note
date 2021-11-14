# 一、端口

```bash
#netstat命令参数
-t: #指明显示TCP端口
-u：#指明显示UDP端口
-l：#仅显示监听套接字(所谓套接字就是使应用程序能够读写与收发通讯协议(protocol)与资料的程序)
-p: #显示进程标识符和程序名称，每一个套接字/端口都属于一个程序。
-n：#不进行DNS轮询，显示IP(可以加速操作)
#即可显示当前服务器上所有端口及进程服务，于grep结合可查看某个具体端口及服务情况··

#查看当前所有tcp端口·
netstat -ntlp  
#查看所有80端口使用情况·
netstat -ntulp |grep 80
#查看所有3306端口使用情况·
netstat -an | grep 3306
#查看一台服务器上面哪些服务及端口
netstat -lanp
#查看一个服务有几个端口。比如要查看mysqld
ps -ef |grep mysqld
#查看某一端口的连接数量,比如3306端口
netstat -pnt |grep :3306 |wc
#查看某一端口的连接客户端IP 比如3306端口
netstat -anp |grep 3306
netstat -an
#使用lsof -i :port就能看见所指定端口运行的程序，同时还有当前连接。
lsof -i :port
nmap 端口扫描
netstat -nupl  (UDP类型的端口)
netstat -ntpl  (TCP类型的端口)
netstat -anp 显示系统端口使用情况
```

# 二、进程

```bash
#1、ps相关
#列出所有运行中/激活进程
ps -a
ps -ef|grep 8888
ps -aux -
#2、pstree
#3、top
#4、htop
#5、kill
kill -9 
```



# 三、防火墙

```bash
#1、查看firewall服务状态
systemctl status firewalld  #以active、inactive作为区别
#2、查看firewall的状态
firewall-cmd --state
#3、开启、重启、关闭firewall
	service firewalld start
	service firewalld restart
	service firewalld stop
#4、查看防火墙规则
firewall-cmd --list-all
#5、查询、开放、关闭端口
firewall-cmd --query-port=8080/tcp
firewall-cmd --permanent --add-port=8181/tcp
firewall-cmd --reload
```

# 四、ssh远程连接问题

```bash
#安装
yum install openssh-server
#编辑
vim /etc/ssh/sshd_config
	PermitRootLogin yes
#重启
service ssh restart

```

# 五、jar包运行

```shell
#运行j
java -jar my-site.jar
#当前ssh窗口不被锁定，但窗口关闭后，程序终止
java -jar my-site.jar &
#不挂断运行命令，当账号推出或终端关闭时，程序依然运行，当用nohup命令执行作业时，缺省状态下该作业的所有输出被重定向到nohup.out的文件中，除非另外指定文件
nohup java -jar my-site.jar &
#将command的输出重定向到out.file文件，即输出内容不打印到屏幕上，而是输出到out.file文件中
nohup java -jar my-site.jar >/dev/null &
#将错误重定向到标准输出上
nohup java -jar shareniu.jar >/dev/null 2>&1 &
#指定端口
java -jar my-site.jar --server.port=80
```

# 六、查看日志

```bash
#1、systemctl的日志
journalctl
#显示所有的systemctl的日志，这里的日志是多个服务混合的
journalctl -f
#Follow the journal
journalctl -u 服务名
journalctl -fu 服务名
#查看某个服务的日志
journalctl -h
#帮助
```

# 七、Vim常用命令

```bash
set nu    显示行号
gg     跳转到文件开头
/     向后搜索
?    向前搜索
n    查找下一处
N    查找上一处
|     光标所在行行首
L    屏幕所显示的底行
{    段首
}    段尾
-    前一行行首
+    后一行行首
(    句首
)    下一句首
$    行末
M    屏幕中间行
0    行首（零）
hjkl    左下上右
x    删除光标所在字符
R    替换模式（可以替换任意字符）
r    单个替换
dd     删除光标所在的行
D    删除至行末（从光标位置开始）
s    删除字符并插入（单个字符删除，并进入插入模式）
S    删除行并插入（整行删除）
>>     缩进（相当于一个tab）
<<     反缩进
=    自动格式化
J    合并上下两行
I    插入到行首
i     插入
C    从光标处开始修改至行位
a    在光标后附件或追加
A    在行末追加
p    粘贴（后）
P    粘贴（前）
Esc     命令模式
ZZ     保存退出编辑(vi，含保存)
ZQ    不保存退出编辑
```



# 八、杂七杂八

```bash
#修改主机名
#1、需重启
vi /etc/sysconfig/network
	HOSTNAME=yourhostname
#2、无需重启，z
hostname yourhostname	
```

# 九、公司相关

```bash
调用地址 http://172.32.147.240:8003/test
开发主机 172.32.147.240  bossapp/bossapp

UIG启停路径
/opt/source/bossapp/openplat/application/uig/script
MDB启停路径
/opt/source/bossapp/openplat/application/mdb/script

日志路径
/opt/source/bossapp/openplat/bosslog/uig/log

nginx路径：
/opt/source/bossapp/nginx
能开地址：
http://172.32.147.240:8003/

模拟返回nginx：
/opt/source/bossapp/nginx_lua/nginx/
nginx地址：http://172.32.148.240:8099/
 
重启能开：
先停uig，再停mdb，再起mdb，再起uig

IMPORTGLACCOUNTCLAIMDOCSRVTEST
ImportGLAccountClaimDocSrvTest

重启nginx：
ps -ef| grep nginx找到进程
复制路径跟上-s reload

ls -lrt

#重启服务
source /opt/source/bossapp/openplat/application/uig/script/stopuig.sh

source /opt/source/bossapp/openplat/application/mdb/script/stop.sh

source /opt/source/bossapp/openplat/application/mdb/script/start.sh

source /opt/source/bossapp/openplat/application/uig/script/startuig.sh
```

# 十、Docker

### Docker镜像加速器

```bash
#编辑镜像文件
vim /etc/docker/daemon.json

#内容 电子科技大学的镜像
{"registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]}

```

### Docker安装MySQL

#### 1、下载安装

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

#### 2、修改Docker位置 

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

#### 3、my-site对应的docker使用

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



