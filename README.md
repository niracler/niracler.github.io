# Niracler的博客
> 基于hexo的next主题


![](source/images/header.png)


## 安装npm并换中国源

```
sudo pacman -S npm

npm install cnpm --save
```

## 安装hexo

```bash

cnpm install hexo-cli -g

cnpm install hexo --save

cnpm install hexo-server

cnpm install hexo-deployer-git --save

cnpm install hexo-wordcount --save #字数统计
```

## 用例

```bash
hexo new [name] #新建文章
```

## 部署

```bash
hexo g #生成前端文件
hexo d #上传到github
```

## 参考文献
-   [使用hexo+github免费搭建个人博客网站超详细教程](https://www.jianshu.com/p/a39573555039)

-   [使用 Hexo 创建项目文档网站](https://github.com/nodejh/nodejh.github.io/issues/37)

-   [blog+github备份和恢复](https://jinzequn.github.io/2018/01/24/github-hexo/)

-   [Hexo博客Next主题打开过慢的解决办法](http://www.mdslq.cn/archives/f186e832.html)
