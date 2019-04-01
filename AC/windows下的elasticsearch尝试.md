# windows下的elasticsearch尝试


环境：windows7 64位

elasticsearch（后面简称es）基于java，所以务必先安装java，java安装省略，建议安装最新的稳定版。

## 安装es

[es下载][1]

解压后进入目录，启动bin/elasticsearch.bat

访问：
> http://localhost:9200/

可以看到一些信息，版本等，就表明已经安装病启动成功！


### 安装HEAD插件

> git clone git://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install
npm run start


浏览器打开， http://localhost:9100/，可以看到刚开启动的ES实例。


### 伪集群

打开config下得elasticsearch.yml

加入以下配置：
```
# 自定义配置
cluster.name: sai_es
node.name: es0
node.master: true
http.port: 9200
```

复制es文件夹到es1,es2,修改配置:

```
es1:
# 自定义配置
cluster.name: sai_es
node.name: es1
node.master: false
http.port: 9201
discovery.zen.ping.unicast.hosts: ["127.0.0.1"]

es2:
# 自定义配置
cluster.name: sai_es
node.name: es2
node.master: false
http.port: 9202
discovery.zen.ping.unicast.hosts: ["127.0.0.1"]
```

注意，此时要保证es1，es2下面data目录为空，否则启动时会因为冲突id连接不上es0。

然后启动es1，es2，通过head插件，我们可以看到集群搭建完成！sai_es集群下有3个实例：es0，es1，es2。




  [1]: https://www.elastic.co/downloads/elasticsearch