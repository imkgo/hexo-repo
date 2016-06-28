title: Hexo搭建Github静态博客
date: 2015-11-18 16:07:18
tags: [hexo]
---
# 1. 依赖环境
## 1.1 安装Git
## 1.2 安装node.js

# 2. 配置Github
## 1.1 建立Repository
建立与你用户名对应的仓库，仓库名必须为:[your_user_name.github.io]
## 1.2 配置SSH-Key
......

# 3. 安装Hexo
## 3.1 Installation

打开命令行工具，执行命令 

``` bash
$ npm install -g hexo 
```

## 3.2 Start
### 1. Setup your blog

``` bash
$ hexo init "your folder"
```

Hexo随后会自动在目标文件夹建立网站所需要的文件。
进入新建的hexo目录，运行
``` bash
$ npm install
```
会在新建目录中安装 node_modules。

### 2. Start the server

运行下面命令
``` bash
$ hexo server
[info] Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
```
表明Hexo Server已经启动了，在浏览器中打开 http://localhost:4000/, 这时可以看到Hexo已经为你生成了一篇blog。你可以按Ctrl+C 停止Server。
<!-- more -->
### 3. 新建博客
新打开一个命令窗口，执行下面的命令
``` bash
$ hexo new "blog name"
```
刷新http://localhost:4000/, 可以发现已经生成了一篇新文章。

### 4. 生成静态网页
``` bash
$ hexo generate
```
该命令执行完后，会在 hexo/public 目录下生成一系列html，css等文件。
### 5. 部署到Github
部署到Github前需要配置_config.yml文件， 首先找到下面的内容

```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type:
```

然后将它们修改为：
```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: github仓库地址
  branch: master
```
**注意：3.0版本type需要设置为git而不是github。**

需要在环境变量中配置git的bin目录和libexec\git-core目录。
需要在git的.ssh目录中生成公钥和私钥。或将用户文件夹下的.ssh中的公钥和私钥拷贝到git安装目录的.ssh目录中。另外需要安装hexo-deployer-git，执行以下命令
``` bash
$ npm install hexo-deployer-git --save
```
## 3.3 命令总结
### 3.3.1 常用命令
``` bash
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
```
### 3.3.2 复合命令
``` bash
hexo deploy -g  #生成加部署
hexo server -g  #生成加预览
```
命令简写为
``` bash
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```
# Hexo配置
待续...