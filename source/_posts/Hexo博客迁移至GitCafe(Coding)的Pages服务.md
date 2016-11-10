---
title: Hexo博客迁移至GitCafe(Coding)的Pages服务
tag:
- 静态博客
- Hexo
categories:
- 工具
---
这个博客之前是挂在GitHub的Pages空间，这两天发现访问博客的速度好慢啊。啊不对，是一直都很慢，这两天是完全打不开了。。。找了找能优化访问速度的办法，最后决定在GitCafe上也部署一个。GitCafe是国内版的GitHub，有了它访问速度真的快好多。国内的支持部署静态博客的代码托管仓库还有Coding，不过打开[GitCafe官网](https://coding.net/gitcafe.html)发现，它已经被[Coding](https://coding.net/)收购了。。。    
<!--more-->
![](/images/ToGitCafe/QQ20161109-2@2x.png)
### 迁移步骤
#### 注册Coding账号
用户名要想好，因为无法更改，而且博客的项目名称要跟用户名一致，作为博客网址的一部分。    
比如用户名是chang20159,项目名是chang20159，博客网址就是http://chang20159.coding.me/    
![](/images/ToGitCafe/QQ20161109-3@2x.png)

#### 创建项目并开启Pages服务
新建项目很简单，注意项目名最好与用户名一直，至于为什么我也不知道，没研究。。    
进入项目，在Pages服务中开启Pages服务，并设置分支为master,因为你的项目开发分支是master。    
这个与你要发布的分支名一直就可以。    
![](/images/ToGitCafe/QQ20161109-6@2x.png)    
如果有自己的域名的话，可以将它的cname指向博客地址，并在开启Pages服务后绑定到自己的域名。    
![](/images/ToGitCafe/QQ20161109-7@2x.png)
#### 编辑Hexo配置文件_config.yml
这个跟部署到GitHub是一样的，加上在coding.net上刚创建项目的仓库地址。

    deploy:
      type: git
      repo: 
         github: git@github.com:chang20159/chang20159.github.io.git
         coding: git@git.coding.net:chang20159/chang20159.git
      branch: master

#### 配置SSH
如果之前生成过ssh,可以直接使用，存放在~/.ssh/id_rsa.pub这个目录下。
打开个人设置-SSH公钥，把id_rsa.pub这个文件里的内容粘贴上去，点击添加就可以了。
这一步不做的话，在hexo deploy的时候会提示没有权限，无法部署的。    
![](/images/ToGitCafe/QQ20161109-4@2x.png)
#### 添加Staticfile文件
最后需要在source/目录下放置一个名为Staticfile的文件，coding.net需要这个文件来作为以静态文件部署的标志。如果没有这个文件，打开http://chang20159.coding.me/就会看到**404 page not found**。

    cd source/
    touch Staticfile  #名字必须是Staticfile

这样hexo deploy时就会同时部署到GitHub和Coding.net的Page服务上啦~~~   

    changdeMacBook-Pro:blog chang$ hexo deploy
    INFO  Deploying: git
    INFO  Clearing .deploy_git folder...
    INFO  Copying files from public folder...
    On branch master
    nothing to commit, working directory clean
    To git@git.coding.net:chang20159/chang20159.git
       c8979a1..18777cd  HEAD -> master
    Branch master set up to track remote branch master from git@git.coding.net:chang20159/chang20159.git.
    On branch master
    nothing to commit, working directory clean
    To git@github.com:chang20159/chang20159.github.io.git
       c8979a1..18777cd  HEAD -> master
    Branch master set up to track remote branch master from git@github.com:chang20159/chang20159.github.io.git.
    INFO  Deploy done: git 
然后http://chang20159.coding.me/和https://chang20159.github.io/都可以看到新发的内容啦~~~    
不过，新发布的内容coding上需要等几分钟才会更新。

### 参考
附上一篇写得很好的文章 》》》》》    

- [hexo同时托管到coding.net与github](https://segmentfault.com/a/1190000004548638)




