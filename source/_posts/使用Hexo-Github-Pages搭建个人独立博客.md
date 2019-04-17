---
title: 使用Hexo + Github Pages搭建个人独立博客
tags:
  - hexo
date: 2019-03-10 19:51:30
---


之前在弄这个博客的时候踩了一些坑，也看了一些教程，今天试着总结成一篇文章。整个过程我只是做过四五次，所以有什么问题还请大家指出。

效果
[https://www.niracler.com](https://www.niracler.com)

> 注意： 我这里是在linux系统上搭建的，所以win上搭建会有点不一样，不过大致都差不多。

<!-- more -->

### 什么是 Hexo？
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

### 什么是 Github Pages?
Github Pages 是面向用户、组织和项目开放的公共静态页面搭建托管服 务，站点可以被免费托管在 Github 上，你可以选择使用 Github Pages 默 认提供的域名 github.io 或者自定义域名来发布站点。Github Pages 支持 自动利用 Jekyll 生成站点，也同样支持纯 HTML 文档，将你的 Jekyll 站 点托管在 Github Pages 上是一个不错的选择。

### 安装相关工具

#### 安装Node.js以及Git

首先，你要先确保自己的系统中已经安装了下面这些应用程序，Hexo是基于Node.js的，而Git则是用来上传文件到github上的工具：

- Node.js
- Git

检验是否安装成功：

```bash
node -v
npm -v
git --version
```
![](/images/Screenshot_20190310_165122.png)

#### 换源，使用cnpm源

因为在国内直接用npm实在是太慢了，所以我选择使用淘宝的源，而用cnpm命令。

```bash
$ sudo npm install -g cnpm --registry=https://registry.npm.taobao.org   
```

#### 安装Hexo

```bash
$ sudo cnpm install hexo-cli -g
```

### 初始化Hexo项目

#### 创建并进入blog文件夹

```bash
$ mkdir blog
$ cd blog
```

#### 初始化hexo基础配置文件

> 初始化时间可能会比较长。

```bash
$ hexo init
```
目录结构  
![](/images/Screenshot_20190310_170440.png)

#### 生成静态文件
```bash
$ hexo g
```

#### 加载hexo基础html、css、js等文件。

在这完成后等于已经在本地创建了一个网页，想查看的话，输入

```bash
$ hexo s
```

![](/images/Screenshot_20190310_170719.png)

然后相当于开启了一个本地的服务器，会提示你拷贝url到浏览器。

![](/images/Screenshot_20190310_170838.png)



### 在github上创建并设置远程库  

#### 创建仓库

名字必须是 用户名.github.io,这里是因为我已经有这仓库了

![](/images/Screenshot_20190310_193709.png)

#### 创建分支

我们总共有两个分支，master和hexo。之所以要有这两个分支，是为了在电脑出问题的时候或者换新的电脑以后才能快速的恢复blog环境。hexo分支存储的是该项目，而master分支存储的是hexo生成的静态文件。

![](/images/Screenshot_20190310_193901.png)

#### 设置hexo为默认分支

![](/images/Screenshot_20190310_193917.png)

#### 将项目克隆到本地

```bash
$ git clone https://github.com/(用户名)/(仓库名).git:克隆到本地
```

#### 将文件复制到blog文件夹中

```bash
$ cd ..
$ git clone https://github.com/(用户名)/(仓库名).git
$ cp -r (仓库名)/.git blog/.git
```

### 安装主题

可以到下面这个网站看看有什么喜欢的主题,然后安装。

https://hexo.io/themes/

#### 下载主题

```bash
$ cd blog
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```

#### 配置主题

在项目的_config.yml中配置自己的主题

```yml
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next
```

现在打开http://localhost:4000/ 已经可以看到一篇内置的blog了。当然，这是我经过配置后的主题。

![](/images/Screenshot_20190310_202804.png)

### 发布hexo到github page

#### 配置远程仓库

在项目的_config.yml中配置自己的远程仓库地址

```yml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/Niracler/niracler.github.io.git
  branch: master
```

#### 提交项目，部署

安装部署工具（方便以后更新）
```bash
$ cnpm install hexo-deployer-git --save
```

这里有几个坑需要注意一下：

>1、所有的配置“:”符号后面都要带空格，否则执行本地测试直接失败。  
>2、language是设置语言。zh-CN是中文。  

#### 生成静态文件并上传到github仓库
```bash
$ hexo clean && hexo g && hexo d #等于一次性执行了，清空、刷新、部署三个命令
```

### 总结

对于日常修改博客的时候，应该首先用 ```git add . ``` , ```git commit -m “…”``` , ```git push origin hexo```　将内容保存至github中，然后才用 ```hexo g -d``` 部署博客

#### Hexo常用的几个命令：

```bash
$ hexo generate #(hexo g) 生成静态文件，会在当前目录下生成一个新的叫做public的文件夹  
$ hexo server #(hexo s) 启动本地web服务，用于博客的预览  
$ hexo deploy #(hexo d) 部署播客到远端（比如github, heroku等平台）  
```

#### 另外还有其他几个常用命令：

```bash
$ hexo new "postName" #新建文章
$ hexo new page "pageName" #新建页面
$ hexo new draft "postName" #新建草稿
$ hexo publish "postName" #发布草稿
```

#### 常用简写
```bash
$ hexo n == hexo new
$ hexo g == hexo generate
$ hexo s == hexo server
$ hexo d == hexo deploy
```

#### 常用组合
```bash
$ hexo d -g #生成部署
$ hexo s -g #生成预览
```

### 这仅仅是一个开始

虽然我上面已经说了这么多了，但这只是一个开始，你还要选好主题，还有对主题进行无数的优化，还要用自己的域名什么的。我就不写了，直接在下面给这些链接你们去看吧。

### 参考

-   [使用hexo+github免费搭建个人博客网站超详细教程](https://www.jianshu.com/p/a39573555039)
-   [使用 Hexo 创建项目文档网站](https://github.com/nodejh/nodejh.github.io/issues/37)
-   [blog+github备份和恢复](https://jinzequn.github.io/2018/01/24/github-hexo/)
-   [Hexo博客Next主题打开过慢的解决办法](http://www.mdslq.cn/archives/f186e832.html)
-   [使用hexo+github搭建个人博客遇到过哪些坑](https://www.jianshu.com/p/59e8d170346d)
-   [npm换源](https://www.jianshu.com/p/0deb70e6f395)
-   [Hexo - 编写和发布草稿](http://www.cubemister.com/Blog/2016/10/04/Hexo-%E7%BC%96%E5%86%99%E5%92%8C%E5%8F%91%E5%B8%83%E8%8D%89%E7%A8%BF/)
-   [hexo的next主题个性化教程:打造炫酷网站](http://shenzekun.cn/hexo%E7%9A%84next%E4%B8%BB%E9%A2%98%E4%B8%AA%E6%80%A7%E5%8C%96%E9%85%8D%E7%BD%AE%E6%95%99%E7%A8%8B.html)
