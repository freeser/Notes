---
title: Hexo博客安装步骤
date: 2017-09-09 10:30:00
tags: [hexo, bash]
categories: [安装说明, 初使化建站]
---
欢迎来到我的博客! 
Hexo博客安装步骤。
在这里将是我工作的随笔、是我的移动存储、是我的大脑数据库。

<!-- more --> 
[文档](https://hexo.io/docs/) &emsp; [问题](https://hexo.io/docs/troubleshooting.html) &emsp; [GitHub](https://github.com/hexojs/hexo/issues).

## 安装 Hexo

```
$ npm install -g hexo-cli
```

## 建站

```
$ hexo init <folder>
$ cd <folder>
$ npm install
```

## 配置

> 发布到github之前您必须先在 `_config.yml` 中修改参数，一个正确的部署配置中至少要有 type 参数如下：

```
url: http://liufeize.com

deploy:
  type: git
  repo: git@github.com:*****/*****.github.io.git
```
> 以上配置依赖` hexo-deployer-git`。`$ npm install hexo-deployer-git --save`

更多信息: [部署](https://hexo.io/zh-cn/docs/deployment.html)

## 快速开始

### 创建一篇文章

``` bash
$ hexo new "My New Post"
```
> 也可以在source下面的_post里面手动公建md文件


更多信息: [Writing](https://hexo.io/docs/writing.html)

### 启动服务

``` bash
$ hexo server
$ hexo server -p 5000
$ hexo server -i 192.168.1.1
```

更多信息: [Server](https://hexo.io/docs/server.html)

### 生成静态文件

``` bash
$ hexo generate
```

更多信息: [Generating](https://hexo.io/docs/generating.html)

### 清除缓存文件

``` bash
$ hexo clean
```
清除缓存文件 (db.json) 和已生成的静态文件 (public)。
在某些情况（尤其是更换主题后），如果发现您对站点的更改无论如何也不生效，您可能需要运行该命令。

``` bash
$ hexo list <type>
```
列出网站资料。

更多信息: [commands](https://hexo.io/zh-cn/docs/commands.html)

### 发布到远程

``` bash
$ hexo deploy
```
以上两个可以简写
```
$ hexo generate --deploy
$ hexo deploy --generate

$ hexo g -d
$ hexo d -g
```

更多信息: [Deployment](https://hexo.io/docs/deployment.html)
