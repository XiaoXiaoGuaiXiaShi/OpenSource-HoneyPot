参考链接：
1、[https://labs.signalsciences.com/getting-started-with-honeypy-part-3](https://labs.signalsciences.com/getting-started-with-honeypy-part-3)
2、[https://honeypy.readthedocs.io/en/latest/plugins/](https://honeypy.readthedocs.io/en/latest/plugins/)


> HoneyPy附带了很多插件。交互级别由使用的插件的功能决定。可以创建插件来模拟基于UDP或TCP的服务，以提供更多交互。默认情况下，所有活动都会记录到文件中，但也可以配置将蜜罐活动发布到Twitter或Web服务端点。


#### 一、介绍
**1、环境**
HoneyPy主要使用Python 2.7.9在Debian和Ubuntu上进行测试和运行。

**2、优点**
- 1）、HoneyPy构建为可扩展，因此您可以轻松地为基于TCP或UDP的协议添加新的服务仿真（插件）。但是，它确实带有一小组基本插件，在此列出，您可以运行或用作编写自己的插件的起点。此外，配置要启用的插件就像编辑services.cfg文件一样简单。
- 2）、另一个很棒的功能是它与Twitter，HoneyDB，Slack，Logstash和Elasticsearch等其他工具的事件日志处理程序集成。这些事件日志处理程序旨在使消费日志事件更加方便。HoneyPy日志处理程序不是编写脚本来解析日志文件并生成警报，而是为您完成此操作。创建新的事件日志处理程序相对简单，因此工具列表将继续增长。
- 3）、易于部署。

#### 二、安装和运行
**1、安装**
```shell
1、cd /opt && git clone https://github.com/foospidy/HoneyPy.git
2、安装依赖：
apt install virtualenv
apt-get install -y python-requests python-twisted python-pip
3、virtualenv -p $PYTHON env
4、. env/bin/activate
5、sudo pip install -r requirements.txt
6、./Honey.py
```

**2、运行**
```shell
运行（不要以root用户身份运行）：
1、控制台模式 ./Honey.py
2、守护进程模式：/opt/Honey.py -d &
3、启动脚本 etc/honeypyd（Debian测试）
4、使用屏幕实用程序在控制台模式下运行：apt-get install screen.
创建：screen -S MyHoneyPyConsole
恢复：screen -r MyHoneyPyConsole
5、开始：start
```

#### 三、配置文件
> 1、主配置文件：/opt/HoneyPy/etc/honeypy.cfg
2、服务配置文件：/opt/honeyPy/etc/services.cfg

**1、服务配置**
1）cp etc/profiles/ services.linux.profile etc/services.cfg
    使用list profiles命令查看可用的服务配置文件。
    使用set profile命令启用配置文件。
    使用cp profiles/service.default.profile service.cfg恢复到默认服务配置文件。
2）配置HoneyPy服务：/etc/services.cfg告诉HoneyPy要运行哪些服务以及运行它们的端口的文件。目录中还有几个附加的服务配置文件etc/profiles。服务配置文件用于定义在蜜罐上运行的服务名称，端口和插件。文件中定义的每个服务都有一个enabled选项。此选项可以设置为“是”或“否”，以确定在启动时运行的服务。您也可以使用提供的配置文件之一，或创建自己的配置文件。要使用其他文件之一，只需通过service.cfg复制该文件即可。
```
[Echo] //指定了服务的名称，服务名称不得包含空格。
plugin = Echo //要加载哪个插件用于服务仿真，所有可用的插件都存在于插件目录中，它们模拟telnet、ftp、ssh等服务。
low_port = tcp: 7  //如果服务要在低端口上侦听，需要在此处指定端口号。否则，该值应与下一行port相同。注意，这也指定了服务的协议，tcp或udp。要使用的格式是<protocol>：<端口号>。
port = tcp: 10007 // 这是HoneyPy实际上会听的端口。注意，这也指定了服务的协议，tcp或udp。要使用的格式是<protocol>：<端口号>。
description = 回显通过tcp接收的数据  //描述是对该服务的作用的简短描述。
启用 = Yes //具有值Yes或No，这告诉HoneyPy应该在启动时启用还是禁用此服务。
```

**2、记录配置**
记录器：查看/etc/honeypy.cfg文件，对于要启用的记录器，您需要为每个记录器指定所需的配置信息。然后只需将启用的配置选项更改为是并重新启动HoneyPy。
- 1、Twitter
- 2、HoneyDB
- 3、Slack：将事件发布到slack通道
- 4、记录存储：发布事件以记录存储
- 5、Elastic search：将事件直接发布到Elasticsearch

```shell
[honeypy]
nodename  = honeypy //此HoneyPy节点的名称将显示在推文，Slack消息和其他集成中。
#whitelist = 192.168.0.5, 192.168.0.21
#service_profiles = services.databases.profile, services.linux.profile
limit_internal_logs = No  // 启用此选项将确保内部日志文件限制为一天，这对于有限部署（例如自动容器）（例如是或否）非常有用。
internal_log_dir = log/  //内部HoneyPy日志的目录（不是外部记录器）。使用前导斜杠表示绝对路径，或省略相对路径。默认：log/

// honeypy.cfg配置文件中的其余部分用于配置记录器。记录器是通过将事件数据发送到另一个服务来使消费或处理事件数据更方便的模块。

//注意：一次可以配置多个记录器。例如，如果配置了Elasticsearch，HoneyDB和RabbitMQ记录器，则所有事件都将发送到所有三个服务。

//注意：默认情况下，HoneyPy会将所有事件记录到日志文件中/opt/HoneyPy/log/honeypy.log。无论是否配置了记录器，所有事件都会记录到此文件中。
```


#### 四、HoneyPy插件
**1、现有的插件**
```
|  |--DNS
|  |  |--中交互，使用随机IP地址响应DNS查询。
|  |  |--UDP：plugins/DnsUdp
|  |--Echo
|  |  |--低交互，Echos支持连接客户端发送的数据。
|  |  |--tcp: plugins/Echo
|  |  |--udp: plugins/Echo_udp
|  |--Elasticsearch：
|  |  |--低交互，简单的Elasticsearch仿真。 响应/请求节点信息/ _nodes和搜索请求/ _search的请求。
|  |  |--tcp: plugins/Elasticsearch
|  |--HashCountRandom
|  |  |--低交互，虚假服务。 对于由连接的客户端发送的每个数据实例，它递增计数器并返回计数的md5散列和一些随机数据。
|  |  |--tcp: plugins/HashCountRandom
|  |--MOTD
|  |  |--低交互，对于每个连接，它返回一条消息并关闭连接。
|  |  |--tcp: plugins/MOTD
|  |  |--udp: plugins/MOTD_udp
|  |--NTP
|  |  |--低交互，接受NTP请求，返回包含当前系统时间的数据包。
|  |  |--udp: plugins/NtpUdp
|  |--Random
|  |  |--低交互，对于连接的客户端发送的每个数据实例，它返回一些随机数据。
|  |  |--tcp: plugins/Random
|  |--SIP
|  |  |--低交互，简单SIP（会话启动协议）仿真。
|  |  |--udp: plugins/SIP
|  |--SMTP
|  |  |--低交互，简单的SMTP服务器模拟。 提供与命令子集的交互。
|  |  |--tcp: plugins/SmtpExim
|  |--TFTP
|  |  |--中交互，TFTP（普通文件传输协议）仿真。
|  |  |--udp: plugins/TFTP
|  |--Telnet
|  |  |--中交互，模拟telnet服务。
|  |  |--tcp: plugins/TelnetUnix
|  |--Web
|  |  |--低交互，简单的Web服务器模拟。 响应/robots.txt，/ wp-login.php和各种PhpMysqlAdmin页面。 所有其他请求都会收到简单的200 OK响应。
|  |  |-- tcp: plugins/Web
```
**2、开发插件**
1）HoneyPy负责发送/接收数据和记录，您所要做的就是编写自定义协议/逻辑。plugins目录中的服务模拟器可用作模板来创建新的模拟器。
2）要编辑的部分有三个：自定义导入，自定义协议和自定义功能。为了使模板井井有条，您只应在指定的部分中进行修改，并注意表示每个部分的注释。
```python
（1）导入所需模块
### START CUSTOM IMPORT
import time
import os
import md5
### END CUSTOM IMPORT
（2）使用自定义协议部分编写逻辑。使用该self.tx()函数将数据传输（发送）到套接字，并使用该self.rx()函数从套接字接收数据。
### START CUSTOM PROTOCOL ###########################################################
self.tx('ACCEPT_CONN: ' + str(self.remote_host) + ':' + str(self.remote_port) + '\n')
count = 0

while True:
    count = count + 1
    self.tx(self.md5sum(count) + ':' + str(os.urandom(99)) + '\n')
    self.rx()
    time.sleep(1)

### END CUSTOM PROTOCOL #############################################################
（3）根据需要在底部添加自定义功能。所有函数必须具有第一个参数be self。从自定义协议部分调用自定义函数时，必须为它们添加前缀self.，例如：self.md5sum('test')。
### START CUSTOM FUNCTIONS ##########################################################
def md5sum(self, data):
    m = md5.new()
    m.update(str(data))
    return m.hexdigest()

### END CUSTOM FUNCTIONS ############################################################
```


#### 五、注意
1、避免使用root身份运行HoneyPy，但这也意味着HoneyPy将无法侦听端口1到1024。处理在低端口上运行HoneyPy服务的问题：
- 使用iptables来实现，以便在低端口上启用端口转发到HoneyPy可以使用的高端口。
- 使用ipt-kit为HoneyPy配置iptables（https://github.com/foospidy/ipt-kit），需要以root身份运行ipt-kit来修改IPTables。一旦重定向规则到位，无需以低端口的root身份运行HoneyPy。

