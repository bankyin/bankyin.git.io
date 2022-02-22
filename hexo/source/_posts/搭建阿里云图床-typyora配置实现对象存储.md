---
title: 解决Hexo无法显示图片的几种方案
date: 2022-02-22 16:18:33
tags: hexo
cover: "http://bank-blog.oss-cn-beijing.aliyuncs.com/img/aliyun.jpg"
---
# 解决Hexo无法显示图片的几种方案

在搭建hexo博客的时候会遇见图片上传是否麻烦，使用markdown编写博客时我们习惯将图片保存到本地，使用‘![]{}’的格式+相对路径去插入图片，但是在使用`hexo d`的时候相对路径会被转换导致最后发布上去的博客图片无法查看。下面提供几种解决方案：

## 使用hexo图片格式

查看hexo官网，可以看到官网推荐将`post_asset_folder`属性置为true，在新建博客`hexo n “xxx”`的时候会在根目录下创建一个同名的文件夹，将需要插入的图片放到这个目录。

![](http://bank-blog.oss-cn-beijing.aliyuncs.com/img/解决图片无法显示1.png)

在markdown文件中使用如下格式进行图片的访问：

```tex
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}

{% asset_img example.jpg This is an example image %}
```

如此就能够在hexo中访问到图片，但是这种方式，我们在VScode或者Typora种编写文档的时候是无法查看到图片的，无法渲染。

为了解决无法渲染和不习惯的图片插入格式，可以使用hexo-asset-image。

在根目录安装hexo-asset-image，就能按照markdown的格式进行图片的插入。

```shell
npm install https://github.com/CodeFalling/hexo-asset-image --save
```

不过好像hexo3之后由于图片路径中有时间格式导致不可访问，后面再研究这个方式吧，可以暂时使用hexo推荐的格式。

## 搭建阿里云图床+typora配置实现对象存储

第二种方式使用图床，将图片上传到对象存储，好处是方便！！！

1. 首先申请一个对象存储OSS，登录阿里云->进入工作台->对象存储OSS

​	![](http://bank-blog.oss-cn-beijing.aliyuncs.com/img/解决图片无法显示2.png)

2. 点击创建Bucket：

​	![image-20220222172433826](http://bank-blog.oss-cn-beijing.aliyuncs.com/img/image-20220222172433826.png)

3. 修改以下部分：

​	![image-20220222184456883](http://bank-blog.oss-cn-beijing.aliyuncs.com/img/image-20220222184456883.png)

4. 开通完成后，先购买资源包（对象存储的下载和访问会消耗流量费用，建议购买一个资源包，半年也就5块钱不到）

   ![image-20220222184734852](http://bank-blog.oss-cn-beijing.aliyuncs.com/img/image-20220222184734852.png)

   以上对象存储部分就申请好了，下面就是整合到PicGo

5. 下载typroa

   这里提供一个typroa的下载地址，当然还是建议付费，支持开发者。

   链接：https://pan.baidu.com/s/1PpMt21LcWAH4MJrNvaWxWQ 

   提取码：8jkp

6. 安装PicGo-Core插件

   下载好后依次点击：文件->偏好设置->图像，按照如下配置后点击下载或更新。

   ![image-20220222185817030](http://bank-blog.oss-cn-beijing.aliyuncs.com/img/image-20220222185817030.png)

 7. 设置配置文件

    点击打开配置文件：按照如下配置

    ```json
    {
        "picBed": {
          "uploader": "aliyun",
          "aliyun": {
          "accessKeyId": "", //ram的accessKey
          "accessKeySecret": "", //ram的accessKey
          "bucket": "", // 桶名
          "area": "", // 地域节点
          "path": "img/", // 存储路径
           "customUrl": "", // Bucket域名，注意要加 http://或者 https://
           "options": "" // 针对图片的一些后缀处理参数 PicGo 2.2.0+ PicGo-Core 1.4.0+
          }
        },
        "picgoPlugins": {}
      }
    ```

    接下来就需要找到上面的配置：

    首先是accessKey：访问accessKey管理

    ![image-20220222190413130](http://bank-blog.oss-cn-beijing.aliyuncs.com/img/image-20220222190413130.png)

    然后会出现创建新的AccessKey：可以直接点击继续使用就是用当前用户进行创建AccessKey，不过还是建议使用子用户，规划一下权限。创建好后记得立马将显示的accessKeyId和accessKeySecret记录下来，后续没有地方可以查看，若是遗忘只能重新创建。

    ![image-20220222190526067](http://bank-blog.oss-cn-beijing.aliyuncs.com/img/image-20220222190526067.png)

    其余字段在对象存储->bucket列表->概览，分别是bucket、area、customUrl的值，其中area只需要三级域名也就是.aliyuncs.com之前的部分，customUrl则需要添加http://

    ![image-20220222192511266](http://bank-blog.oss-cn-beijing.aliyuncs.com/img/image-20220222192511266.png)

    剩下字段可以不配置。

 8. 验证

    配置好后，就可以进行测试是否配置成功，点击验证图片上传选项，typora会将icon上传到对象存储中，可以自行去阿里云控制台查看是否有文件。

​		![image-20220222185817030](http://bank-blog.oss-cn-beijing.aliyuncs.com/img/image-20220222185817030.png)

9. 大功告成

   完成后，就可以直接在typora中复制图片或者使用快捷键ctrl+shift+i进行图片插入。typora会自动将图片上传到对象存储并将路径改为对象存储中的路径。
