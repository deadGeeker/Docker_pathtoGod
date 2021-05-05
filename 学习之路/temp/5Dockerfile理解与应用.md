[目录](../目录.md/)

### 前言
---
本小节中，我将依次讲述下面几个概念：
- 概念
- Dockerfile 基本语法
- Dockerfile 创建镜像流程
---

### 概念
---
Dockerfile 是一个文本文件，其中包含我们为了构建 Docker 镜像而手动执行的所有命令。
Docker 可以从 Dockerfile 中读取指令来自动构建镜像。我们可以使用 docker build 命令来创建一个自动构建。
---

### Dockerfile 基本语法
---
##### 指定基础镜像
+ FROM ubuntu:14.04
##### 维护者信息
+ MAINTAINER cyrus_chen

##### 镜像操作命令
+ RUN \
    apt-get -yqq update && \
    apt-get install -yqq apache2

##### 容器启动命令
+ CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
  
---  
基础镜像：以哪个镜像为基础进行制作，使用 FROM 指令来指定基础镜像，一个 Dockerfile 必须以 FROM 指令启动。  
维护者信息：可以指定该 Dockerfile 编写人的姓名及邮箱，使用 MAINTAINER 指令。  
镜像操作命令：对基础镜像要进行的改造命令，比如安装新的软件，进行哪些特殊配置等，常见的是 RUN 命令。  
容器启动命令：基于该镜像的容器启动时需要执行哪些命令，常见的是 CMD 命令或 ENTRYPOINT  
---
### Dockerfile 创建镜像流程
---
下面我们创建一个空目录，并在其中编辑 Dockerfile 文件，并基于此构建一个新的镜像，使用如下操作：  

##### 首先创建目录并切换目录
+ mkdir /cyrus/test/demo && cd /cyrus/test/demo

##### 编辑 Dockerfile 文件，默认文件名为 Dockerfile，也可以使用其它值，  使用其它值需要在构建时通过 `-f` 参数指定，这里我们使用默认值。并在其中添加上述示例的内容。
+ vim Dockerfile

##### 使用 build 命令，`-t` 参数指定新的镜像，. 是基于该目录下的 Dockerfile 构建
+ docker image build -t ubutu14.04:1.0 
---