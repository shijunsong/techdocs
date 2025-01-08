# Docker 常见概念

### 1. API接口：2375

### 2. OCI 与 CRI标准

# Docker 命令

### 1. 基础命令

- 启动Docker: *systemctl start docker*
- 关闭Docker: *systemctl stop docker*
- 重启Docker: *systemctl restart docker*
- 设置Docker开机自启: *systemctl enable docker*
- 查看Docker运行状态: *systemctl status docker*
- 查看Docker版本信息: *docker version* 或 *docker info*
- Docker帮助命令: *docker --help* 或 *docker <command> --help*

### 2. 镜像命令

- 搜索镜像: *docker search <镜像名>*
- **拉取镜像**: *docker pull <镜像名>* 或 *docker pull <镜像名>:<标签>*
- 查看本地镜像: *docker images*
- **删除镜像**: *docker rmi <镜像ID或名称>* 或 *docker rmi -f <镜像ID或名称>*
- 构建镜像: *docker build <路径>*
- 保存镜像为文件: *docker save -o <文件名> <镜像名>*
- 从文件加载镜像: *docker load -i <文件名>*
- 镜像打版本: *docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]*

### 3. 容器命令

- 列出运行中的容器: *docker ps*
- 列出所有容器: *docker ps -a*
- 停止容器: *docker stop <容器ID或名称>*
- 强制停止容器: *docker kill <容器ID>*
- 启动已停止的容器: *docker start <容器ID或名称>*
- **查看容器详细信息**: *docker inspect <容器ID>*
- **查看容器日志**: *docker logs <容器ID>*
- 查看容器内进程: *docker top <容器ID>*
- **进入运行中的容器**: *docker exec -it <容器ID> /bin/bash*
- **退出容器**: *exit* 或 *ctrl+p+q*（不关闭容器）
- 删除容器: *docker rm <容器ID或名称>* 或 *docker rm -f <容器ID>*

### 4. 数据卷命令

- 列出所有数据卷: *docker volume ls*
- 创建数据卷: *docker volume create <卷名>*
- 删除数据卷: *docker volume rm <卷名>*
- 查看数据卷详细信息: *docker volume inspect <卷名>*



#  [DockerFile](https://developer.aliyun.com/article/1436056)

###  1. 文件格式 

```
#### 1、第一行必须指定 基础镜像信息
FROM java:8

#### 2、维护者信息
MAINTAINER oldGj_  oldGj_@163.com

#### 3、镜像操作指令
RUN echo "wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.55/bin/apache-tomcat-9.0.55.tar.gz"
run tar -zxvf apache-tomcat-9.0.55.tar.gz -C /usr/local

#### 4、容器启动执行指令
CMD /usr/local/tomcat/bin/catalina.sh
```

### 2. 常用指令

| 指令名称       | 作用                                                         |
| -------------- | ------------------------------------------------------------ |
| **FROM**       | 指定基础镜像                                                 |
| **MAINTAINER** | 指定维护者信息                                               |
| **RUN**        | 在容器内执行命令。它可以用于安装软件包、配置环境等操作       |
| **ADD**        | 将文件从主机复制到容器内                                     |
| **WORKDIR**    | 设置工作目录，即在容器内执行命令时的默认目录                 |
| **EXPOSE**     | 声明容器内部监听的端口                                       |
| **CMD**        | 定义容器启动时要运行的命令。通常用于定义容器的默认命令       |
| **ENTRYPOINT** | 与`CMD`类似，用于定义容器启动时要运行的命令。通常用于定义容器的入口点。<br />**不同**：`CMD`的参数可以被覆盖，ENTRYPOINT`的参数不能被覆盖。 |
| *ENV*          | 设置环境变量                                                 |
| *VOLUME*       | 设置卷，挂在主机目录                                         |
| *USER*         | 指定在容器内执行命令时使用的用户名或UID，避免以root权限运行  |
| ARG            | 定义构建时的参数，可以在构建过程中传递给Dockerfile。它允许你在构建时动态设置一些值。 |
| *LABEL*        | 镜像中添加元数据标签，通常用于提供镜像的描述信息、维护者信息等 |
| *HEALTHCHECK*  | 定义容器的健康检查                                           |

### 3. 设计规范

- **最小化镜像大小**：使用多阶段构建
- **合理使用缓存**
- **清理不必要的文件**
- **安全性**
- **文档化**

