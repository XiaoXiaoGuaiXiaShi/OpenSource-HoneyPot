###### 一、蜜罐介绍
opencanary是2015年blackhat在单独发布环节推出的的一款蜜罐工具，纯python模拟多种应用和服务。当模拟的服务被某人使用（交互登录）时，它就会产生相应的日志。可以理解为探针，发现并记录服务器上的恶意行为。


###### 二、安装使用
1、安装脚本
```shell
#!/bin/bash
sudo apt-get install gcc
# ubuntu18.04自带python3，但是没有python2，pip2，pip3。
sudo apt-get install python-dev python-pip python-virtualenv
virtualenv env/
. env/bin/activate

# rdpy（optional）
sudo apt-get install -y build-essential libssl-dev libffi-dev python-dev
pip install rdpy

pip install -i https://pypi.tuna.tsinghua.edu.cn/simple opencanary
# optional
pip install scapy pcapy

opencanaryd --copyconfig
opencanaryd --start
ps -ef|grep opencanary
```

2、相关命令
> - 1、日志存放位置：/var/tmp/opencanary.log
> - 2、修改配置文件：~/.opencanary.conf，这里可以设置虚假的端口并设置对应端口的 banner 信息。通过查看配置文件可以模拟ftp(21)、http(80)、httpproxy、smb(445)、mysql(3306)、mssql(1433)、ssh(22)、rdp(3389)、telnet(23)等服务。
> - 3、工具JQ可用于检查配置文件是否格式正确：jq . ~/.opencanary.conf
> - 4、在前台运行opencanaryd以查看更多错误消息：opencanaryd --dev
> - 5、重启服务：opencanaryd --restart

3、日志中可以获取到的数据
```
目标 IP：10.59.0.84
目标端口：22
时间：2018-09013 08:25:13.085117
log 数据
本地服务信息（名称及其版本）
远端服务信息（名称及其版本）
用户名 root
密码 asdfa
log 类型：4002
节点id:opencanary-1
源 IP ：192.168.190.91
源 port：33995
```


###### 三、优缺点

1、优点：可以模拟 TCP 的众多常用端口，例如：80,8080（http）、21（ftp）、22（ssh）、3306（MySQL）。
> 说明：Opencanary v0.3.2及其以下版本无法成功地检测到SYN包的扫描，但其之后的版本(如现在的0.5.2)可以监控到这些扫描方式。例如：nmap 的 -sS 扫描，-Pn、-sV。

2、缺点：smb需要依赖系统真实的smb服务。


###### 参考链接
1、[http://pirogue.org/2019/01/15/opencanary_2/#HTTP](http://pirogue.org/2019/01/15/opencanary_2/#HTTP)

2、[http://vic.pub/opencanary/](http://vic.pub/opencanary/)
