[目录](../目录.md/)

### 前言  
---
本小节从三部分来讲解Docker网络
- 宿主机ping容器  
- 使用默认网关的容器与容器之间互ping  
- 不同网关之间的容器之间互ping  
---

### 预科知识
---
1. 键入命令 $ ip addr 
+ + ![avatar](/png/20.png)
+ + 这条命令是查看宿主机的网关，从上图的信息可以知道，本地有三个网关：
+ + lo：这个是本地回环地址【127.0.0.1】对应的网关
+ + ens33：这个是宿主机内网地址【192.168.73.133】对应的网关
+ + docker0：这个是docker在安装以后自动生成的网关，网关地址为【172.17.0.1】

2. 键入命令 $ docker network ls
+ + ![avatar](/png/21.png)
+ + 这条命令是查看docker内部的网络，docker在安装时自动生成三个网关：bridge/host/none，
+ + 同时三个网关也是三种网络模式，在后面自定义网关时会涉及到
+ + 现在键入命令 $ docker network inspect bridge
+ + ![avatar](/png/22.png)  
+ + 可以看到bridge的网关与docker0一致，也就是docker0的网络模式为bridge
---
### 第一个问题：宿主机可以通过容器的网关地址ping到容器吗？
---
1. 为了验证这个问题，首先先以默认网络模式(bridge)的方式创建一个容器
+ + 1. $ docker run --name tomcat01 -d -p 80:8080 tomcat 
+ + 查看容器的网关地址
+ + 2. $ docker exec -it tomcat01 cat /etc/hosts  
+ + ![avatar](/png/23.png)
+ + 通过以上的步骤我们生成了一个网关地址为172.17.0.2的容器，看起来是不是跟docker0很相似呢？
+ + 3 $ ping 172.17.0.2
+ + ![avatar](/png/24.png)

---
### 第二个问题：使用默认网关的容器与容器之间可以互ping吗？
---
1. 为了验证这个问题，首先先以默认网络模式(bridge)的方式创建一个容器
+ + 1. $ docker run --name tomcat02 -d -p 81:8080 tomcat 
+ + 查看容器的网关地址
+ + 2. $ docker exec -it tomcat02 cat /etc/hosts  
+ + ![avatar](/png/25.png)
+ + 通过以上的步骤我们生成了一个网关地址为172.17.0.3的容器，看起来是不是跟docker0很相似呢？
+ + 3 $ docker exec -it tomcat02 ping 172.17.0.2
+ + ![avatar](/png/26.png)
+ + 可以看到这两个容器的网关地址处于同一网段下，当然可以互相访问到
+ + 既然ip地址可以访问，那么通过容器的名字可以访问吗？
+ + 4. $ docker exec -it tomcat02 ping tomcat01
+ + ![avatar](/png/27.png)
+ + 可以看到是无法实现的，可以实现吗？请把这个疑问带到下一个问题
+ + 其实在平时的使用中，域名肯定比IP地址好记，不然DNS又怎么会被造出来？？？

---
### 第三个问题：不同网关之间的容器之间可以互ping？
---
1. 为了验证这个问题，首先自定义一个docker网络：
+ + 1. $ docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 first-net
+ + 查看自定义网络的的详细信息
+ + 2. $ docker network inspect first-net  
+ + ![avatar](/png/28.png)
+ + 指定自定义网络first-net运行一个容器tomcat-net-01
+ + 3. $ docker run -d -P --name tomcat-net-01 --net first-net tomcat
+ + 再运行一个容器tomcat-net-02
+ + 4. $ docker run -d -P --name tomcat-net-02 --net first-net tomcat
+ + tomcat-net-02通过容器名去访问tomcat-net-01
+ + 5. $ docker exec -it tomcat-net-02 ping tomcat-net-01
+ + ![avatar](/png/29.png)
+ + 可以看到自定义网络模式下，容器与容器之间能够通过容器名来互相访问
+ + 接下来我们尝试一下容器tomcat01通过IP地址访问tomcat-net-01
+ + 6. $ docker exec -it tomcat01 ping 172.18.0.2
+ + ![avatar](/png/30.png)
+ + 可以看到，访问不了，完全丢包，为什么？根本原因在于容器tomcat01与tomcat-net-01处在不同的网段下
+ + tomcat01的网段是172.17.0.0，tomcat-net-01的网段是172.18.0.0
+ + 两个不在一个频段上的人当然不能沟通，就像崇尚掠夺与战争的西方纸老虎无法战胜东方仁者之师！！！
+ + 有什么办法可以解决这个问题？？当然有，docker network提供了一个方法能让容器加入某个网络
+ + tomcat-net-01处在first-net网络下，那么把tomcat01加入到first-net网络是否就可以解决问题了呢？
+ + 把tomcat-net-01加入到first-net网络
+ + 7. $ docker network connect first-net tomcat01
+ + ![avatar](/png/31.png)
+ + 尝试一下容器tomcat01通过容器名字访问tomcat-net-01
+ + 8. $ docker exec -it tomcat01 ping tomcat-net-01
+ + ![avatar](/png/32.png)
+ + 答案就在图片里，详细阅读，各位！！！
---

