参考链接：
- http://www.freebuf.com/column/147545.html
- https://www.secpulse.com/archives/2480.html
- http://chuansong.me/n/1551568851212
- https://www.cnblogs.com/lijingchn/p/5294954.html


## 基础介绍
> 从2010年的震网到近年的Havex，工控网络作为一个相对封闭的网络则出现了越来越多更具有针对性的攻击事件和恶意程序。一些黑客使用Shodan、Zoomeye、Fofa等搜索引擎，可以快速探查特定组织暴露在网络空间中的设备资产。作为一种相对主动的安全检测手段 ，有针对地模拟工控系统某些特征的蜜罐变得十分必要。


## 工控蜜罐分类
| 蜜罐      |    分类     | 特点                      |
| :------: | :--------:| :------------: |
| Conpot  |  低交互    |  可以实现对安装有基本扩展模块的西门子S7-200 PLC的仿真   |
| Snap7    |  中等交互 |          PLC蜜罐                  |
| CryPLH  | 中等交互 |  模拟S7-300 PLC |
| XPOT     |  高交互     | 模拟西门子S7-314C-2 PN / DP，也能够模拟几乎任何其他的S7-300/400模型  |


## 开源工控蜜罐Conpot
1、Conpot介绍
> Conpot是一个部署在服务端的低交互ICS蜜罐，能够快速地部署、修改和拓展。开发者通过提供一系列的通用工控协议，使得我们能够非常快速地在我们的系统上构建一套复杂的工控基础设施用于欺骗未知的攻击者。 Conpot一共支持bacnet、enip、guardian_ast、http、ipmi、kamstrup、misc、modbus、s7comm和snmp等10个协议。

2、支持协议
| 协议      | 介绍                         |
| :------: | :-------------------: |
| bacnet  |  用于智能建筑的通信协议   |  
| enip    |  基于通用工业协议               |    
| ipmi  | 智能平台管理接口，可以智能地监视、控制和自动回报大量服务器的运作状况               |  
| modbus     |  一种串行通信协议 |
| s7comm     |  西门子S7通讯协议簇里的一种 |
| http     |  超文本传输协议 |
| snmp     |  简单的网络管理协议 |


3、环境搭建及安装
**注意：使用docker安装后的docker不是直接在conpot目录下，而应该在/home/conpot/.local/bin下。**
```
三种安装方式：
- Pre-Build镜像：
  （1）、docker pull honeynet/conpot
  （2）、docker run -it -p 80:80 -p 102:102 -p 502:502 -p 161:161/udp --network=bridge honeynet/conpot:latest /bin/sh
  （3）、conpot --template default
- 源代码编译
  （1）、git clone https://github.com/mushorg/conpot.git
  （2）、cd conpot
  （3）、docker build -t conpot .
  （4）、docker run -it -p 80:80 -p 102:102 -p 502:502 -p 161:161/udp --network=bridge conpot
- ubuntu安装
  （1）、sudo apt-get install libsmi2ldbl snmp-mibs-downloader python-dev libevent-dev libxslt1-dev libxml2-dev
  （2）、pip install conpot
```
4、配置文件
**注意不要使用协议的默认配置**

5、日志分析
不同协议的脚本不同，日志格式也不一样。

