---
title: GitHub Pages+Hexo建立博客
date: 2017-10-27 19:32:04
tags: 
- Hexo
- Github Pages
categories:
- 技术
toc: true
---

之前有用过WordPress直接建站，但是WordPress有两个不好的地方：
1、WordPress是动态的，对于服务器的要求比较高，配置比较低的服务器运行时候会加载很慢。
2、国内想用自己的域名建网站必须要备案，比较麻烦。

恰好听到同学说可以用Hexo和Github Pages来做静态的个人博客。相比WordPress，Hexo+Github Pages可以不用购买服务器，省了一笔钱。其次Hexo是静态的，加载速度很快，很适合个人博客的搭建。
<!--more-->
# 安装环境
要想使用Hexo，首先要在你的电脑上面安装Node.js和Git工具
安装完毕之后，在你想放置网站文件的地方建立一个文件夹。比如 D:/Hexo。进入到文件夹之后使用git工具
```
$ npm install hexo-cli -g
$ hexo init blog
$ cd blog
$ npm install
$ hexo g # 或者hexo generate
$ hexo s # 或者hexo server，可以在http://localhost:4000/ 查看
```
这里提一下Hexo常用的几个命令：
```
hexo generate(hexo g)       生成静态文件，会在当前目录下生成一个新的public文件夹
hexo server(hexo s)         启动本地Web服务，用于博客的预览
hexo deploy(hexo d)         部署博客到远端，比如GitHub
hexo new "postName"         新建文章，路径在/source/_posts
hexo new pages "pageName"   新建页面，路径在/source
```
安装完成之后，就要选择你需要使用的主题了。Hexo有很多主题可以选择，我这里使用的是Next主题。在GitHub上面搜索到项目之后会有很详细的使用说明，这里不再赘述。<br>
关于主题的个性化定制，一般主题会有很多地方可以个性化更改，可以根据官方文档自行更改，或者使用搜索引擎来获取个性化定制的细节，每个主题都不相同。<br>
需要注意的是，更新主题的时候如果是修改了css文件，需要先清除之前生成的缓存文件，不然有可能无法正常生效。
```
$ hexo clean
$ hexo generate
```
# GitHub Pages设置
## 什么是GitHub Pages
[GitHub Pages](https://pages.github.com/)本来是用来介绍托管在Github上面的项目，不过由于空间免费稳定，被很多人拿来搭建个人博客。<br>
每个人只能有一个仓库来存放个人主页，而且仓库的名字必须是username/username.github.io，这是特殊的命名约定。可以通过 http://username.github.io 来直接访问你的个人主页。<br>
这里需要特别注意一下，个人网页的网站内容是在master分支下面的。
## 配置和使用GitHub Pages
我们如何让本地的项目与远程的Github建立联系呢？一般来说有两种方法。一种是通过GitHub的账号密码来进行操作，第二种是比较推荐，通过SSH keys来建立连接。
### 检查SSH keys的设置
首先需要检查电脑上现有的SSH key：
`cd ~/.ssh   检查本机的ssh密钥`
如果提示：No such file or directory 说明你第一次使用git，按照下面的步骤创建新的SSH key
```
$ ssh-keygen -t rsa -C "邮件地址"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):<回车就好>
```
然后系统会提示你输入密码：
```
Enter passphrase (empty for no passphrase):<输入加密串>
Enter same passphrase again:<再次输入>
```
### 添加SSH key到Github
1 在本机设置SSH key之后，需要添加到Github上，已完成SSH链接的设置。<br>
首先打开本地id_rsa.pub文件，(参考地址： C:\Users\yourname\.ssh\id_rsa.pub)。文件内容为刚才生成的密钥，复制文件内容。
2 登陆Github，点击右上角Settings->SSH and GPG keys 。新建SSH keys，内容为刚才复制的密钥内容。
3 测试下面的命令，看是否成功，命令不要更改
`$ ssh -T git@GitHub.com`
如果看到如下反馈
```
The authenticity of host 'GitHub.com (207.97.227.239)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)?
```
不要紧张，输入 yes 就好，然后会看到成功的信息。
有的时候会提示permission denied，此时一般是ssh公钥没有添加到本地SSH环境中，只需要使用ssh-add命令添加公钥即可。
```
ssh-add your_publickey
```
## 设置用户信息
现在你已经可以通过 SSH 链接到 GitHub 了，还有一些个人信息需要完善的。
Git 会根据用户的名字和邮箱来记录提交。GitHub 也是用这些信息来做权限的处理，输入下面的代码进行个人信息的设置，把名称和邮箱替换成你自己的，名字必须是你的真名，而不是GitHub的昵称。
```
$ git config --global user.name "username"//用户名
$ git config --global user.email  "username@gmail.com"//填写自己的邮箱
```
# 域名
上面已经完成了基本环境的设置，但是搭建个人博客，最重要的是要有一个自己的域名。这里域名推荐去Godaddy上面购买，选好自己喜欢的域名之后，建立解析即可。注意，域名解析要建立两个A的解析记录，分别为192.30.252.154和192.30.252.153。然后建立一个CNAME类型的解析，主机记录设置为www，记录值设置为你的Github Pages的网址，比如yourname.github.io。<br>
做完上面的步骤，其实还是不能直接通过自己的域名去访问Github Pages的。还需要两个小步骤的设置：
先进入Github，进入Github Pages的仓库，然后找到Settings->Options->GitHub Pages 找到Custom domain，把你设置的个人域名添加进去。<br>
然后在仓库的根目录添加一个CNAME文件，文件内容为你的域名<br>
完成上面的设置，已经可以成功通过自己的域名访问

# 把博客部署到Github
前面我们已经完成了所有的基础设置，并且已经可以在本地访问自己的博客，下面我们需要完成的就是把本地的博客部署到Github上面。Hexo提供了deploy插件让我们使用，方便部署到很多平台。如果部署到Github，需要先安装扩展
`$ npm install hexo-deployer-git --save`
然后在配置文件_config.xml中做下面的修改：
```
deploy:
    type: git
    repo: git@github.com:yourname/yourname.github.io.git //可以在项目仓库clone看到地址
    branch: master
```
然后再执行
`hexo d`
就能完成部署，这时通过你的域名就能直接访问博客了。


# 参考链接
[手把手教你使用Hexo + Github Pages搭建个人独立博客](https://linghucong.js.org/2016/04/15/2016-04-15-hexo-github-pages-blog/)
[MarkDown语法说明(简体中文版)](http://wowubuntu.com/markdown/index.html)
[hexo的next主题个性化配置教程](http://shenzekun.cn/hexo%E7%9A%84next%E4%B8%BB%E9%A2%98%E4%B8%AA%E6%80%A7%E5%8C%96%E9%85%8D%E7%BD%AE%E6%95%99%E7%A8%8B.html)
[Hexo Markdown 简明语法手册](https://hyxxsfwy.github.io/2016/01/15/Hexo-Markdown-%E7%AE%80%E6%98%8E%E8%AF%AD%E6%B3%95%E6%89%8B%E5%86%8C/)

