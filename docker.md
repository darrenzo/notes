# docker

- [官网教程](https://docs.docker.com/get-started/)
- Docker是开发人员和系统管理员使用容器开发、部署和运行应用程序的平台。使用Linux容器部署应用程序称为容器化。容器可轻松部署应用程序。
- 特点
  - 灵活:即使是最复杂的应用程序也可以被容器化
  - 轻量级:容器利用并共享主机内核
  - 可互换:可以动态部署更新和升级
  - 可移植:可以在本地构建后部署到云，可以在任何地方运行
  - 可伸缩:可以增加并自动分发容器副本
  - 可堆叠:可以垂直和动态堆叠服务
- 应用场景
  - web应用的自动化打包和发布
  - 自动化测试和持续集成、发布
  - 在服务型环境中部署和调整数据库或其他的后台应用
  - 从头编译或者扩展现有的OpenShift或Cloud Foundry平台来搭建自己的PaaS环境
- Docker系统有两个程序：docker服务端和docker客户端
  - docker服务端是一个服务进程，管理着所有的容器
  - docker客户端则扮演着docker服务端的远程控制器，可以用来控制docker的服务端进程
  - docker服务端和客户端一般运行在一台机器上

## 映像与容器

- 通过运行映像启动容器。映像是一个可执行包，它包含运行应用程序所需的所有内容——代码、运行时、库、环境变量和配置文件
- 容器是映像的运行时实例——当执行映像时(即具有状态的映像或用户进程)，映像在内存中的位置。docker ps命令查看正在运行的容器列表

## 容器和虚拟机

- 容器在Linux上本机运行，并与其他容器共享主机的内核
- 容器运行一个离散的进程，这个进程占用比其他任何可执行文件更少的内存，这让容器更加轻量
- 虚拟机(VM)运行一个成熟的“客户”操作系统，通过管理程序对主机资源进行虚拟访问。一般来说，VMs提供的资源比大多数应用程序所需的要多

## 查看docker版本

- docker --version 查看以确保docker安装正常
- docker info或docker version 查看详细的安装信息
- [为了避免权限错误(以及sudo的使用)，将用户添加到docker组](https://docs.docker.com/install/linux/linux-postinstall/)

## 测试docker的安装

- 通过运行简单的docker镜像[hello-world](https://hub.docker.com/_/hello-world/) 来进行测试

```docker
docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
ca4f61b1923c: Pull complete
Digest: sha256:ca0eeb6fb05351dfc8759c20733c91def84cb8007aa89a5bf606bc8b315b9fc7
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

- docker image ls 命令可以把下载到本机的镜像信息罗列出来
- 罗列出本机容器的详细信息

```docker
## 容器正在运行就可以不加--all
docker container ls --all

CONTAINER ID     IMAGE           COMMAND      CREATED            STATUS
54f4984ed6a8     hello-world     "/hello"     20 seconds ago     Exited (0) 19 seconds ago
```

- 测试需要的命令

```docker
## List Docker CLI commands
docker
docker container --help

## Display Docker version and info
docker --version
docker version
docker info

## Execute Docker image
docker run hello-world

## List Docker images
docker image ls

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq
```

## Dockerfile

- 在过去，如果要开始编写Python应用程序，第一件事就是在本机上安装Python运行时。但是本机上的环境需要非常完美，以便应用程序能够按照预期运行，而且还需要匹配生产环境。
- 使用Docker，只需获取一个可移植的Python运行时作为映像，而不需要安装。然后，构建可以在应用程序代码旁边包含基本的Python映像，确保应用程序、它的依赖项和运行时一起运行。这些可移植的映像由Dockerfile定义
- Dockerfile定义了容器内部的环境。对网络接口和磁盘驱动器等资源的访问在这个环境中是虚拟化的，这个环境与系统的其他部分是隔离的，因此需要将端口映射到外部世界，并指定想要“复制”到该环境的文件。此后，在此Dockerfile中定义的应用程序的构建可以在任何地方正确运行
- 步骤：
  - 创建一个空目录。cd进这个空目录，创建一个名为Dockerfile的文件，复制并粘贴以下内容到该文件中，并保存它
  - 创建requirements.txt和app.py文件，和Dockerfile文件放在一起

```docker
# Use an official Python runtime as a parent image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

<!-- TODO 待续 -->