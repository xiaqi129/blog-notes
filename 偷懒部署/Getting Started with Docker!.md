# Getting Started with Docker

## 一、环境准备

### 1、电脑中安装好git以及docker

```shell
xiaqi@XiangdeMacBook-Pro workplace % docker --version
Docker version 20.10.5, build 55c4c88
```

```sh
xiaqi@XiangdeMacBook-Pro workplace % git --version
git version 2.24.3 (Apple Git-128)
```

### 2、拉取Demo代码

```shell
git clone git@github.com:nigelpoulton/gsd.git
```

## 二、构建镜像

### 1、Build Docker Image

```shell
cd /gsd/first-container
docker image build -t elonqi/gsd:first-ctr .
```

### 2、查看Docker Image

```shell
docker image ls
```

### 3、推送Image到Docker Hub

```shell
docker image push elonqi/gsd:first-ctr
```

### 4、删除本地Image

## 三、管理容器

```shell
docker image rm elonqi/gsd:first-ctr
```

### 1、启动Docker容器

```shell
docker container run -d --name web -p 8000:8080 elonqi/gsd:first-ctr
Unable to find image 'elonqi/gsd:first-ctr' locally
first-ctr: Pulling from elonqi/gsd
540db60ca938: Already exists
4f9832ab4c84: Already exists
b660fdf49706: Already exists
feaab9ebc363: Already exists
738d074d8372: Already exists
d5afa2dec533: Already exists
4f4fb700ef54: Already exists
94eaa135fb91: Already exists
Digest: sha256:802058310937a9d1326e71998383445d05981f5eddf3ea4885221ac5cf290663
Status: Downloaded newer image for elonqi/gsd:first-ctr
054f2a8f237ce2a7743e3a3d9ba6ff9acbb9e1d2a88eb462aaccbb2dccc67131
```

​	

| 指令 |         含义         |
| :--: | :------------------: |
|  -d  |       后台运行       |
| 8000 | docker 启动程序端口  |
| 8080 | 映射到程序启动的端口 |

###  2、查看容器

```shell
docker container ls
CONTAINER ID   IMAGE                  COMMAND         CREATED              STATUS              PORTS                    NAMES
054f2a8f237c   elonqi/gsd:first-ctr   "node app.js"   About a minute ago   Up About a minute   0.0.0.0:8000->8080/tcp   web
```

### 3、停止容器

```shell
docker container stop web
```

### 4、查看停止的容器

```shell
xiaqi@XiangdeMacBook-Pro first-container % docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
xiaqi@XiangdeMacBook-Pro first-container % docker container ls -a
CONTAINER ID   IMAGE                  COMMAND         CREATED         STATUS                        PORTS     NAMES
054f2a8f237c   elonqi/gsd:first-ctr   "node app.js"   6 minutes ago   Exited (137) 57 seconds ago             web
```

### 5、启动容器

```shell
docker container start web
```

### 6、删除容器

```shell
docker container rm web
```

### 7、进入和退出容器Shell

```shell
docker container run -it --name test alpine sh
```

​	*退出容器Shell并保持容器后台运行*：**Ctrl+P+Q**

### 8、彻底删除Shell容器

```shell
docker container rm test -f
```

