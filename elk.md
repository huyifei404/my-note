## elk

#### Filebeat

转发和集中日志数据的工具，监视指定的日志文件或者位置，将其转发到es或者logstash进行索引。启动时会启动多个输入，在日志数据指定的位置中进行查找，最后聚集事件，将聚集的数据发送到filebeat配置的输出。

#### Logstash

它可以从多个来源采集转换数据，将数据发送到"存储库中"，它可以动态的采集转换、传输数据，不受格式或者复杂度的影响。

#### ElasticSearch

是分布式搜索和分析引擎，通过restful方式进行交互的近实时搜索框架，es可为所有类型的数据提供快速的搜索和分析。

#### kibana

针对es的可视化平遥，各种展示功能。

#### filebeat+logstash

- 通Logstash具有基于磁盘的自适应缓冲系统，该系统将吸收传入的吞吐量，从而减轻背压
- 从其他数据源（例如数据库，S3或消息传递队列）中提取
- 将数据发送到多个目的地，例如S3，HDFS或写入文件
- 使用条件数据流逻辑组成更复杂的处理管道
- 水平可扩展性，高可用性和可变负载处理：filebeat和logstash可以实现节点之间的负载均衡，多个logstash可以实现logstash的高可用
- 消息持久性与至少一次交付保证：使用Filebeat或Winlogbeat进行日志收集时，可以保证至少一次交付。从Filebeat或Winlogbeat到Logstash以及从Logstash到Elasticsearch的两种通信协议都是同步的，并且支持确认。Logstash持久队列提供跨节点故障的保护。对于Logstash中的磁盘级弹性，确保磁盘冗余非常重要。
- 具有身份验证和有线加密的端到端安全传输：从Beats到Logstash以及从 Logstash到Elasticse

## filebeat

#### 1.1、工作流程

![img](E:\note\pictures\1271254-20200615140610959-1559395773.png)

#### 2.1、filebeat的构成

filebeat结构：由两个组件构成，分别是inputs（输入）和harvesters（收集器）；每个文件会启动一个harvest进行日志搜集，文件删除或重命名不影响。

一个input负责管理harvests和寻找所有来源读取.。如果input类型是log，则input将查找驱动器上与定义的路径匹配的所有文件，并为每个文件启动一个harvester.

#### 2.2、filebeat如何保存文件的状态

　　Filebeat保留每个文件的状态，并经常将状态刷新到磁盘中的注册表文件中。该状态用于记住harvester读取的最后一个偏移量，并确保发送所有日志行。如果无法访问输出（如Elasticsearch或Logstash），Filebeat将跟踪最后发送的行，并在输出再次可用时继续读取文件。当Filebeat运行时，每个输入的状态信息也保存在内存中。当Filebeat重新启动时，来自注册表文件的数据用于重建状态，Filebeat在最后一个已知位置继续每个harvester。对于每个输入，Filebeat都会保留它找到的每个文件的状态。由于文件可以重命名或移动，文件名和路径不足以标识文件。对于每个文件，Filebeat存储唯一的标识符，以检测文件是否以前被捕获。



#### 2.3、filebeat何如保证至少一次数据消费

　　Filebeat保证事件将至少传递到配置的输出一次，并且不会丢失数据。是因为它将每个事件的传递状态存储在注册表文件中。在已定义的输出被阻止且未确认所有事件的情况下，Filebeat将继续尝试发送事件，直到输出确认已接收到事件为止。如果Filebeat在发送事件的过程中关闭，它不会等待输出确认所有事件后再关闭。当Filebeat重新启动时，将再次将Filebeat关闭前未确认的所有事件发送到输出。这样可以确保每个事件至少发送一次，但最终可能会有重复的事件发送到输出。通过设置shutdown_timeout选项，可以将Filebeat配置为在关机前等待特定时间

#### 3.1、压缩包安装

```bash
wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.7.0-linux-x86_64.tar.gz
tar -xzvf filebeat-7.7.0-linux-x86_64.tar.gz
```

#### 3.2、基本命令

详见官网：https://www.elastic.co/guide/en/beats/filebeat/current/command-line-options.html

```bash
export   #导出
run      #执行（默认执行）
test     #测试配置
keystore #秘钥存储
modules  #模块配置管理
setup    #设置初始环境
#./filebeat test config
```

#### 3.3、输入输出

支持的输入组件：

Multilinemessages,Azureeventhub,CloudFoundry,Container,Docker,GooglePub/Sub,HTTPJSON,Kafka,Log,MQTT,NetFlow,Office365ManagementActivityAPI,Redis,s3,Stdin,Syslog,TCP,UDP（最常用的额就是log）

支持的输出组件：

Elasticsearch,Logstash,Kafka,Redis,File,Console,ElasticCloud,Changetheoutputcodec（最常用的就是Elasticsearch,Logstash

#### 3.4、keystore的使用

keystore主要是防止敏感信息被泄露，比如密码等，像ES的密码，这里可以生成一个key为ES_PWD，值为es的password的一个对应关系，在使用es的密码的时候就可以使用${ES_PWD}使用

```txt
创建一个存储密码的keystore：filebeat keystore create
然后往其中添加键值对，例如：filebeatk eystore add ES_PWD
使用覆盖原来键的值：filebeat key store add ES_PWD–force
删除键值对：filebeat key store remove ES_PWD
查看已有的键值对：filebeat key store list
```

后期即可使用${ES_PWD}使用其值，例如：

output.elasticsearch.password:"${ES_PWD}"

#### 3.5、filebeat.yml(log输入为例)

详见官网：https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-log.html

```yml
type: log #input类型为log
enable: true #表示是该log类型配置生效
paths：     #指定要监控的日志，目前按照Go语言的glob函数处理。没有对配置目录做递归处理，比如配置的如果是：
- /var/log/* /*.log  #则只会去/var/log目录的所有子目录中寻找以".log"结尾的文件，而不会寻找/var/log目录下以".log"结尾的文件。
recursive_glob.enabled: #启用全局递归模式，例如/foo/**包括/foo, /foo/*, /foo/*/*
encoding：#指定被监控的文件的编码类型，使用plain和utf-8都是可以处理中文日志的
exclude_lines: ['^DBG'] #不包含匹配正则的行
include_lines: ['^ERR', '^WARN']  #包含匹配正则的行
harvester_buffer_size: 16384 #每个harvester在获取文件时使用的缓冲区的字节大小
max_bytes: 10485760 #单个日志消息可以拥有的最大字节数。max_bytes之后的所有字节都被丢弃而不发送。默认值为10MB (10485760)
exclude_files: ['\.gz$']  #用于匹配希望Filebeat忽略的文件的正则表达式列表
ingore_older: 0 #默认为0，表示禁用，可以配置2h，2m等，注意ignore_older必须大于close_inactive的值.表示忽略超过设置值未更新的
文件或者文件从来没有被harvester收集
close_* #close_ *配置选项用于在特定标准或时间之后关闭harvester。 关闭harvester意味着关闭文件处理程序。 如果在harvester关闭
后文件被更新，则在scan_frequency过后，文件将被重新拾取。 但是，如果在harvester关闭时移动或删除文件，Filebeat将无法再次接收文件
，并且harvester未读取的任何数据都将丢失。
close_inactive  #启动选项时，如果在制定时间没有被读取，将关闭文件句柄
读取的最后一条日志定义为下一次读取的起始点，而不是基于文件的修改时间
如果关闭的文件发生变化，一个新的harverster将在scan_frequency运行后被启动
建议至少设置一个大于读取日志频率的值，配置多个prospector来实现针对不同更新速度的日志文件
使用内部时间戳机制，来反映记录日志的读取，每次读取到最后一行日志时开始倒计时使用2h 5m 来表示
close_rename #当选项启动，如果文件被重命名和移动，filebeat关闭文件的处理读取
close_removed #当选项启动，文件被删除时，filebeat关闭文件的处理读取这个选项启动后，必须启动clean_removed
close_eof #适合只写一次日志的文件，然后filebeat关闭文件的处理读取
close_timeout #当选项启动时，filebeat会给每个harvester设置预定义时间，不管这个文件是否被读取，达到设定时间后，将被关闭
close_timeout 不能等于ignore_older,会导致文件更新时，不会被读取如果output一直没有输出日志事件，这个timeout是不会被启动的，
至少要要有一个事件发送，然后haverter将被关闭
设置0 表示不启动
clean_inactived #从注册表文件中删除先前收获的文件的状态
设置必须大于ignore_older+scan_frequency，以确保在文件仍在收集时没有删除任何状态
配置选项有助于减小注册表文件的大小，特别是如果每天都生成大量的新文件
此配置选项也可用于防止在Linux上重用inode的Filebeat问题
clean_removed #启动选项后，如果文件在磁盘上找不到，将从注册表中清除filebeat
如果关闭close removed 必须关闭clean removed
scan_frequency #prospector检查指定用于收获的路径中的新文件的频率,默认10s
tail_files：#如果设置为true，Filebeat从文件尾开始监控文件新增内容，把新增的每一行文件作为一个事件依次发送，
而不是从文件开始处重新发送所有内容。
symlinks：#符号链接选项允许Filebeat除常规文件外,可以收集符号链接。收集符号链接时，即使报告了符号链接的路径，
Filebeat也会打开并读取原始文件。
backoff： #backoff选项指定Filebeat如何积极地抓取新文件进行更新。默认1s，backoff选项定义Filebeat在达到EOF之后
再次检查文件之间等待的时间。
max_backoff： #在达到EOF之后再次检查文件之前Filebeat等待的最长时间
backoff_factor： #指定backoff尝试等待时间几次，默认是2
harvester_limit：#harvester_limit选项限制一个prospector并行启动的harvester数量，直接影响文件打开数

tags #列表中添加标签，用过过滤，例如：tags: ["json"]
fields #可选字段，选择额外的字段进行输出可以是标量值，元组，字典等嵌套类型
默认在sub-dictionary位置
filebeat.inputs:
fields:
app_id: query_engine_12
fields_under_root #如果值为ture，那么fields存储在输出文档的顶级位置

multiline.pattern #必须匹配的regexp模式
multiline.negate #定义上面的模式匹配条件的动作是 否定的，默认是false
假如模式匹配条件'^b'，默认是false模式，表示讲按照模式匹配进行匹配 将不是以b开头的日志行进行合并
如果是true，表示将不以b开头的日志行进行合并
multiline.match # 指定Filebeat如何将匹配行组合成事件,在之前或者之后，取决于上面所指定的negate
multiline.max_lines #可以组合成一个事件的最大行数，超过将丢弃，默认500
multiline.timeout #定义超时时间，如果开始一个新的事件在超时时间内没有发现匹配，也将发送日志，默认是5smax_procs #设置可以同时执行的最大CPU数。默认值为系统中可用的逻辑CPU的数量。name #为该filebeat指定名字，默认为主机的hostname
```

## logstash

#### 1.1、基本原理

logstash分为三个步骤：inputs(必须的)→ filters（可选的）→ outputs（必须的），inputs生成时间，filters对其事件进行过滤和处理，outputs输出到输出端或者决定其存储在哪些组件里。inputs和outputs支持编码和解码。

Logstash管道中的每个input阶段都在自己的线程中运行。将写事件输入到内存（默认）或磁盘上的中心队列。每个管道工作线程从该队列中取出一批事件，通过配置的filter处理该批事件，然后通过output输出到指定的组件存储。管道处理数据量的大小和管道工作线程的数量是可配置的

![img](E:\note\pictures\1271254-20200616135249028-794737920.png)

#### 1.2、安装

解压安装后可以测试

```bash
./bin/logstash -e 'input { stdin { } } output { stdout {} }'#需要有root权限
```

![image-20220327214303299](E:\note\pictures\elk_1.png)

#### 1.3、logstash配置

 logstash.yml：包含Logstash配置标志。您可以在此文件中设置标志，而不是在命令行中传递标志。在命令行上设置的任何标志都会覆盖logstash中的相应设置

 pipelines.yml：包含在单个Logstash实例中运行多个管道的框架和指令。

 jvm.options：包含JVM配置标志。使用此文件设置总堆空间的初始值和最大值。您还可以使用此文件为Logsta设置语言环境

 log4j2.properties：包含log4j 2库的默认设置

 start.options (Linux)：用于配置启动服务脚本

```yaml
node.name  #默认主机名，该节点的描述名字
path.data  #LOGSTASH_HOME/data ，Logstash及其插件用于任何持久需求的目录
pipeline.id #默认main，pipeline的id
pipeline.java_execution #默认true，使用java执行引擎
pipeline.workers #默认为主机cpu的个数，表示并行执行管道的过滤和输出阶段的worker的数量
pipeline.batch.size #默认125 表示单个工作线程在尝试执行过滤器和输出之前从输入中收集的最大事件数
pipeline.batch.delay #默认50 在创建管道事件时，在将一个小批分派给管道工作者之前，每个事件需要等待多长时间(毫秒)
pipeline.unsafe_shutdown  #默认false，当设置为true时，即使内存中仍有运行的事件，强制Logstash在关闭期间将会退出。默认情况下，Logstash将拒绝退出，直到所有接收到的事件都被推入输出。启用此选项可能导致关机期间数据丢失
pipeline.ordered #默认auto，设置管道事件顺序。true将强制对管道进行排序，如果有多个worker，则阻止logstash启动。如果为false，将禁用维持秩序所需的处理。订单顺序不会得到保证，但可以节省维护订单的处理成本
path.config #默认LOGSTASH_HOME/config  管道的Logstash配置的路径
config.test_and_exit #默认false，设置为true时，检查配置是否有效，然后退出。请注意，使用此设置不会检查grok模式的正确性
config.reload.automatic #默认false，当设置为true时，定期检查配置是否已更改，并在更改时重新加载配置。这也可以通过SIGHUP信号手动触发
config.reload.interval  #默认3s ，检查配置文件频率
config.debug #默认false 当设置为true时，将完全编译的配置显示为调试日志消息
queue.type #默认memory ，用于事件缓冲的内部排队模型。为基于内存中的遗留队列指定内存，或为基于磁盘的脱机队列(持久队列)指定持久内存
path.queue #默认path.data/queue  ,在启用持久队列时存储数据文件的目录路径
queue.page_capacity #默认64mb ，启用持久队列时(队列)，使用的页面数据文件的大小。队列数据由分隔为页面的仅追加数据文件组成
queue.max_events #默认0，表示无限。启用持久队列时，队列中未读事件的最大数量
queue.max_bytes  #默认1024mb，队列的总容量，以字节为单位。确保磁盘驱动器的容量大于这里指定的值
queue.checkpoint.acks #默认1024，当启用持久队列(队列)时，在强制执行检查点之前被隔离的事件的最大数量
queue.checkpoint.writes #默认1024，当启用持久队列(队列)时，强制执行检查点之前的最大写入事件数
queue.checkpoint.retry #默认false，启用后，对于任何失败的检查点写，Logstash将对每个尝试的检查点写重试一次。任何后续错误都不会重试。并且不推荐使用，除非是在那些特定的环境中
queue.drain #默认false，启用后，Logstash将等待，直到持久队列耗尽，然后关闭
path.dead_letter_queue#默认path.data/dead_letter_queue，存储dead-letter队列的目录
http.host #默认"127.0.0.1" 表示endpoint REST端点的绑定地址。
http.port #默认9600 表示endpoint REST端点的绑定端口。
log.level #默认info，日志级别fatal，error，warn，info，debug，trace，
log.format #默认plain 日志格式
path.logs  #默认LOGSTASH_HOME/logs 日志目录
```

#### 1.4、keystore

keystore可以保护一些敏感的信息，使用变量的方式替代，比如使用ES_PWD代替elasticsearch的密码，可以通过${ES_PWD}来获取elasticsearch的密码，这样就是的密码不再是明文密码。

```bash
./bin/logstash-keystore create   #创建一个keyword
 ./bin/logstash-keystore add ES_PWD #创建一个elastic的passwd，然后通过${ES_PWD}使用该密码
 ./bin/logstash-keystore list  #查看已经设置好的键值对
 ./bin/logstash-keystore remove ES_PWD #删除在keyword中的key
```

#### 1.5、logstash命令

注意：参数和logstash.yml配置文件对应

```yml
-n, --node.name NAME          
-f, --path.config CONFIG_PATH 
-e, --config.string CONFIG_STRING
--field-reference-parser MODE 
--modules MODULES             
-M, --modules.variable MODULES_VARIABLE
--setup                      
--cloud.id CLOUD_ID                                            
--cloud.auth CLOUD_AUTH       
--pipeline.id ID              
-w, --pipeline.workers COUNT 
--pipeline.ordered ORDERED   
--java-execution                                               
--plugin-classloaders         
-b, --pipeline.batch.size SIZE 
-u, --pipeline.batch.delay DELAY_IN_MS 
--pipeline.unsafe_shutdown    
--path.data PATH              
-p, --path.plugins PATH       
-l, --path.logs PATH         
--log.level LEVEL             
--config.debug                
-i, --interactive SHELL      
-V, --version                 
-t, --config.test_and_exit    
-r, --config.reload.automatic 
--config.reload.interval 
--http.host HTTP_HOST         
--http.port HTTP_PORT         
--log.format FORMAT           
--path.settings SETTINGS_DIR
```

## elasticsearch

#### 1、基本概念

Cluster 集群、Node节点、Index索引、Document文档、Shards & Replicas分片与副本等

#### 2、linux系统参数设置

```txt
1、设置系统配置
ulimit #暂时修改，切换到该用户es，ulimit -n 65535 
/etc/security/limits.conf #永久修改 es -  nofile  65535
ulimit -a #查看当前用户的资源限制

2、禁用sawpping
方式一：
swapoff -a #临时禁用所有的swap文件
vim /etc/fstab #注释掉所有的swap相关的行，永久禁用

方式二：
cat /proc/sys/vm/swappiness #查看该值
sysctl vm.swappiness=1 #临时修改该值为1
vim /etc/sysctl.conf #修改文件 永久生效
vm.swappiness = 1 #如果有该值，则修改该值，若没有，则追加该选项，sysctl -p生效命令

方式三：
配置elasticsearch.yml文件，添加如下配置：
bootstrap.memory_lock: true
GET _nodes?filter_path=**.mlockall  #检查如上配置是否成功
注意：如果试图分配比可用内存更多的内存，mlockall可能会导致JVM或shell会话退出!

3、配置文件描述符
ulimit -n 65535  #临时修改
vim /etc/security/limits.conf #永久修改
es         soft    nproc     65535
es         hard    nproc     65535

4、配置虚拟内存
sysctl -w vm.max_map_count=262144 #临时修改该值
vim /etc/sysctl.conf #永久修改
vm.max_map_count=262144

5、配置线程数
ulimit -u 4096 #临时修改
vim /etc/security/limits.conf #永久修改
```

#### 3、es安装

es需要其他用户启动的，因此需要线创建一个新用户的elk；

```bash
groupadd elatic
useradd elk -d /data/hd05/elk -g elastic
echo '2edseoir@' | passwd elk --stdin
```

下载

```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.7.0-linux-x86_64.tar.gz
#建立软连接
ln –s elasticsearch-7.7.0  es
```

配置java参数jvm.options

```options
配置java参数
一种是通过修改/data/hd05/elk/elasticsearch-7.7.0/config/jvm.options文件修改jvm参数，一个使用过一个变量ES_JAVA_OPTS来声明jvm参数
/data/hd05/elk/elasticsearch-7.7.0/config/jvm.options介绍：
8:-Xmx2g  #表示只适合java8
8-:-Xmx2g  #表示适合高于java8的版本
8-9:-Xmx2g #表示适合java8，和java9
其他配置，都是jvm的相关参数，如果要想明白，得去看java虚拟机

通过变量ES_JAVA_OPTS来声明jvm参数：
例如：export ES_JAVA_OPTS="$ES_JAVA_OPTS -Djava.io.tmpdir=/path/to/temp/dir"
./bin/elasticsearch
```

配置加密通信证书

生成证书：

- 方法一：

```bash
./bin/elasticsearch-certutil ca -out config/elastic-certificates.p12 -pass "password"
```

查看config目录，有elastic-certificates.p12文件生成

- 方法二：

```bash
./bin/elasticsearch-certutil ca  #创建集群认证机构，需要交互输入密码
./bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12  #为节点颁发证书，与上面密码一样
执行./bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password 并输入第一步输入的密码 
执行./bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password 并输入第一步输入的密码 
将生成的elastic-certificates.p12、elastic-stack-ca.p12文件移动到config目录下
```

elasticsearch.yml

```yaml
[elk@lgh config]$ cat  elasticsearch.yml  | egrep -v '^$|#'
cluster.name: my_cluster
node.name: lgh01
node.data: true
node.master: true
path.data: /data/hd05/elk/elasticsearch-7.7.0/data
path.logs: /data/hd05/elk/elasticsearch-7.7.0/logs
network.host: 192.168.110.130
http.port: 9200
transport.tcp.port: 9300
discovery.seed_hosts: ["192.168.110.130","192.168.110.131","192.168.110.132","192.168.110.133"]
cluster.initial_master_nodes: ["lgh01","lgh02","lgh03"]
cluster.routing.allocation.cluster_concurrent_rebalance: 32
cluster.routing.allocation.node_concurrent_recoveries: 32
cluster.routing.allocation.node_initial_primaries_recoveries: 32
http.cors.enabled: true
http.cors.allow-origin: '*'#下面的是配置x-pack和tsl/ssl加密通信的

xpack.security.enabled: true
xpack.license.self_generated.type: basic
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12

bootstrap.memory_lock: false   #centos6需要配置
bootstrap.system_call_filter: false #centos6需要配置
```

然后通过scp到其他的节点，修改上面的node.name和node.master参数，然后要删除data目标，不然会存在报错

然后使用./bin/elasticsearch -d 后台启动elasticsearch，去掉-d则是前端启动elasticsearch

然后./bin/elasticsearch-setup-passwords interactive 配置默认用户的密码：（有如下的交互），可以使用auto自动生成。

