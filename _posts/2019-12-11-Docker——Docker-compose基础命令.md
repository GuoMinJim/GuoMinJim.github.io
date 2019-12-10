---
layout:     post
title:      Docker / Docker-compose 常用命令
subtitle:   Docker / Docker-compose 常用命令
date:       2019-12-11
author:     jinguomin
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - docker
    - docker-compose
--- 

# Docker
## 获取镜像
```
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
// 未指定标签按latest 默认地址是Docker Hub
```

## 运行镜像
```
docker run -it --rm \
    ubuntu:18.04 \
    bash
    
    -d:后台运行
    -e:环境变量设置
    -i:保持容器运行
    --link=[]    Add link to another container(容器之间的通讯) 
    --name=      Assign a name to the container(指定容器名称)
    -p  端口映射 80：8080把80映射到宿主8080端口
    -it：这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。
    我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端。
    --rm：这个参数是说容器退出后随之将其删除。
    默认情况下，为了排障需求，退出的容器并不会立即删除，
    除非手动 docker rm。我们这里只是随便执行个命令，
    看看结果，不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。
     ubuntu:18.04：这是指用 ubuntu:18.04 镜像为基础来启动容器。
     bash：放在镜像名后的是 命令，这里我们希望有个交互式 Shell，因此用的是 bash。

```
## 列出镜像
```
docker image ls

  -a 显示中间层镜在内的所有镜像
  ubuntu 列出部分镜像
  -f 进行过滤 // docker image ls -f since=mongo:3.2
      如果定义了label  还可以支持label过滤  lable="aa"
   -q 以特定的格式显示  
   docker image ls --format "{{.ID}}: {{.Repository}}" 
   docker image ls --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

## 镜像、容器、数据卷所占用的空间
> docker system df


## 删除本地镜像
```
docker image rm [选项] <镜像1> [<镜像2> ...
    // 配置列表命令进行删除
    docker image rm $(docker image ls -q redis)
// 清除所有的终止状态的容器
$ docker container prune
```

## 进入容器
```
// 推荐使用exec
docker attach  []

docker exec
    -i:没有命令提示符，但是命令执行仍然可以返回
    -it:返回命令提示符
    使用exit并不会导致容器的停止
```

## 导出/导入容器
```
// 导出容器到本地
docker export 7691a814370e > ubuntu.tar 
// 导入容器快照
cat ubuntu.tar | docker import - test/ubuntu:v1.0
docker import http://example.com/exampleimage.tgz example/imagerepo
    用户可以使用docker load来导入镜像存储文件到本地镜像库，也可以使用docker 
    import 来导入一个容器快照到本地镜像库，这两者的却别在于容器快照文件将丢弃
    所有的历史记录和元数据信息（仅保存容器当时的快照状态），而镜像存储文件将保存
    完整记录，体积也要大，此外，从容器快照文件导入时可以重新指定标签等元数据信息。
```



## 登录Docker
```
  docker login 
  docker logout 
```

## 推送镜像
```
// username为你的docker账户名
docker tag ubuntu:18.04 username/ubuntu:18.04
```

# docker compose
## 术语
> 服务 (service)：一个应用容器，实际上可以运行多个相同镜像的实例。
> 项目 (project)：由一组关联的应用容器组成的一个完整业务单元。

## 命令对象与格式
```
对于compose,命令的对象可以是项目也可以是容器或者是服务。如果没有特别的说明就是项目（项目中所有的服务都会受到影响）。
// 基本格式
docker-compose [-f=<arg>...] [options] [COMMAND] [ARGS...]
```

## 命令选项

```
-f,--file FILE 指定使用的docker-compose模板文件(默认为docker-compose.yml),可以多次指定
-p --project-name Name 指定项目名称，默认将使用所在目录名称作为项目名
--x-networking 使用Docker的可拔插网络后端特性
--verbose 输入更多调试信息
-v --version 打印版本并退出
```

## 命令使用说明
> 构建（重新构建）项目中的服务容器
> 服务容器一旦构建，将会带上一个标记名。web项目下db--->web_db
```
docker-compose build [options] [SERVICE...]
    --force-rm 
        删除构建过程中的临时容器
    --no-cache 
        构建镜像过程中不使用cache(加长构建过程)
    --pull 
        始终尝试通过pull来获取更新版本的镜像
docker-compose
    config 
        验证Compose文件格式是否正确，若正确显示配置，若格式错误则显示错误原因。
    down 
        此命令将会停止up命令所启动的容器，并移除网络。
    exec
         进入指定的容器
    help
         获得一个命令的帮助
    images
         列出Compose文件中包含的镜
    kill
         格式为 docker-compose logs [options] [Service....]
         docker-compose默认对于不同的服务可以使用不同的颜色， 通过--no-color来关闭
    pause
         docker-compose pause [SERVICE...]暂停一个服务容器
    port
        docker-compose port [options] SERVICE PRIVATE_PORT
        打印某个容器端口所映射的公共端口
        --protocol=proto 指定端口协议，tcp（默认值）或者 udp
        --index=index 如果同一服务存在多个容器，指定命令对象容器的序号（默认为 1
    ps
        列出项目中所有容器
    pull
        docker-compose pull [options] [SERVICE...]
        拉取服务依赖的镜像
        --ignore-pull-failures 忽略拉取镜像过程中的错误
    push
       推送服务依赖的镜像到 Docker 镜像仓库 
    restart
        docker-compose restart [options] [SERVICE...]
        重启项目中的服务
    rm
        docker-compose rm [options] [SERVICE...]
        删除所有（停止状态的）服务容器。推荐先执行 docker-compose stop 命令来停止容器
        -v 删除容器所挂载的数据卷
        -f, --force 强制直接删除，包括非停止状态的容器。一般尽量不要使用该选项。     
    run
        docker-compose run [options] [-p PORT...] [-e KEY=VAL...] SERVICE [COMMAND] [ARGS...]
        命令类似启动容器后运行指定的命令，相关卷、链接等等都将会按照配置自动创建
            给定命令将会覆盖原有的自动运行命令；
            不会自动创建端口，以避免冲突。
            -d 后台运行容器。
            --name NAME 为容器指定一个名字
            --entrypoint CMD 覆盖默认的容器启动指令
            -e KEY=VAL 设置环境变量值，可多次使用选项来设置多个环境变量。
            -u, --user="" 指定运行容器的用户名或者 uid。
            --no-deps 不自动启动关联的服务容器
            --rm 运行命令后自动删除容器，d 模式下将忽略。
            -p, --publish=[] 映射容器端口到本地主机
            --service-ports 配置服务端口并映射到本地主机。
    scale
        docker-compose scale [options] [SERVICE=NUM...]
        设置指定服务运行的容器个数
        过 service=num 的参数来设置数量。
            docker-compose scale web=3 db=2
        -t, --timeout TIMEOUT 停止容器时候的超时（默认为 10 秒
        
    start
        格式为 docker-compose start [SERVICE...]
    stop
        停止已经处于运行状态的容器，但不删除它
    top
        查看各个服务容器内运行的进程。
            -t, --timeout TIMEOUT 停止容器时候的超时（默认为 10 秒）
    unpause
        格式为 docker-compose unpause [SERVICE...]。恢复处于暂停状态中的服务。
    up
        docker-compose up [options] [SERVICE...]
        尝试自动完成构建镜像，（重新）创建服务，启动服务，并关联服务相关容器的一系列操作。
        链接的服务都将会被自动启动，除非已经处于运行状态。
        大部分时候通过该命令来启动一个项目
        默认情况，docker-compose up启动的容器都在前台，控制台将会同时打印所有容器的输出信息。
        当通过Ctrl + c的时候，所有容器将会停止。
        使用 -d 参数，将会在后台启动并运行所有服务，一般推荐生产环境使用该选项。
        默认情况，如果服务容器已经存在，docker-compose up 将会尝试停止容器，然后重新创建（保持使用 volumes-from 挂载的卷），以保证新启动的服务匹配 docker-compose.yml 文件的最新内容。如果用户不希望容器被停止并重新创建，可以使用 docker-compose up --no-recreate。这样将只会启动处于停止状态的容器，而忽略已经运行的服务。如果用户只想重新部署某个服务，可以使用 docker-compose up --no-deps -d <SERVICE_NAME> 来重新创建服务并后台停止旧服务，启动新服务，并不会影响到其所依赖的服务。
            -d 在后台运行服务容器。
            --no-color 不使用颜色来区分不同的服务的控制台输出
            --no-deps 不启动服务所链接的容器
            --force-recreate 强制重新创建容器，不能与 --no-recreate 同时使用
            --no-recreate 如果容器已经存在了，则不重新创建，不能与 --force-recreate 同时使用。
            --no-build 不自动构建缺失的服务镜像。
            -t, --timeout TIMEOUT 停止容器时候的超时（默认为 10 秒）
    version
        打印版本信息。
        -T 不分配伪 tty，意味着依赖 tty 的指令将无法运行。
```


