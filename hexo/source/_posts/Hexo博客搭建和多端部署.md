---
title: Hexo博客搭建和多端部署
date: 2022-02-17 16:46:51
tags: hexo
top_img: 
cover: "http://bank-blog.oss-cn-beijing.aliyuncs.com/img/hexo.jpg"
---
# Hexo博客搭建+多端部署
> 最近将博客搭建起来了，在这里记录下爬的坑。也作为第一篇博客  
> 博客使用的是hexo+github，主题是Butterfly。


## Hexo搭建
1. 首先安装node.js  
https://nodejs.org/en/  (Node.js 版本需不低于 10.13，建议使用 Node.js 12.0 及以上版本),一路next就可以,记得勾选上Add to Path，默认是勾选了的。
``` text 
cmd检查node版本：node -v
```
2. 安装Hexo  
``` text
npm install -g hexo-cli 
#检查是否安装成功，出现hexo <command>表明成功
hexo
```
Hexo与Node兼容版本推荐（来自Hexo官网）
{% asset_img 1.png %}
1. 测试本地启动  
安装完成hexo-cli后就可以尝试先在本地启动：
```text
hexo s # 启动本地hexo服务，访问地址localhost:4000
```

## github部署
1. 新建GitHub库
首先在github上新建一个公共库  
需要注意库名最好是：owener.github.io(这类格式)，选择pulic公开
{% asset_img 2.png %}
打开新建的库，复制库地址(如果本地git配置了ssh，这里也可以复制SSH的地址)
{% asset_img 3.png %}
1. 修改hexo配置
在本地拉取的hexo包下找到_config.yml文件，修改发布配置如下：
``` YML
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repo: git@github.com:XXX/XXX.github.io.git # 配置前面复制下来的http地址或者SSH地址
  branch: master # 默认是master
```
3. 发布
发布步骤如下：后续更新博客需要发布也可以按照这个步骤
```text
hexo clean #清理缓存文件
hexo g #生成网站静态文件到默认设置的public文件夹中
hexo d #生成网站静态文件，并部署到指定的仓库中
```
可以通过访问前面复制http地址直接查看是否部属成功了

## 多端部署
想要实现多台终端都能对博客内容进行修改，主要问题是使用hexo d部署到github上的文件都是网站静态文件，那么我们就需要再将本地文件上传到库中，这里我们就直接新建一个hexo的分支，然后将所有的文件上传到新建的分支中，之后提交本地更改就提交到hexo分支中，master分支就用于hexo的部署。
1. 首先在github上新建分支
{% asset_img 4.png %}
2. clone库代码
    ``` text
    git clone github地址
    ```
3. 将本地文件拷贝到本地库中


    找到clone下来的库，在本地库中新建文件夹hexo，将本地文件拷贝到hexo文件夹下。
4. 将本地文件提交到对应分支

    在本地库输入以下git指令
    ```TEXT
    git checkout -b "hexo" #创建本地分支并切换
    git branch --set-upstream-to=oringin/hexo #本地分支关联到远端hexo分支
    git branch -v #查看本地分支状况
    git remote show origin #查看远端分支情况
    ```
    配置好后就可以直接git add的步骤了。可以看到本地的hexo指向了远端的hexo
    {% asset_img 5.png %}
5. 大功告成
   
   最后在其它终端操作的时候就直接git clone后然后操作就可以了。
