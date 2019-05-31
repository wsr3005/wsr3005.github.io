---
title: 如何利用hexo与GitHub Pages创建自己的博客网站
date: 2019-05-29 14:40:38
tags: hexo
comments: true
---
# 什么是hexo，GitHub Pages又是什么？
[Hexo](https://hexo.io/zh-cn/) 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。  
[GitHub Pages](https://pages.github.com/) 是GitHub为开发者提供的一种静态站点托管服务，旨在直接从GitHub存储库托管您的个人，组织或项目页面。  
GitHub Pages可以与各种博客框架配合搭建个人博客网站，本文主要介绍hexo这种框架与GitHub Pages的配合使用。
# 准备工作
* 安装Node.js
* 安装Git
* GitHub账号
* 可联网的环境
* 可用域名(非必选)  

# 本地部署hexo
## 安装Node.js与Git
我这里安装使用的是PPA源安装，若有需要也可以使用nvm等其他方式安装，网上教程很多，就不赘述了。
					   
                       
    sudo apt-get update  
    sudo apt-get install npm  
    sudo apt-get install nodejs  
    sudo apt-get install git  

## 安装hexo与next主题
	$ npm install -g hexo-cli
    //创建文件夹
    $ mkdir [filename]
    //进入新建的hexo文件夹
    $ cd [filename]
    //初始化
    $ hexo init    
    //安装git部署插件
    $ npm install hexo-deployer-git --save
    //配置git身份信息
    $ git config --global user.name "[yourname]"
	$ git config --global user.email "[youremail]"
    $ git clone https://github.com/iissnan/hexo-theme-next.git themes/next
安装好后打开hexo文件夹的\_config.yml文件，找到theme:landscape，改为next，其他注释按需修改。  
在本地查看部署效果：

	$ hexo g
    $ hexo s
访问http://localhost:4000/
# 关联GitHub Pages
## 创建个人的GitHub Pages
如果没有GitHub账号，点击[GitHub](https://github.com/)使用邮箱注册，然后新建仓库，格式应该为username .github.io，username是你的GitHub用户名，这里的格式不能错。[详见](https://pages.github.com/)
## 推送网站
之前只是本地部署，接下来我们需要将本地项目推送到刚刚创建的GitHub仓库中。  
首先编辑站点文件夹下的\_config.yml文件，找到deploy这一项，修改为如下格式：

    deploy:
      type: git
      repository: git@github.com:wsr3005/wsr3005.github.io.git
      branch: master
只需输入

	hexo d
即可完成推送，此时在地址栏输入\*\*\*.github.io，发现博客上线。
## 绑定域名(可选)
- 本地hexo的source文件夹下创建CNAME，其中输入你的域名
- 域名添加解析
- GitHub仓库设置中将Custom domain设置为你的域名
- 本地推送修改到仓库  


未完待续