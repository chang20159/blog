---
title: 使用Hexo+GitHub搭建静态博客
tag:
- Hexo
- GitHub
categories:
- 静态博客
---
一直都想有个自己的空间写写东西，现在终于动起来啦~    
网上找了好久的教程，选择方案也有很多，最后还是觉得基于GitHub，用Hexo来搭建最省事。（ps: 一开始还想自己写个主题来着，后来写哭了。。太费时费力）     
关于主题的选择也是纠结了很久，一直找不到喜欢的主题，这个博客主题也是无意在一个网友的个人博客看到的，有了喜欢的主题，我才开始搭建这个博客。。。
<!-- more -->
来记录一下博客搭建的过程吧~    
首先，使用任何有官方的东东都别忘了去官网溜一圈，[Hexo官网](https://hexo.io/)，还有[官方文档](https://hexo.io/docs/)和[官网提供的主题](https://hexo.io/themes/)
<!-- more -->
### 这样只能本地查看个人博客
#### 安装 hexo
	npm install hexo-cli -g

####  新建项目目录blog,并在此目录下初始化hexo 
	mkdir blog
	cd blog
	hexo init

#### 安装依赖
	npm install

#### 启动hexo
	hexo server

命令行会有提示，在浏览器地址栏输入http://localhost:4000，就能看到hexo的实例页面。

### 这样就能让别人看到你的博客

#### GitHub新建项目
项目的名字得是这样： chang20159.github.io ，chang20159是github的账号
#### 编辑hexo的配置文件 _config.yml
    # Deployment
    ## Docs: https://hexo.io/docs/deployment.html
    deploy:
      type: git
      repo: git@github.com:chang20159/chang20159.github.io.git
      branch: master
注意：   
 
- type、repo、branch的前面有两个空格，后面的:后面有一个空格    
- repo的值是上面创建的项目的github仓库地址
这样就把项目关联到这个github项目上了。

#### 部署hexo
首先要安装git插件，这样才能把本地资源部署到github服务器上 
   
	npm install hexo-deployer-git --save
然后执行

	hexo deploy

现在可以打开https://chang20159.github.io/，并且别人也可以来访问了。

### 写博客
发布的文章都在soource/_posts目录下，要写一篇文章可以直接在这个目录下新建一个markdown文件，也可以用这个命令

    hexo new "使用Hexo+GitHub搭建静态博客"
写好之后保存下来，依次执行下面的命令：

    hexo clean
    hexo generate
    hexo server
    hexo deploy
https://chang20159.github.io/就能看到最新文章。

可以先在本地写好再部署，执行hexo server后，在本地修改是可以实时更新到http://localhost:4000的

### 更换主题

hexo默认的主题是landscape,我使用的主题是hexo-theme-next

#### 下载主题
找到喜欢的主题，从github上clone到themes目录下，https://github.com/iissnan/hexo-theme-next
#### 配置主题 
编辑_config.yml文件
 
    # Extensions
    ## Plugins: https://hexo.io/plugins/
    ## Themes: https://hexo.io/themes/
    theme: hexo-theme-next
theme的值设置成主题名，保存后重新部署就好了。    
hexo-theme-next这个主题功能蛮强大的，现在有三种页面模式，改一个配置相当于换一个主题，只要在主题的配置文件中设置这个字段

    # Schemes
    # scheme: Muse
    # scheme: Mist
    scheme: Pisces
它还有很全的配置文档。

- [hexo-theme-next 作者博客](http://notes.iissnan.com/)
- [hexo-theme-next GitHub](https://github.com/iissnan/hexo-theme-next)
- [hexo-theme-next 配置文档](http://theme-next.iissnan.com/)

### 参考文章
- [静态博客框架之Hexo & Jekyll](http://www.jianshu.com/p/ce1619874d34)
- [从零开始定制hexo主题](https://maintao.com/2014/hexo-theme-from-scratch/)
- [手把手教从零开始在GitHub上使用Hexo搭建博客教程(一)-附GitHub注册及配置](https://segmentfault.com/a/1190000005590795)
- [手把手教从零开始在GitHub上使用Hexo搭建博客教程(二)-Hexo参数设置](https://segmentfault.com/a/1190000005609509)
- [Hexo博客主题推荐](https://www.aswifter.com/2016/01/18/hexo-themes/)
- [leancloud官网](https://leancloud.cn/)
- [多说官网](http://duoshuo.com/)