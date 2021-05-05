[目录](../目录.md/)

### 前言  
---
+ 这一小节，我们会跑两个实例来巩固学习之前学到的有关docker run以及镜像、容器的相关操作指令  

##### 部署Nginx和Tomcat
+ 在前面提到的docker run流程，用一个流程图概括了从镜像的下载到镜像的执行(容器)全过程  
+ 所以，按照这个思路我们将一步步的去实现这些部署：
1. 搜索和下载镜像，就一定绕不开[docker hub](https://hub.docker.com)这个官方提供的镜像仓库，进入官网通过搜索关键字Nginx和Tomcat，我们可以得到有关这两个镜像的详细介绍和官方提供的用例方法，然后根据需求去下载相应的版本  
---
+ 镜像的代号由镜像名(name):版本(tag_name)组成，为了方便我这里省略掉后面的(tag_name)，即默认下载最新版本：
+ + 1. $ docker pull nginx
+ + ![avatar](/png/6.png)
+ + 2. $ docker pull tomcat:9.0  
+ + ![avatar](/png/7.png)  
+ + 3. $ docker images  
+ + ![avatar](/png/9.png)
---
2. 运行镜像，生成实例  
---  
+ + \# --name 容器名关键字
+ + \# -d    后台运行
+ + \# -p 宿主机端口:容器端口，建立映射关系，nginx默认端口是80
+ + 1. $ docker run --name nginx01 -d -p 8080:80 nginx  
+ + ![avatar](/png/10.png)
+ + \# 从宿主机访问容器 在宿主机浏览器输入 localhost:8080  
+ + ![avatar](/png/11.png)  
+ + \# 停止容器nginx01的服务
+ + 2. $ docker container stop nginx01
+ + ![avatar](/png/12.png)  
+ + \# 再次访问访问容器 在宿主机浏览器输入 localhost:8080
+ + ![avatar](/png/13.png) 
+ + \# 删除容器nginx01
+ + 2. $ docker container rm nginx01
---
3. 关于tomcat的部署过程与上述步骤类似，关于tomcat容器的默认端口可以查阅官方文档[docker hub](https://hub.docker.com)。
---