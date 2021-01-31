Whaler

参考链接：
1、https://github.com/oncyberblog/whaler
2、https://oncyberblog.wordpress.com/2018/06/14/dockerpot-a-docker-honeypot/


> Whaler是Docker Daemon蜜罐/沙箱，它暴露了一个不安全的Docker Daemon API，旨在吸引和捕获尝试运行恶意容器的尝试。Whaler完全在Docker中运行，该解决方案的核心是Docker in Docker（DinD）容器，该容器充当蜜罐。


#### 一、介绍
**1、架构**
Whaler作为单个docker-compose堆栈运行，该堆栈由以下容器组成：
- 1）容器：受害者
    Docker（DinD）容器中的Docker，在端口2375上暴露了不安全的守护程序。没有容器部署到此DinD主机中。由于DinD的要求，该容器必须以特权身份运行，因此可以很容易地破坏主机的假设（攻击者应意识到“主机”实际上是另一个容器！）。
- 2）容器：控制器
控制器执行以下主要功能：

**2、优点**


#### 二、安装和运行
**1、安装**
```shell
1、cd /opt && git clone https://github.com/oncyberblog/whaler
2、安装依赖：

```

**2、运行**
```shell
运行
```

#### 三、配置文件
> 1、主配置文件：/opt/HoneyPy/etc/honeypy.cfg
2、服务配置文件：/opt/honeyPy/etc/services.cfg

**1、服务配置**


**2、记录配置**
记录器：查看/etc/honeypy.cfg文件，对于要启用的记录器，您需要为每个记录器指定所需的配置信息。然后只需将启用的配置选项更改为是并重新启动HoneyPy。
- 1、Twitter
- 2、HoneyDB
- 3、Slack：将事件发布到slack通道
- 4、记录存储：发布事件以记录存储
- 5、Elastic search：将事件直接发布到Elasticsearch



#### 四、注意
1、Whaler应该在隔离的服务器（例如，Cloud）上运行，并假定熟练的黑客将能够轻而易举地绕过Docker容器以获得服务器上的root。
2、可以设置蜜罐用于远程Docker日志记录，并且compose文件允许与免费的logz.io帐户轻松集成。请注意，请勿连接付费帐户，因为如果攻击者破坏主机并破坏您的日志记录预算，这也很容易被滥用。
