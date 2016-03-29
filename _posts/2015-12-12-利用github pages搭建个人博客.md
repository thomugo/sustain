---
layout: post
title: 利用Ｇithub Pages搭建个人博客
tags:
- News
- Tags
- Blog
- Post
---
* TOC
{:toc}

# 利用Ｇithub Pages搭建个人博客    {#mytitle}

## 简介

### 1. Jekyll简介

　　1. 简单: 无需数据库、无需评论功能，不需要不断更新版本，只需要关心博客内容。   
　　2. 静态: 只用 Markdown (或 Textile)、Liquid、HTML & CSS 就可以构建可部署的静态网站。   
　　3. 博客形态: 自定义地址、分类、页面、博客内容 以及 自定义的布局设计 都是系统中的一等公民。   
　　4. [Jekyll详细介绍](http://jekyll.bootcss.com/)

### 2. Github Pages简介
　　1. Github Pages是Github提供给每个用户的，用来介绍和展示自己的项目。我们可以使用Jekyll生成静态网站，然后部署到Github pages上，利用Github的服务器，运行自己的网站。   
　　2. [Github Pages详细情况](https://pages.github.com/)
    
## 构建github个人博客库

###  安装环境
	
1. 安装ruby环境   
　　之所以要安装ruby，是因为jekyll是用ruby开发的。ubuntu14.04 LTS上默认是没有安装ruby环境的，需要自己安装。安装命令如下：

```
sudo apt-get install ruby1.9.1-dev
```
　　安装完成后，在终端中输入ruby -v，出现如下结果，则说明安装成功：

```
ruby 1.9.3p484 (2013-11-22 revision 43786) [x86_64-linux]
```
　　2. 安装nodejs环境   
　　之所以要安装nodejs是因为ruby的gem管理需要用到nodejs环境，在完成ruby环境后，在终端中输入gem -v，会出现以下结果：

```
var/lib/gems/1.9.1/gems/execjs-2.5.2/lib/execjs/runtimes.rb:48:in `autodetect': Could not find a JavaScript runtime. See https://github.com/rails/execjs for a list of available runtimes. (ExecJS::RuntimeUnavailable) 
```
　　错误信息提示：找不到一个JavaScript环境，只要在终端中输入如下命令即可：

```
sudo apt-get install nodejs
```
　　3. 安装Jekyll环境   
　　在终端中输入如下命令安装Jekyll，这个过程比较慢，和源有关：

```
gem install jekyll
```
　　安装完成后，在终端中输入如下命令，验证jekyll安装是否成功：

```
jekyll new myblog 
```
　　如果成功创建目录，则说明jekyll安装成功，可以进行之后的工作了。   
　　[关于jekyll](http://jekyll.bootcss.com/)　   
　　4. 安装git   
　　[git安装详细步骤](http://www.cnblogs.com/zhcncn/p/4030078.html)   

### 配置   　　
　　1. [在github上创建一个新的仓库](https://pages.github.com/)   
　　在github上创建一个新的仓库，创库名称为：username.github.io,这个仓库用来保存博客的内容。   
　　２. 修改相应文件建立自己的blog   
　　库中所包含的主要文件及文件夹有：   

> 文件夹_layouts：用于存放模板的文件夹，   
文件夹_posts：用于存放博客文章的文件夹   
文件夹css：存放博客所用css的文件夹,比如主题文件以及高亮文件都是放在此处的   
.gitignore：可以删掉，后面会将项目添加到git项目，所以这个不需要了   
_coinfig.yml：jekyll的配置文件，里面可以定义相当多的配置参数，具体配置参数可以参照其官网   
index.html：项目的首页   
_includes:用于存放一些固定的HTML代码段，文件为.html格式，可以在模板中通过liquid标签引入，常用来在各个模板中复用如 导航条、标签栏、侧边栏之类的在每个页面上都一样不变的内容，需要注意的是，这个代码段也可以是未被编译的，也就是说也可以使用liquid标签放在这些代码段中

　　通过修改配置文件_coinfig.yml以及post和layouts就可以实现主要blog的建立和修改。   
　　３. 接着，再将本地内容推送到github上你刚创建的库。   

```
$ git add .
$ git commit -m "first post" 
$ git remote add origin https://github.com/username/jekyll_demo.git
$ git push origin gh-pages
```
　　上传成功之后，访问http://username.github.com/jekyll_demo/就可以看到Blog已经生成了（将username换成你的用户名）。


　　[详细过程参考](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)