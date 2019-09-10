---
title: hexo常用命令
date: 2018-12-06 11:24:37
categories: 千百度
tags: hexo
---


#### 前言: 基于hexo + GitHub 构建个人博客，当前环境默认已安装git 和node，两者的安装方法这里不再赘述。

<!--more-->

----

相关步骤
---------
- 新建文件目录，并进入新的文件目录。
``` 
mkdir blog
cd blog/
```
- 安装hexo (-g 为全局安装)，并检查是否安装成功。
  ***由于本地已经安装hexo上述代码不再详细赘述。***
```
wangguanweideMacBook-Pro:blog xiaowei$ npm install hexo -g
wangguanweideMacBook-Pro:blog xiaowei$ hexo -v
hexo: 3.8.0
hexo-cli: 1.1.0
node: 8.11.1
```

- 有hexo 就说明安装成功，初始化该文件夹，初始化完成之后再下载文件依赖。
```
wangguanweideMacBook-Pro:blog xiaowei$ hexo init
wangguanweideMacBook-Pro:blog xiaowei$ npm install 
```

- 生成静态文件，启动服务。
```
wangguanweideMacBook-Pro:blog xiaowei$ hexo g
wangguanweideMacBook-Pro:blog xiaowei$ hexo s
```
- 将git的公钥配置在GitHub中。配置Deployment，在其文件夹中，找到_config.yml文件，修改repo值（在末尾）username 为你的github名。
```
$ vim _config.yml 
```
  > deploy:
  >  type: git
  >  repository: git@github.com:username/username.github.io.git
  >  branch: master

- 新建文章。layout为布局，title为文件名。
```
$ hexo new [layout] <title>
```

- 编辑完成之后，就可以发布部署了。在生成以及部署文章之前，需要安装一个hexo-deployer-git 扩展。
```
$ npm install hexo-deployer-git --save
$ hexo d -g 
```

###### 以上。

----

###### 2019.04.25 补充
1. 针对于每次发布部署新的文章之后，都需要在github page页上面重新配置解析的域名。
  - 解决办法: 在blog/source/ 目录下，新建一个无任何格式的文件，文件名 CNAME。在CNAME文件中写入要解析到域名url

```
vim CNAME
  blog.xxx.com // 在文件中写入域名url
```
###### 以上。
