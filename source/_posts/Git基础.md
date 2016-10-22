---
title: Git基础
tag:
- git
categories:
- 工具
---
Git是一个版本控制与管理工具。在Mac OS X上可以使用Homebrew安装Git。关于Homebrew的安装参见[Homebrew 官网](http://brew.sh/)。安装完成后，终端执行brew install git。然后就可以开始使用git啦~~
<!-- more -->
## 文件的三种状态

git保存的不是文件差异，而是一系列的文件快照.    在git版本控制系统中，任何一个文件都有三种状态：
- 已修改(modified) 表明文件被修改，但没有提交保存（工作目录）
- 已暂存(staged)   表明已修改的文件放在下次提交需要保存的清单中（暂存区域，本质是一个简单的文件）
- 已提交(committed)  表明该文件已被安全的保存在本地仓库（本地仓库）
**一个已加入版本控制的文件的工作流：被修改－被暂存－被提交**
 

## git clone 

git clone [url] 
从已有的git仓库克隆一个镜像仓库出来，git支持多种数据传输协议
    
- git://   
   
- http(s)://    

- ssh://user@server:/path.git表示的SSH传输协议

git clone 本质上就是自动创建了本地的master分支用于跟踪远程的master分支

例：  在当前目录下创建demo目录，也可重新定义新建项目名称，在命令末尾指定新名称mygrit

    git clone git://github.com/chang20159/demo git-demo
 

## git status

确定哪些文件当前处于什么状态

例1:当前工作目录很干净，没有任何文件在赏析提交后更改过，也没有处于未跟踪的新文件，当前在alpha分支

	On branch alpha
	Your branch is up-to-date with 'origin/alpha'.
	nothing to commit, working directory clean
 

例2:新建test.ftl文件，没有修改，会提示有新建文件


	On branch alpha
	Your branch is up-to-date with 'origin/alpha'.
	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)
	
	        new file:   apollo-message-center-web/src/main/webapp/WEB-INF/pages/notice/test.ftl

 

例3:新建test.ftl，并修改，修改后处于未暂存状态，提示使用git add 对未被跟踪的文件跟踪，并将文件快照放入暂存区域。

在Changes to be committed下的都是已暂存状态，在Changes not staged for commit下的都是未暂存状态。若此时提交，则提交的是已暂存的文件，而不是修改后的文件，所以想提交修改后的文件，需要再次使用git add .将修改后的文件提交到暂存区域。

	On branch alpha
	Your branch is up-to-date with 'origin/alpha'.
	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)
	
	        new file:   apollo-message-center-web/src/main/webapp/WEB-INF/pages/notice/test.ftl
	
	Changes not staged for commit:
	  (use "git add <file>..." to update what will be committed)
	  (use "git checkout -- <file>..." to discard changes in working directory)
	
	        modified:   apollo-message-center-web/src/main/webapp/WEB-INF/pages/notice/test.ftl
 

例4. 对已修改的文件，未提交至暂存区域，红色显示，提示使用git add提交至暂存区域，或者直接使用 git commit -a将已修改但不在暂存区域的文

件一起提交。

	changdeMacBook-Pro:apollo-message-center chang$ git status
	On branch alpha
	Your branch is up-to-date with 'origin/alpha'.
	Changes not staged for commit:
	  (use "git add <file>..." to update what will be committed)
	  (use "git checkout -- <file>..." to discard changes in working directory)
	
	        modified:   apollo-message-center-web/src/main/webapp/WEB-INF/pages/notice/edit-notice.ftl
	
	no changes added to commit (use "git add" and/or "git commit -a")
	
	---------------------我是分割线--------------------------
	
	changdeMacBook-Pro:apollo-message-center chang$ git add .
	changdeMacBook-Pro:apollo-message-center chang$ git status
	On branch alpha
	Your branch is up-to-date with 'origin/alpha'.
	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)
	
	        modified:   apollo-message-center-web/src/main/webapp/WEB-INF/pages/notice/edit-notice.ftl
 

## git add 

多功能命令，根据目标文件状态的不同，作用也不同。

- 跟踪新文件，即将新文件纳入版本控制

- 将已跟踪的文件放入暂存区

- 在merge时可将有冲突的文件标记为已解决状态

 

## git diff 

查看尚未暂存的文件更新了哪些具体内容，比较的是工作目录当前文件与暂存区域快照之间的差异，即修改后还未暂存起来的变化内容。

 

git diff --cached   git 1.6.1及以上版本可使用git diff --staged，作用相同。

查看已暂存文件和上次提交时快照之间的差异

 

## git commit

提交到本地仓库，每次提交前需要确定文件是否都已暂存，commit不会纪录还未暂存的数据。

使用git status查看， 将修改过或新建的文件git add。

在git中提交时，会保存一个提交(commit)对象，该对象保存一个指向暂存内容快照的指针。

例1：直接使用git commit 不带参数，提示提交时要加上提交说明，＃号后是附加信息

	# Please enter the commit message for your changes. Lines starting
	# with '#' will be ignored, and an empty message aborts the commit.
 

例2: 在文件未放入暂存区时，使用git commit -m"test"，提交后git status发现仍然是Changes not staged for commit

因为git commit只提交处于暂存区域的文件，提交前需要git add

	changdeMacBook-Pro:apollo-message-center chang$ git commit -m"test "
	[alpha d51853e] test
	 Committer: chang <chang@changdeMacBook-Pro.local>
	Your name and email address were configured automatically based
	on your username and hostname. Please check that they are accurate.
	You can suppress this message by setting them explicitly:
	
	    git config --global user.name "Your Name"
	    git config --global user.email you@example.com
	
	After doing this, you may fix the identity used for this commit with:
	
	    git commit --amend --reset-author
	
	 1 file changed, 1 insertion(+), 1 deletion(-)
	changdeMacBook-Pro:apollo-message-center chang$ git st
	On branch alpha
	Your branch is ahead of 'origin/alpha' by 1 commit.
	  (use "git push" to publish your local commits)
	Changes not staged for commit:
	  (use "git add <file>..." to update what will be committed)
	  (use "git checkout -- <file>..." to discard changes in working directory)
	
	        modified:   apollo-message-center-web/src/main/webapp/WEB-INF/pages/notice/edit-notice.ftl
	
	no changes added to commit (use "git add" and/or "git commit -a")

 

例3.跳过使用暂存区域  git commit 加上-a参数，git会自动将所有已跟踪的文件暂存起来一并提交，从而跳过git add步骤  

   使用git commit -am"test"后，提示当前工作目录很干净，可以使用git push


	changdeMacBook-Pro:apollo-message-center chang$ git commit -am"test"
	[alpha 545fc8f] test
	 Committer: chang <chang@changdeMacBook-Pro.local>
	Your name and email address were configured automatically based
	on your username and hostname. Please check that they are accurate.
	You can suppress this message by setting them explicitly:
	
	    git config --global user.name "Your Name"
	    git config --global user.email you@example.com
	
	After doing this, you may fix the identity used for this commit with:
	
	    git commit --amend --reset-author
	
	 1 file changed, 1 insertion(+), 1 deletion(-)
	changdeMacBook-Pro:apollo-message-center chang$ git st
	On branch alpha
	Your branch is ahead of 'origin/alpha' by 2 commits.
	  (use "git push" to publish your local commits)
	nothing to commit, working directory clean
 

## git remote

查看当前的远程库，会列出每个远程库的简短名称，克隆一个项目后，至少有一个名为origin的远程库，git默认用origin来标识所克隆的原始仓库。

	changdeMacBook-Pro:apollo-message-center chang$ git remote
	origin
 

### git remote -v 

-v  --verbose的缩写，显示对应的克隆地址，若有多个远程仓库，会全部列出

	changdeMacBook-Pro:apollo-message-center chang$ git remote -v
	origin  git@code.dpoa.com:tg/apollo-message-center.git (fetch)
	origin  git@code.dpoa.com:tg/apollo-message-center.git (push)
 

## git fetch 

从远程仓库中抓取所有本地仓库还没有的数据到本地，执行命令后，可在本地访问该远程仓库中的所有分支。

若是克隆了一个仓库，会自动将远程仓库归于origin名下

git fetch origin会抓取从上次clone后或者上次fetch后别人push的更新。

**注意**：fetch命令只是将远端的其他人更新的分支数据拉到本地仓库，并不自动合并到当前工作分支，因而无法在本地编辑该远程仓库中的分支，需要手动合并到当前分支。

## git pull

若设置了某个分支用于跟踪某个远程仓库的分支，可使用pull抓取数据下来，并将远程分支自动合并到本地仓库的当前分支

git pull相当于 git fetch + git merge

## git push 

将本地仓库的数据推送到远程仓库

**注意**：在推数据之前，远程仓库已有其他人推送了若干更新，推送操作就会被驳回(rejected)，必须先pull到本地，若有conflict，要先解决冲突，再重新commit，重新push。


	changdeMacBook-Pro:apollo-message-center chang$ git push
	To git@code.dpoa.com:tg/apollo-message-center.git
	 ! [rejected]        alpha -> alpha (fetch first)
	error: failed to push some refs to 'git@code.dpoa.com:tg/apollo-message-center.git'
	hint: Updates were rejected because the remote contains work that you do
	hint: not have locally. This is usually caused by another repository pushing
	hint: to the same ref. You may want to first integrate the remote changes
	hint: (e.g., 'git pull ...') before pushing again.
	hint: See the 'Note about fast-forwards' in 'git push --help' for details.

## git branch

查看本地分支

	changdeMacBook-Pro:apollo-message-center chang$ git branch
	  20150604_chang_apolloMsgCenter
	  20150612_chang_edit_notice
	* alpha
	  master

### git branch -a

查看本地和远程分支 ，可以使用参数 -a 查看

	changdeMacBook-Pro:apollo-message-center chang$ git branch -a
	  20150604_chang_apolloMsgCenter
	  20150612_chang_edit_notice
	* alpha
	  master
	  remotes/origin/20150604_merge_branch
	  remotes/origin/20150604_jun_dev
	  remotes/origin/20150604_xu_add_message_DAO
	  remotes/origin/20150604_chang_apolloMsgCenter
	  remotes/origin/20150608_zhao_issue_notice_page
	  remotes/origin/20150609_xu_make_Administrator_UI
	  remotes/origin/20150612_chang_edit_notice
	  remotes/origin/HEAD -> origin/master
	  remotes/origin/alpha
	  remotes/origin/alpha_lightmerge
	  remotes/origin/beta_lightmerge
	  remotes/origin/master
	  remotes/origin/merge_alpha
 

## 根据远程分支创建本地分支

例如需要对远程分支remotes/origin/merge_alpha工作，需要创建本地分支对其跟踪

	git checkout -b  merge_alpha  origin/merge_alpha

**注**：从远程分支checkout出来的本地分支，称为跟踪分支(tracking branch)。 

## 根据本地分支创建远程分支

在当前分支使用git push ，会自动创建被跟踪的远程分支

## git branch
### 新建分支

	git branch ［branch-name］
新建分支，新建分支后不会自动切换到这个分支。 

### 查看所有远程分支
	git branch -r  

git保存一个HEAD指针，始终指向当前工作的本地分支。

	changdeMacBook-Pro:apollo-message-center chang$ git branch -r
	  origin/20150604_merge_branch
	  origin/20150604_jun_dev
	  origin/20150604_xu_add_message_DAO
	  origin/20150604_chang_apolloMsgCenter
	  origin/20150608_zhao_issue_notice_page
	  origin/20150609_xu_make_Administrator_UI
	  origin/20150612_chang_edit_notice
	  origin/HEAD -> origin/master
	  origin/alpha
	  origin/alpha_lightmerge
	  origin/beta_lightmerge
	  origin/master
	  origin/merge_alpha
 

### 删除本地分支
	git branch -d


## 删除远程分支alpha  
	git push origin/alpha

## 切换分支
### git checkout [branch-name]

切换分支，切换分支后，HEAD就指向切换后的分支

### git checkout -b [branch-name]

新建分支并切换到该分支，相当于git branch+git checkout

## git merge 

git merge [branch-name] 将branch-name分支合并到当前分支，branch-name分支可以是本地分支，也可以是远程分支。

合并时可能会遇到冲突，git  会在有冲突的文件中加入标准的冲突解决标记。

例如：当前分支alpha   使用git pull

	<<<<<<< HEAD:index.jsp
	<h1>Tp Message Center Web is running!!!</h1>
	======= 
	<h1>Hello World!</h1>
	>>>>>>> origin/alpha:index.jsp
======= 隔开的上半部分是HEAD中的内容，下半部分是远程alpha分支中的内容。
 解决冲突的办法：二者选其一或者整合，根据具体情况选择是删除还是保留。<<<<<<< ======= >>>>>>> 这三行都要删掉

冲突解决后使用git add将冲突文件标记为已解决状态，本质上是将快照放到暂存区。确认冲突都解决后，git commit完成此次合并的提交。


## .gitignore文件

有些文件不需要进行版本控制，不需要纳入git管理，也不希望总出现在未跟踪文件列表，比如日志文件、编译过程中创建的临时文件等等。

可以创建一个名为.gitignore的文件，列出要忽略的文件模式

## .git文件

如果需要删除对文件的版本控制，可以直接删除.git文件

参考：    
- http://www.open-open.com/lib/view/open1328069609436.html


