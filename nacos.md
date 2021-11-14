# 下载安装运行

下载后用maven打包

```bash
git clone https://github.com/alibaba/nacos.git
cd nacos/
mvn -Prelease-nacos -Dmaven.test.skip=true clean install -U  
ls -al distribution/target/

// change the $version to your actual path
cd distribution/target/nacos-server-$version/nacos/bin
```

进入bin目录后修改startup.cmd

> set MODE="standalone" //一开始是cluster，单体开启

修改conf目录下的application.properties，配置数据库等信息

# 创建服务提供者和消费者(后台开启nacos)

创建父工程，其依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.feige</groupId>
    <artifactId>nacos</artifactId>
    <packaging>pom</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>nacos</name>
    <description>Demo project for Nacos</description>

    <modules>
        <module>nacos-provider</module>
        <module>nacos-consumer</module>
    </modules>
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.RELEASE</spring-cloud.version>
        <nacos.version>0.2.2.RELEASE</nacos.version>
        <spring-boot.version>2.2.4.RELEASE</spring-boot.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${nacos.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
        </repository>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
        </pluginRepository>
        <pluginRepository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </pluginRepository>
    </pluginRepositories>

</project>

```

### 创建服务提供者

其依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.feige</groupId>
        <artifactId>nacos</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.feige</groupId>
    <artifactId>nacos-provider</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>nacos-provider</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

#### 创建服务

```java
@RestController
@EnableDiscoveryClient//开启服务注册功能
@SpringBootApplication
public class NacosProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(NacosProviderApplication.class, args);
    }

    @RequestMapping("/hello")
    public String hello(){
        return "hello,nacos";
    }
}
```

配置文件

```yaml
server:
  port: 9527
spring:
  application:
    name: nacos-provider
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848

```

### 服务消费者

依赖和提供者相同

### 创建消费者

```java
@RestController
@EnableDiscoveryClient
@SpringBootApplication
public class NacosConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(NacosConsumerApplication.class,args);
    }

    @Autowired
    private RestTemplate restTemplate;

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }

    @RequestMapping("/he")
    public String test(){
        return restTemplate.getForObject("http://nacos-provider/hello",String.class);
    }
}

```

#### 配置文件

```yaml
server:
  port: 9528
spring:
  application:
    name: nacos-consumer
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
```

# 配置中心

在nacos下新建nacos-config项目

主启动类

```java
@SpringBootApplication
@RestController
public class NacosConfigApplication {
    public static void main(String[] args) {
        SpringApplication.run(NacosConfigApplication.class,args);
    }

    @NacosValue(value = "${service.name:1}",autoRefreshed = true)
    private String serverName;

    @RequestMapping(value = "/test",method = RequestMethod.GET)
    public String get(){
        return serverName;
    }
}
```

### 配置文件获取serverName

```yml
server:
  port: 9529
spring:
  application:
    name: nacos-config
nacos:
  config:
    server-addr: 127.0.0.1:8848
#注意发现server的配置和正常使用的不一样，nacos.config
```

### 客户端配置文件类型设置

在bootstrap.properties文件中
spring.cloud.nacos.config.file-extension=properties,yml,yaml
属性声明从配置中心中读取的配置文件格式
该配置的缺省值为properties，即默认是读取properties格式的配置文件。当客户端没有配置该属性，并且在nacos server添加的是yml格式的配置文件，则给客户端会读取不到配置文件，导致启动失败。
非properties配置格式，必须添加如下配置才可生效

> spring.cloud.nacos.config.file-extension=yml

根据profile设置不同的环境配置

springboot中可以通过配置spring.profiles.active 实现在开发、测试、生产环境下采用不同的配置文件
同样，我们也可以在nacos server分别创建

> ${application.name}-dev.properties 
>
> ${application.name}-test.properties 
>
> ${application.name}-prod.properties

然后通过命令启动jar时 设置spring.profiles.active来实现不同环境下使用不同的配置文件。

>java -jar nacos-client-0.0.1-SNAPSHOT.jar --spring.profiles.active=test

同样也适用于yml/yaml文件，只是客户端设置spring.cloud.nacos.config.file-extension=yaml具体可见上一个说明

### 服务中心使用mysql保存数据

在0.7版本之前，在单机模式时nacos使用嵌入式数据库实现数据的存储，不方便观察数据存储的基本情况。0.7版本增加了支持mysql数据源能力，具体的操作步骤：

1.安装数据库，版本要求：5.6.5+
2.初始化mysql数据库，数据库初始化文件：nacos-mysql.sql
3.修改conf/application.properties文件，增加支持mysql数据源配置（目前只支持mysql），添加mysql数据源的url、用户名和密码。

#### 具体操作

1、在mysql server新建数据库：nacos(名字随意)

2、在nacos server的conf目录下找到nacos-mysql.sql文件，并在创建的nacos数据库执行该sql文件

3、修改nacos server application.properties配置文件，修改后如图

```properties
# spring
 
server.contextPath=/nacos
server.servlet.contextPath=/nacos
server.port=8848
 
spring.datasource.platform=mysql
 
db.num=1
db.url.0=jdbc:mysql://数据库IP:端口号/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=数据库用户名
db.password=数据库密码


```

4、重启Nacos server并添加怕配置文件，就可以通过mysql数据库数据表中出现了配置文件内容

注：由嵌入式数据库切换为mysql数据库后，数据并不能自动转移到mysql中，导致之前的配置文件丢失

注 ：当然为了可用性较高，生产使用建议至少主备模式，或者采用高可用数据库。

