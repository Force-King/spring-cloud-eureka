# Spring Cloud registry - Eureka

## spring cloud 注册中心，服务注册与发现

#### 支持集群模式、ELK日志


### 编译打包jar
````
mvn clean install -DskipTests -U
````

### 本地启动：

- 修改 hosts
````
127.0.0.1 eureka.peer1
127.0.0.1 eureka.peer2
127.0.0.1 eureka.peer3
````
- 启动命令
````
$ nohup java -jar framework-eureka.jar --spring.profiles.active=peer1 > nohup1.log &
$ nohup java -jar framework-eureka.jar --spring.profiles.active=peer2 > nohup2.log &
$ nohup java -jar framework-eureka.jar --spring.profiles.active=peer3 > nohup3.log &
````

### 线上环境启动步骤：

#### 1. hosts文件配置
vi /etc/hosts
````
192.168.100.1 eureka.peer1
192.168.100.2 eureka.peer2
192.168.100.3 eureka.peer3
````

#### 2. 创建集群启动脚本


- 节点1启动脚本 start_peer1.sh

````
#!/bin/sh

nohup java \
-server -Xms2g -Xmx2g -Xmn1g \
-XX:+UseParNewGC -XX:+UseConcMarkSweepGC \
-XX:ParallelGCThreads=4 -XX:TargetSurvivorRatio=90 \
-verbose:gc -Xloggc:logs/gc.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps \
-XX:+PrintHeapAtGC -XX:+HeapDumpOnOutOfMemoryError \
-jar framework-eureka.jar --spring.profiles.active=peer1 >> eureka_peer.log 2>&1 &
````
 
- 节点2启动脚本 start_peer2.sh

```
#!/bin/sh

nohup java \
-server -Xms2g -Xmx2g -Xmn1g \
-XX:+UseParNewGC -XX:+UseConcMarkSweepGC \
-XX:ParallelGCThreads=4 -XX:TargetSurvivorRatio=90 \
-verbose:gc -Xloggc:logs/gc.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps \
-XX:+PrintHeapAtGC -XX:+HeapDumpOnOutOfMemoryError \
-jar framework-eureka.jar --spring.profiles.active=peer2 >> eureka_peer.log 2>&1 &
```

- 节点3启动脚本 start_peer3.sh

``` 
#!/bin/sh

nohup java \
-server -Xms2g -Xmx2g -Xmn1g \
-XX:+UseParNewGC -XX:+UseConcMarkSweepGC \
-XX:ParallelGCThreads=4 -XX:TargetSurvivorRatio=90 \
-verbose:gc -Xloggc:logs/gc.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps \
-XX:+PrintHeapAtGC -XX:+HeapDumpOnOutOfMemoryError \
-jar framework-eureka.jar --spring.profiles.active=peer3 >> eureka_peer.log 2>&1 &
```

#### 3. 启动服务

````
sh start_peer1.sh
sh start_peer2.sh
sh start_peer3.sh
````

#### 4. 访问后台

http://localhost:8000/

如果访问成功，能正确显示三个节点的信息，说明启动集群成功






