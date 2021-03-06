---
layout: post
title: "Docker基础"
description: Docker基础
modified: 2021-02-03
category: Docker
tags: [Docker]
---

# 一、Docker基础

镜像相关命令：

    // 拉取镜像
    docker pull hub.c.163.com/library/hello-world:latest
    docker pull hub.c.163.com/library/nginx
    // 查看镜像
    docker images
    // 删除镜像
    docker rmi xxxx(镜像ID)
    // 搜索镜像
    docker search httpd
    // 为镜像添加一个新的标签。
    docker tag xxxx(镜像ID) test/ubuntu:v2
    // 查看指定镜像的创建历史
    docker history hub.c.163.com/library/nginx
    // 将指定镜像保存成tar归档文件
    docker save -o my_ubuntu_v3.tar test/ubuntu:v3
    // 从归档文件中创建镜像
    docker import my_ubuntu_v3.tar test/ubuntu:v4

容器相关命令：

    // 创建但是不启动容器，使用docker镜像nginx:latest创建一个容器,并将容器命名为test
    docker create --name test nginx:latest
    // 运行镜像
    docker run hub.c.163.com/library/hello-world
    // 后台运行Nginx
    docker run -d hub.c.163.com/library/nginx
    // 指定Nginx本地端口为8080，容器中端口80，-P是容器内部端口随机映射到主机的高端口。-p是容器内部端口绑定到指定的主机端口。
    docker run -d -p 8080:80 hub.c.163.com/library/nginx
    // 进入容器，其中xxxx代表run之后看到的容器id
    docker exec -it xxxx bash
    // 查看容器运行情况
    docker ps -a
    // 停止容器
    docker stop xxxx
    // 查看容器内的标准输出
    docker logs xxxx
    // 删除容器
    docker rm xxxx
    // 启动一个已停止的容器
    docker start xxxx
    // 重启容器
    docker restart xxxx
    // 导出容器快照到本地文件
    docker export xxxx > ubuntu.tar
    // 将快照文件ubuntu.tar导入到镜像test/ubuntu:v1:
    cat ubuntu.tar | docker import - test/ubuntu:v1
    // 清理掉所有处于终止状态的容器。
    docker container prune
    //查看容器端口的映射情况。
    docker port xxxx
    // 查看容器内部运行的进程
    docker top xxxx
    // 来查看容器的底层信息
    docker inspect xxxx
    // 拷贝文件进容器
    docker cp /users/zhanghao/localdir xxxx:/users/containerUser/
    // 杀掉一个运行中的容器
    docker kill xxxx
    // 暂停容器中所有的进程
    docker pause xxxx
    // 恢复容器中所有的进程
    docker unpause xxxx
    // 从服务器获取实时事件
    docker events --since="2016-07-01"

容器状态有7种：

    created（已创建）
    restarting（重启中）
    running 或 Up（运行中）
    removing（迁移中）
    paused（暂停）
    exited（停止）
    dead（死亡）

网络相关

    // 创建网络 
    docker network create -d bridge test-net
    // 查看网络列表
    docker network ls
    // 运行一个容器并连接到新建的test-net网络:
    docker run -itd --name test1 --network test-net ubuntu /bin/bash

# 二、制作镜像

方法1：编写Dockerfile

    // 编写Dockerfile
    from hub.c.163.com/library/tomcat:latest
    MAINTAINER zhanghao zhh900601@sina.com
    COPY horizon.war  /usr/local/tomcat/webapps
    // 编译镜像
    docker build . -t zh-horizon
    // 运行镜像，然后就可以在http://localhost:8080/horizon/，访问到应用了。
    docker run -d -p 8080:8080 zh-horizon
    //推送本地镜像到阿里云镜像仓库
    docker login --username=xxx registry.cn-shanghai.aliyuncs.com
    docker tag [ImageId] registry.cn-shanghai.aliyuncs.com/zhhaliyun/horizonproj:[镜像版本号]
    docker push registry.cn-shanghai.aliyuncs.com/zhhaliyun/horizonproj:[镜像版本号]

方法2：从已创建容器更新镜像

    docker commit -m="has update" -a="zhanghao" xxxx(容器ID) test/ubuntu:v2

# 三、Dockerfile

    FROM 定制的镜像都是基于FROM的镜像
    RUN 用于执行后面跟着的命令行命令，两种格式：RUN <命令行命令> 等同于在终端操作的shell命令。RUN ["可执行文件", "参数1", "参数2"]
    COPY 复制指令，从上下文目录中复制文件或者目录到容器里指定路径。
    CMD 类似于RUN 指令，用于运行程序，但二者运行的时间点不同:CMD在docker run时运行。RUN是在docker build。为启动的容器指定默认要运行的程序，程序运行结束，容器也就结束。CMD指令指定的程序可被docker run命令行参数中指定要运行的程序所覆盖。如果Dockerfile中如果存在多个CMD指令，仅最后一个生效。
    ENTRYPOINT 类似于CMD指令，但其不会被docker run的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给ENTRYPOINT指令指定的程序。如果 Dockerfile 中如果存在多个ENTRYPOINT指令。一般是变参用CMD，定参用ENTRYPOINT。
    ENV 设置环境变量，定义了环境变量，那么在后续的指令中，就可以使用这个环境变量。
    ARG 构建参数，与ENV作用一至。不过作用域不一样。ARG设置的环境变量仅对Dockerfile内有效，也就是说只有docker build的过程中有效，构建好的镜像内不存在此环境变量。
    EXPOSE 仅仅只是声明端口。帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射。在运行时使用随机端口映射时，也就是docker run -P 时，会自动随机映射EXPOSE的端口。
    WORKDIR 指定工作目录。用 WORKDIR 指定的工作目录，会在构建镜像的每一层中都存在。（WORKDIR 指定的工作目录，必须是提前创建好的）。docker build构建镜像过程中的，每一个RUN命令都是新建的一层。只有通过WORKDIR创建的目录才会一直存在。
    USER 用于指定执行后续命令的用户和用户组，这边只是切换后续命令执行的用户（用户和用户组必须提前已经存在）。
    ONBUILD 用于延迟构建命令的执行。简单的说，就是Dockerfile里用ONBUILD指定的命令，在本次构建镜像的过程中不会执行（假设镜像为test-build）。当有新的Dockerfile使用了之前构建的镜像FROM test-build，这是执行新镜像的Dockerfile构建时候，会执行test-build的Dockerfile里的ONBUILD指定的命令。

# 四、Docker Swarm

    // 创建swarm集群管理节点（manager）
    docker-machine create -d virtualbox swarm-manager
    // 创建2个worker节点
    docker-machine create -d virtualbox swarm-worker1
    docker-machine create -d virtualbox swarm-worker2
    // 查看节点
    docker-machine ls
    // 登录并初始化swarm集群
    docker-machine ssh swarm-manager
    （swarm-manager中）docker swarm init --advertise-addr 192.168.99.107
    // 分别进入两个机器里，指定添加至上一步中创建的集群
    docker-machine --native-ssh ssh swarm-worker1 "docker swarm join --token SWMTKN-1-3p8yil4ufeun3cctk0m1ip14caei2tykmv79sdp18u3yphz66r-5v2lfqmy1925s0eo5sf6jfvw6 192.168.99.100:2377"
    docker-machine --native-ssh ssh swarm-worker2 "docker swarm join --token SWMTKN-1-3p8yil4ufeun3cctk0m1ip14caei2tykmv79sdp18u3yphz66r-5v2lfqmy1925s0eo5sf6jfvw6 192.168.99.100:2377"
    // 查看当前集群的信息
    （swarm-manager中）docker info
    // 查看节点信息
    （swarm-manager中）docker node ls
    // 拉取镜像
    （swarm-manager中）docker pull hub.c.163.com/library/nginx:latest
    // 部署服务到集群中
    （swarm-manager中）docker service create --replicas 3 -p 8088:80 --name nginx hub.c.163.com/library/nginx:latest
    // 查看服务
    （swarm-manager中）docker service ls
    （swarm-manager中）docker service ps nginx
    （swarm-manager中）docker service inspect --pretty nginx
    // 调整服务节点个数
    （swarm-manager中）docker service scale nginx=2
    // 删除服务
    （swarm-manager中）docker service rm nginx
    // 停止、重新激活节点接收任务
    （swarm-manager中）docker node update --availability drain swarm-worker1
    （swarm-manager中）docker node update --availability active swarm-worker1

# 五、参考

1.[网易云镜像中心](https://c.163yun.com/hub#/m/home/)

2.[阿里云开源镜像站](https://opsx.alibaba.com/)

3.[菜鸟docker教程](https://www.runoob.com/docker/docker-tutorial.html)

4.[阿里云容器镜像服务](https://help.aliyun.com/document_detail/60743.html)

5.[docker命令大全](https://www.runoob.com/docker/docker-command-manual.html)

6.[Swarm集群管理](https://www.runoob.com/docker/docker-swarm.html)

7.[Docker Swarm从入门到放弃](https://www.cnblogs.com/sword-successful/p/12267163.html)

8.[【253期】面试官：熟悉Docker操作吗？说几个常用的Docker命令吧](https://mp.weixin.qq.com/s?__biz=MzIyNDU2ODA4OQ==&mid=2247489173&idx=1&sn=2882d3378130c3f3c95d835778fa4214&chksm=e80da0e3df7a29f5ffb21ab68cd578d320309355ac9d6af3de8878f6ae8a8869d5a4aa75a53f&scene=21#wechat_redirect)