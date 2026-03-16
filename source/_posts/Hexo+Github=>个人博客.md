---
title: Hexo+Github==>个人博客
date: 2024-09-09 13:14:52
categories: 博客
tags:
  - Blog
index_img: /img/start.png
banner_img: /img/start.png
---

###  hexo 和 github 搭建个人博客的思路梳理

#### 1、下载 node.js	

https://nodejs.org/zh-cn

#### 2、下载 git	

https://git-scm.com/downloads


验证是否下载成功，管理员运行 cmd 输入以下三条命令

```bash
node -v
npm -v
git -v
```

#### 3、下载 hexo

```bash
npm install hexo-cli -g
```

#### 4、搭建仓库

名称格式：`仓库名称.github.io`

勾选 Public

勾选 Add a README file

拉到下面点击 create 创建

#### 5、生成SSH Keys

进入任意文件夹，右键空白处然后点Git bash here,输入

```bash
ssh-keygen -t rsa -C "邮件地址"
```

然后敲4次Enter
然后进入C:\Users\用户名，在里面进入.ssh文件
用记事本打开里面的id_rsa.pub,全选复制里面的代码
然后打开github
进入用户设置，找到SSH keys
新建SSH keys，名称随意，在下面粘贴代码，
然后创建
测试是否成功
在git bash中输入

```bash
ssh -T git@github.com
```

回车，然后再输入 yes

#### 6、本地部署

在喜欢位置新建博客文件夹，进入此文件夹，右键打开终端，输入

```sh
hexo init
```

然后依次输入

```bash
hexo g	# 生成
hexo s	# 预览
```

因为国内与github连接不稳定，如果不成功的话就等等再试，可以科学上网最好
现在就可以复制生成的链接进入浏览器预览博客啦！！！
验证完后，回到命令行，ctrl+c 关闭预览

#### 7、上线博客

进入之前的Blog文件夹，打开_config.yml
编辑 url 段落

```bash
# URL
## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
url: https://76-s.github.io
```

拉到最下面将deploy后面的全注释下，复制粘贴这段

```bash
  deploy:
  type: git
  repo:
    github: git@github.com:76-s/76-s.github.io.git
  branch: main
  message: "V1.7.0"
```

注意缩进格式：每行前面都有两个空格不要删，每个冒号后面都有个空格也不要删！
去 github 之前生成的仓库页面，点 code，复制链接
将其粘贴到编辑器中的 repo：后面
然后保存退出
回到博客文件夹，进入终端
安装自动部署发布工具

```bash
npm install hexo-deployer-git --save
```

然后在Blog文件夹右键打开终端，依次输入

```bash
hexo g	# 生成
hexo d	# 上传
```

> 如果是第一次使用git的话会需要配置
> `git config --global user.email "你的邮箱"`
> `git config --global user.name "你的名字"`
> 配置完后再 hexo d 上传
> 在跳出来的窗口内进行登录
> 接下来就成功把本地内容上传到github了
> 上传成功以后，就算是搭建好了！上自己的网址看看吧
> 网址是我们之前设的仓库名：`用户名.github.io`

文章摘自🔗：https://blog.fiveth.cc/p/bb32/#%E5%87%86%E5%A4%87%E5%B7%A5%E5%85%B7









