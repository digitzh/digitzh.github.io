参考文档：黑马06-zookeeper集群安装.doc
### 1 下载并解压

在node1上下载：
[Index of /zookeeper/stable](https://downloads.apache.org/zookeeper/stable/)（本文采用3.8.4）
```sh
tar -zxvf zookeeper.tar.gz -C .
ln -s apache-zookeeper-3.8.4-bin zookeeper
```
## 2 修改配置文件

```sh
cd zookeeper/conf
cp zoo_sample.cfg zoo.cfg
mkdir ../zkdata
sudo vim zoo.cfg
```
在zoo.cfg中：
```sh
# 第12行修改为：
dataDir=/home/user/Work/2025/zookeeper/zkdata
# 解除25和28行的注释：
autopurge.snapRetainCount=3
autopurge.purgeInterval=1
# 末尾添加：
server.1=node1:2888:3888
server.2=node2:2888:3888
server.3=node3:2888:3888
```
### 4 配置myid

在node1上，在zkdata中创建名为"myid"的、内容为1的文件：
```sh
echo 1 > ../zkdata/myid
```
分发：
```sh
cd ../..
scp -r ./apache-zookeeper-3.8.4-bin node2:$PWD
scp -r ./apache-zookeeper-3.8.4-bin node3:$PWD
```
第2台机器上建立软连接, 并修改myid的值为2：
```sh
cd ./Work/2025
ln -s apache-zookeeper-3.8.4-bin/ zookeeper
echo 2 > zookeeper/zkdata/myid
```
第3台同理：
```sh
cd ./Work/2025
ln -s apache-zookeeper-3.8.4-bin/ zookeeper
echo 3 > zookeeper/zkdata/myid
```
## 5 启动服务

3台机器分别执行：
```sh
~/Work/2025/zookeeper/bin/zkServer.sh start
```
查看状态：
```sh
~/Work/2025/zookeeper/bin/zkServer.sh status
```
各节点状态如下：
```sh
user@ubuntu-node1:~/Work/2025$ ~/Work/2025/zookeeper/bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/user/Work/2025/zookeeper/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: follower

user@ubuntu-node2:~/Work/2025$ ~/Work/2025/zookeeper/bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/user/Work/2025/zookeeper/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: leader

user@ubuntu-node3:~/Work/2025$ ~/Work/2025/zookeeper/bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/user/Work/2025/zookeeper/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: follower
```
