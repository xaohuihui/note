## Docker-Compose 使用小技巧

#### Docker-compose简介
>  Docker-Compose项目是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。
> - Docker-Compose将所管理的容器分为三层，分别是工程（project），服务（service）以及容器（container）。
> - 一个工程当中可包含多个服务，每个服务中定义了容器运行的镜像，参数，依赖。一个服务当中可包括多个容器实例，Docker-Compose并没有解决负载均衡的问题，因此需要借助其它工具实现服务发现及负载均衡。
> - Docker-Compose项目由Python编写，调用Docker服务提供的API来对容器进行管理。因此，只要所操作的平台支持Docker API，就可以在其上利用Compose来进行编排管理。

#### Docker-Compose常用命令
```bash
pip install docker-compose                           # 下载Docker-compose
docker-compose --version                             # 查看版本，确认是否下载成功
docker-compose pull/push -f docker-compose.yml            # 拉取/推送docker-compose中的镜像 –ignore-pull-failures(忽略拉取时的错误) –ignore-push-failures(忽略推送时的错误)
# 后台方式启动并构建镜像Docker集群，去掉-d则在当前终端启动， --no-build 不构建缺失镜像
docker-compose -f docker-compose.yml up --build -d

# 查看当前项目中的所有容器
docker-compose pd -a

# 停止/启动/重启/强制停止/关闭/移除/查看日志
docker-compose -f docker-compose.yml stop/start/restart/kill/down/rm/logs -f
```

***
* From: xaohuihui
* 手搓不易，记得star哦