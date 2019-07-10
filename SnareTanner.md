参考链接：[http://phantom0301.cc/2019/03/13/snare-and-tanner/](http://phantom0301.cc/2019/03/13/snare-and-tanner/)


一、蜜罐介绍
  Snare/Tanner是web蜜罐Glastopf 的继任者。SNARE是一个Web应用程序蜜罐传感器，可以吸引来自Internet的各种恶意。TANNER是一种远程数据分析和分类服务，用于评估HTTP请求并组成SNARE事件服务的响应。snare和tanner联合使用，作者也在snare中提供了搭建好的一个默认的tanner，但由于网络等原因，建议还是使用自己的Tanner。

二、功能及命令介绍
```
sudo python3 setup.py install

|--SNARE:低交互可以模拟任意网站的蜜罐生成套件
|  |--cloner
|  |  |--爬取网站，存储路由与页面的对应信息
|  |  |--sudo clone --target http://www.exam.com
|  |  |--HTTP请求加BeautifulSoup解析，存储路由和它的hash表。
|  |--server
|  |  |--解析存储的网页，接受请求路由进行匹配
|  |  |--服务器使用Jinja来加载网页文件，使用nsq来做信息传输。
|  |  |--沿用了Glastopf的Dork机制，开启参数后，就可以看到被嵌入到网页中的隐藏堆叠指纹。
|  |--tanner_handler
|  |  |--把请求包装发给Tanner并接收返回的数据
sudo snare --host 0.0.0.0 --port 8080 --page-dir www.exam.com --tanner 127.0.0.1 --no-dorks False

|--TANNER：进一步的攻击反馈和数据分析
|  |--server
|  |  |--负责接收Snare传输的信息
|  |  |--sudo tanner
|  |  |--SessionManager、DorksManager、BaseHandler、HPFEEDS
|  |  |  |--BaseHandler【php执行：php沙盒运行响应；SQL注入：SQLite模拟/Mysql模拟；系统命令执行：Docker中的busybox等】
|  |  |  |--HPFEEDS：sudo tanner --config tanner.config修改
|  |--api
|  |  |--提供json格式的信息反馈，数据存储于Redis中
|  |  |--sudo tannerapi
|  |--web
|  |  |--提供可视化的信息汇聚分析
|  |  |--sudo tannerweb
```

三、待改进
1、tanner的攻击检测基于正则关键字提取，且对攻击类型的判断过于片面，通过设置order字段进行攻击类型的确定过于狭隘。因此在攻击检测与分析处可加入更有效的检测算法。
2、php沙盒可待研究。
3、snare的爬虫机制模拟出来的站点还有待提高。

四、日志结构分析
```json
"session":
{
    "status": 200,
    "cookies":
    {
        " __51laig__": "1",
        "__51cke__": "",
        " __tins__18773325": "%7B%22sid%22%3A%201557743288815%2C%20%22vd%22%3A%201%2C%20%22expires%22%3A%201557745088815%7D",
        " sess_uuid": "c7a998b9-31d8-4221-abb8-8ae13fd89cd9",
        "sess_uuid": null
    },
    "uuid": "d05e9ed6-e1db-4a3e-b418-8aeb3cc79bbd", # uuid是snare的id。
    "headers":
    {
        "accept-language": "zh-cn",
        "accept-encoding": "gzip, deflate",
        "connection": "keep-alive",
        "accept": "image/png,image/svg+xml,image/*;q=0.8,video/*;q=0.8,*/*;q=0.5",
        "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_4) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.1 Safari/605.1.15",
        "host": "10.211.55.31:8080",
        "cookie": "__51cke__=; __51laig__=1; __tins__18773325=%7B%22sid%22%3A%201557743288815%2C%20%22vd%22%3A%201%2C%20%22expires%22%3A%201557745088815%7D; sess_uuid=c7a998b9-31d8-4221-abb8-8ae13fd89cd9",
        "referer": "http://10.211.55.31:8080/"
    },
    "response_msg":
    {
        "version": "0.6.0",
        "response":
        {
            "message":
            {
                "detection":
                {
                    "version": "0.6.0",
                    "type": 1,
                    "name": "index",
                    "order": 1
                },
                "sess_uuid": "6a308dd1-9fcc-476f-a659-b472ebfb55eb"
            }
        }
    },
    "peer":
    {
        "ip": "10.211.55.2",
        "port": 53833
    },
    "path": "/statics/antai/images/lanmu11.jpg",
    "method": "GET"
}
```