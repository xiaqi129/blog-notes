# Github自动化部署前端项目笔记

## 1 准备工作

### 1.1项目初始化

项目已经在服务器从Github上clone下来了，并且通过npm install安装了项目相关的依赖包

### 1.2打包并配置nginx

使用nginx代理服务器运行，配置的相关路径，为打包后的dist目录

## 2 创建workflow配置文件

### 2.1创建自己的.yml后缀的配置文件 

![image-20201010104509564](\iamge\action-position)

### 2.2指定工作流名称

```shell
# This is a basic workflow to help you get started with Actions

name: Automatic package deployment
```

### 2.3指定触发事件

当有人讲代码推送的master分支时触发

```shell
# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the master branch
on:
  push:
    branches: [ master ]

```

### 2.4配置以build命名的job

事件可以触发的多个 job，这里我们添加一个 build job, 指定运行环境。

```shell
# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
```

### 2.5配置build job下的步骤

#### 2.5.1获取源码

使用 [Actions 市场](https://github.com/marketplace?type=actions) 里的 [actions/checkout](https://github.com/actions/checkout) 获取仓库代码，

```shell
# Steps represent a sequence of tasks that will be executed as part of the job
    steps:
# Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - name: Checkout
        uses: actions/checkout@v2
```

#### 2.5.2安装模块和构建

使用 npm 安装模块和运行 build script。

```shell
# Set up node
    - name: Install Node.js
      uses: actions/setup-node@v1
      with:
    	node-version: '10.x'

# Run npm install command
	- name: Install Dependencies
	  run:  npm install

# Run npm run build command
	- name: Build
	  run:  npm run build
```

#### 2.5.3部署

我们要使用 `ssh-deploy`的anction插件来同步构建好的文件目录到到发布目标服务器。

```shell
# Deploy
      - name: Deploy
        uses: easingthemes/ssh-deploy@v2.0.7
        env:
          SSH_PRIVATE_KEY: ${{ secrets.ACCESS_TOKEN }}
          ARGS: "-avz --delete"
          SOURCE: "dist/"
          REMOTE_HOST: ${{ secrets.REMOTE_HOST }}
          REMOTE_USER: ${{ secrets.REMOTE_USER }}
          TARGET: ${{ secrets.REMOTE_TARGET }}
```

![image-20201010182717133](\iamge\secrets)

（***补充注意⚠️\***：**ACCESS_TOKEN** **是服务器ssh密钥对的私钥部分**；**应将服务器公钥部分添加到接收部署的服务器上的authorized_keys文件中**。正常情况下，**ACCESS_TOKEN 是 服务器 ～/.ssh目录下的id_rsa 文件中 的内容**。仔细看一下[https://github.com/marketplac...](https://github.com/marketplace/actions/ssh-deploy#configuration) 文档中的字段配置描述😓）

如果在远端服务器中生成过了密钥，那么也需要copy一份`id_rsa.pub`为`authorized_keys`文件，为的是方便github的虚拟服务器可以登录上来。

