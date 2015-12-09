---
layout: post
title:  "docker笔记 － 开始使用"
date:   2015-12-01 14:59:05 +0800
categories: blog
tags:   [docker]
---
安装Docker环境：ubuntu 14.04

1. 安装

        sudo apt-get install docker.io

2. hello world

        sudo docker run hello-world

3. 获取镜像
    
        sudo docker pull ubuntu:14.04
    
    尝试多次都未能成功从官方拉取镜像                
    解决方法：
    
    1. 使用http代理
    
            sudo vim /etc/default/docker
            export http_proxy="http://xxx.xxx.xxx.xxx:zzzz/"
            sudo service docker restart
          
    2. 使用私有源（[http://dockerpool.com/）](http://dockerpool.com/）){:target="_blank"}
    
            sudo vim /etc/default/docker
            DOCKER_OPTS="--insecure-registry dl.dockerpool.com:5000"
            sudo service docker restart
            sudo docker pull dl.dockerpool.com:5000/ubuntu:14.04
            sudo docker tag dl.dockerpool.com:5000/ubuntu:14.04 ubuntu:14.04

4.  启动容器

        docker run -it ubuntu:14.04

5.  容器保存为镜像

        sudo docker commit -m "Added something from ubuntu14.04" -a "yourDockerHubUserName" 79c761f627f3 yourDockerHubUserName/ubuntu-my:v1

6. 上传镜像[https://hub.docker.com/](https://hub.docker.com/){:target="_blank"}

        docker login
        docker push yourDockerHubUserName/ubuntu-my:v1

7. 启用REST API

        sudo vim /etc/default/docker    
        DOCKER_OPTS="-H tcp://0.0.0.0:4243 -H unix:///var/run/docker.sock"
        sudo service docker restart

8. 使用REST API

        curl ‘http://127.0.0.1:4243/images/json?all=0′| python -m json.tool 
        curl http://127.0.0.1:4243/containers/json | python -m json.tool
        curl -s -XPOST "http://127.0.0.1:4243/containers/d0274fdba510/stop" 
        curl -s -XPOST "http://127.0.0.1:4243/containers/d0274fdba510/start"
        curl -s -XPOST "http://127.0.0.1:4243/containers/d0274fdba510/top"| python -m json.tool 
 
 9. REST API 文档
     [https://docs.docker.com/engine/reference/api/docker_remote_api_v1.22/](https://docs.docker.com/engine/reference/api/docker_remote_api_v1.22/){:target="_blank"}

参考：             
[http://blog.saymagic.cn/2015/06/01/learning-docker.html](http://blog.saymagic.cn/2015/06/01/learning-docker.html){:target="_blank"}        
[http://xiaorui.cc/2014/09/18/关于docker-rest-api接口组件docker-remote-api的使用/](http://xiaorui.cc/2014/09/18/关于docker-rest-api接口组件docker-remote-api的使用/){:target="_blank"}