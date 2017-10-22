---
title: 使用github page + hexo + travis搭建博客
---


* github page服务是免费的，提供300M的空间，可以托管静态网页。
* hexo是台湾人基于nodejs开发的，对中文支持比较友好。
* Travis可以自动构建和部署。

# 一 配置SSH KEY
## 1.1 为不同网站应用不同的SSH KE
一般公司内部会有私有gitlab，为避免身份冲突，我们可以为不同网站应用不同的SSH KEY。
在 ~/.ssh 目录下创建 config 文件：
输入以下信息，这样github和oschina就可以使用两个身份。
``` bash
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_github
Host git.oschina.net
    HostName git.oschina.net
    User git
    IdentityFile ~/.ssh/id_rsa
```

## 1.2 生成SSH KEY
``` bash
ssh-keygen -t rsa -C "wuhaosky@163.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/wuhao/.ssh/id_rsa): /Users/wuhao/.ssh/id_rsa_github
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/wuhao/.ssh/id_rsa_github.
Your public key has been saved in /Users/wuhao/.ssh/id_rsa_github.pub.
The key fingerprint is:
SHA256:xxxxxxxxxakKfBQglu8wqYvLGq0CXEFZxxxxxxxxxx wuhaosky@163.com
The key's randomart image is:
+---[RSA 2048]----+
|  +@EB..  +.     |
| o =*.+..+ .     |
|  *  . ++ +      |
|o+ o  .= B       |
|+o. o o S =      |
| o++     o .     |
|.oo              |
|o                |
|+                |
+----[SHA256]-----+
```
输入文件名为id_rsa_github，按回车则会自动生成私钥和公钥：id_rsa_github、id_rsa_github.pub。后面跟着输入密码和确认密码。
再把公钥id_rsa_github.pub中的内容添加至github上面。


参考文章：一台电脑配置多个SSH KEY，多个用户身份http://lalala.lol/2017/02/21/a-computer-with-multiple-ssh-keys/


# 二 Github Page
## 2.1 创建Github仓库
创建Github仓库，仓库名必须是“用户名.github.io”。

<img width=355 height=59 src="https://p0.meituan.net/education/2de0dfadd379d8bc89f54f369506d8c016293.png">

## 2.2 使用Github Page服务
Github Page规定使用仓库的master分支。
![](https://p0.meituan.net/education/0d6b05f068f4f8ac0fccd2cd996a000c242229.png)

# 三 HEXO
[Hexo](https://hexo.io/zh-cn/)是快速、简洁、高效的博客框架。
## 3.1 新建博客
### 3.1.1 新建文章：
``` bash
hexo new <title>
```
此时在source文件夹下_posts文件夹中便会多出一个文档"title.md"。
如果要删除，直接在此文件夹下删除对应的文件即可。
### 3.1.2 生成静态页面
``` bash
hexo generate
```
生成的静态内容在public文件夹内。
### 3.1.3 清除生成内容
``` bash
hexo clean
```
执行此操作会删除public文件夹中的内容。
### 3.1.4 部署Hexo
``` bash
hexo deploy
```
该操作会将hexo生成的静态内容部署到配置的仓库中，请看下面介绍。

## 3.2 部署Hexo
### 3.2.1 Hexo配置文件_config.yml
编辑下面内容：
``` bash
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: Hexo
subtitle:
description:
author: wuhaosky
language:
timezone:

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: wuhaosky.github.io #网站的地址
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:
  
# Home page setting
# path: Root path for your blogs index page. (default = '')
# per_page: Posts displayed per page. (0 = disable pagination)
# order_by: Posts order. (Order by date descending by default)
index_generator:
  path: ''
  per_page: 10
  order_by: -date
  
# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page

# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: landscape

# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:wuhaosky/wuhaosky.github.io.git #github仓库地址
  branch: master # github分支

```

### 3.2.2 安装git插件
``` bash
npm install hexo-deployer-git --save
```
### 3.2.3 部署
``` bash
hexo deploy
```
部署成功后，就可以访问页面了。
![](https://p0.meituan.net/education/c83aa8fdd088734c211b9af353b193951195484.png)

## 3.3 Hexo命令简写
hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo g == hexo generate #生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy #部署

hexo server #Hexo会监视文件变动并自动更新，无须重启服务器
hexo server -s #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP
hexo clean #清除缓存，若是网页正常情况下可以忽略这条命令

参考文章：手把手教从零开始在GitHub上使用Hexo搭建博客教程
https://zhuanlan.zhihu.com/p/22405775

# 四 TravisCI
Travis CI 是目前新兴的开源持续集成构建项目，它与jenkins的很明显的特别在于采用yaml格式，同时它是在在线的服务，不像jenkins需要你本地搭建服务器，目前大多数的github项目都已经移入到Travis CI的构建队列中。

每次写完博客git push到github，然后Travis自动构建，构建完成后自动推送到Github Page服务上。生成后的静态资源和博客的源文件存放到一个仓库上，使用了不同的分支来区分它们。master：博客的静态文件，也就是hexo生成后的HTML等文件，因为要使用Github Page服务，所以它规定的网页文件必须是在master分支；source分支：存放博客源码。并将source分支设为默认分支。
![](https://p1.meituan.net/education/5052a70c2c6ac4b356e55daf71e7891e243712.png)

## 4.1 github生成access token
![](https://p1.meituan.net/education/5c2ef0208f464105e9ea0e21a2506d80175073.png)

## 4.2 TravisCI构建GitHub项目配置
![](https://p1.meituan.net/education/bc9d47432427386ce87ce682ad847677520440.png)
## 4.3 在项目根目录增加并配置.travis.yml
``` bash
language: node_js
node_js: stable

# S: Build Lifecycle
install:
  - npm install


#before_script:
 # - npm install -g gulp

script:
  - hexo g

after_script:
  - cd ./public
  - git init
  - git config user.name "wuhaosky"
  - git config user.email "wuhaosky@163.com"
  - git add .
  - git commit -m "Update docs"
  - git push --force --quiet "https://${Github_Token}@${GH_REF}" master:master
# E: Build LifeCycle

branches:
  only:
    - source
env:
 global:
   - GH_REF: github.com/wuhaosky/wuhaosky.github.io.git

```
## 4.4 push代码，travis自动构建并部署
![](https://p0.meituan.net/education/1f876e2738207d1b6ec3b512daf0f6ca735779.png)


参考文章：手把手教你使用Travis CI自动部署你的Hexo博客到Github上
http://blog.csdn.net/woblog/article/details/51319364