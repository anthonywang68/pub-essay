---
title: git--summary
date: 2019-10-10 15:23:28
tags:
---

下面是我整理的常用 Git 命令清单。几个专用名词的译名如下。

> - Workspace：工作区
> - Index / Stage：暂存区
> - Repository：仓库区（或本地仓库）
> - Remote：远程仓库

<!--more-->



最近我们的老司机推荐做法来解决线上代码bug，做个笔记：

```
 拉一个新分支，老司机都推荐这样做，充分利用了git特性
先暂存一下工作空间改动：
git stash

新建一个分支，并且换到这个新分支
git branch fix_bug //新建分支
git checkout fix_bug //切换分支

这时候就可以安心的在这个fix_bug分支改bug了，改完之后：
git add .

git commit -m "fix a bug"

切换到master主分支
git checkout master

从fix_bug合并到master分支
git merge fix_bug
提交代码
git push

然后从暂存区恢复代码
git stash pop 
```



1. 使用git实现版本控制--本质 : git帮助我们管理文件夹

2. 进入要管理的文件夹

3. 初始化 git init  (提名 : 宣告git是老大) `git bash here选项卡`,会出现`.git文件夹 `

4. 管理` git add . ` / `git add flename`管理某一个具体filename的文件

5. 生成版本 -->`git commit -m "这是描述版本的信息"`

6. 查看版本 `git log`

   ### 1.总结

   ```python
   1.进入管理的目录
   2.git init 初始化,让git管理当前文件夹
   3.git status 检测当前目录下文件的状态
   4.三种状态的变化
   	1.红色: 新增/修改的文件  --> git add 文件名   / git add .
       2.绿色 : git已经管理起来的  --> git commit -m "版本描述"
       3.生成版本
   5.个人信息配置 :用户名　邮箱
       git config --global user.email "1821333144@qq.com"
       git config --global user.name "DengJack"
   6.生成版本
   	git commit -m "版本描述"
   7.查看版本记录
   	git log     只能看该版本之前的版本
       git reflog  看所有的版本,之后的版本也能拿到,可以任意回滚了.
   8.版本回滚
   	git reset --hard 版本号(前3位就可以了)
   ```

   ### 2.分支(环境的隔离)

   1. 线上代码出现bug紧急修复

      ```python
      出现bug：
      
      1. 创建debug分支
      	git branch bug
      2. 切换到debug分支，修改bug
      	git checkout bug
          # 然后修改bug
      3. 修改完成后提交，切换到master，合并debug的分支
      	git add . 提交修复bug后
          git commit -m "修复完bug"   此时bug分支修复完成,master还没有修复
          git checkout master 切换回master分支
          git merge bug   合并bug分支
          
          
      4. 切换到dev分支,开发分支. 合并master/debug分支
      
      
      5. 删除debug的分支
      	git branch -d bug   删除bug分支
      ```

   2. 分支名分支名

      ```python
      主干线分支master ,线上分支,正在
      开发分支 dev
      修复bug的分支 debug
      ```

   3. 命令

      ```python
      git branch 查看当前分支
      git branch dev  创建dev分支,当前还是master分支
      git checkout dev(分支名)  切换到dev分支
      # 分支之间互不干扰 
      git merge 要合并的分支
      	注意: 切换分支在合并
                可能会产生冲突(手动解决冲突)
      git branch -d 分支 删除分支
      ```

   ### 3. 工作流

   ### 代码托管github/码云/gitlab

   1. github为例

      ```python
      1.注册github账号
      2.创建仓库
      	仓库名 --> 推荐项目名
          描述
          公有or私有仓库类型
          3--> README  .gitignore   license许可证
          
          得到远程仓库 https://xxxxxx
      3.本地代码推送
      	echo "# "  >> README.md
          git init
          git add README.md
          git commit -m "第一次提交"
          
          git remote add origin https://xxxxx # 给远程仓库起别名origin/xxx/second....
          git push -u origin master
          git push -u origin dev 把本地推送到远程dev分支
      4.本地拉取代码
        方法1 :
      	git clone https://xxxxx 把代码拉取回来(把所有分支都拉取回来了),但不显示.
          git checkout dev
          开始在家开发
      5.开发
      	在公司是
          git branch dev
          开发
          git add .
          git commit -m "xxx"
          git push origin dev
          下班回家
          更新最新的dev
          git pull origin dev 获取到最新的代码了
          在家开发了新的
          git add .
          git commit -m "在家开发了"
          git push origin dev
          睡觉
          第二天,到公司去了
          git pull origin dev
          	等同 git fetch origin dev
              	 git merge dev
          又继续开发了.
      ```

      ![1570628306296](D:\md_down_png\1570628306296.png)![1570628326629](D:\md_down_png\1570628326629.png)

   开发完毕,准备上线

   ```python
   1.将dev分支合并到master上,上线
       git branch master
       git mergr dev
       git push origin master
   2 将dev分支也推送到远程
   	git checkout dev
       git merge master
       git push origin dev
   ```

   ### 5.git rebase(变基)

   使git记录变得简洁~

   1. 第一种:多个提交记录整合成一个记录

      ```python
      git rebase -i 老版本号  将当前版本的提交记录 合并到 老板本提交记录
      git rebase -i HEAD~3 将当前开始,找最近的3条记录合并.
      	1.把pick改成s,只保留一个pick
          2.合并往后,整合版本描述
      注意: 合并记录是,不要合并已经push到仓库的记录.
      ```

   2. 类似合并的功能,把dev的提交记录合并到master分支上.

      ```python
      git checkout dev
      git rebase master  类似合并
      git checkout master
      git merge dev
      ```

   3. 忘记推送,导致合并冲突

      ```python
      git pull xxx
       = git fetch
       = git merge ,会产生分叉的
      不执行它了
      git fetch origin dev
      git rebase origin/dev   -->不会有分叉
      ```

   4. rebase 冲突处理

      ```python
      1.产生冲突
      2.解决冲突
      3.git add .
      4.git rebase --continue   继续rebase
      ```

   ### 6.beyond compare软件来解决冲突

   1. 安装beyond compare

   2. 在git中配置

      ```python
      git config --local merge.tool bc3
      git config --local meretool.path "/usr/local/bin/bccom"
      git config --local mergeool.keepBackup false
      	--local 指该配置只在当前生效
      ```

   3. 合并产生冲突后,解决冲突

      ```python
      git mergetool   自动打开,自动打开冲突文件.
      ```

   ## 5.多人协同开发

   ### 2.项目开始

   ```python
   1.创建master,创建项目 和 版本
       mkdir dbhot
       touch app.py 开发中
       git init
       git add .
       git commit -m "第一次提交"
   2.创建多人协作仓库
   	方法1:
           创建仓库
   		进入仓库-->settings-->collaborators(合作者)-->输入其他人的git用户名-->其他人会接受到邮件,同意后,成为协作者.
       方法2: (公司做法)
           创建组织(一个组织可以有n个仓库),公司有多个项目,放到组织中
           在组织内创建仓库    dbhot
   3.git remote add origin https://仓库地址
     git push origin master
     设置版本 :　v1 v1.1 v2.3.2
           git log
           git tag -a v1 -m "第一版"
           在本地给这次提交加上了一个版本
           git push origin --tags 推送到远程仓库
   ```

   ![1570670917673](D:\md_down_png\1570670917673.png)![1570670981489](D:\md_down_png\1570670981489.png)

   ### 3.邀请小弟,小弟开发邀请小弟,小弟开发

   ```python
   1.小弟注册账号,获取其用户名
   2.邀请成员进组织 member/owner ,小弟会接受收到邮件,同意加入组织.组织成员成为2.
   3.组织内可以有很多的项目,默认权限只有read
   4.进入项目,设置权限settings-->合作者,再把小弟邀请进来.小弟加入组织,也加入了这个项目,对该项目由write的权限了.
   ### 小弟开发  ####
   5.mkdir brother
     cd brother/
     git clone https://xxxxxxx 项目仓库
     cd 项目目录(cd dbhot)
     git checkout dev      # 切换到开发分支
     git checkout -b ddz   # 自己新建一个分支  
     开发中 ... ... ...
     git add .
     git commit -m "ddz斗地主开发完成"
     git push origin ddz
   ```

   ### 4. 申请代码review

   ```python
   通过 pull request/merge request实现
   1.配置(loader来做)
   	项目settings > Branches
       给分支添加规则 ,给 dev分支添加规则,合并前review
   2.小弟发起申请pull request
   	把ddz 分支 合并到dev分支,写描述信息
   3.loader会受到一个pull request申请.
   	add your review  查看所有新代码
       merge pull request 可以让你进行合并,已经review
       confirm merge   确定合并,完成合并.
       功能开发完了,ddz分支,可以删除了.
   ```

   ![1570672318029](D:\md_down_png\1570672318029.png)

   ![1570672500488](D:\md_down_png\1570672500488.png)

   ![1570672625251](D:\md_down_png\1570672625251.png)

   ### 5.测试&上线

   ```python
   # 测试团队&loader来做
   1.在dev分支下,  git checkout -b release  # release测试分支
    在release测试
   2.申请pull request. release --> master
   3.代码view , 同意合并
   4.在dev下,把release分支合并,release分支删除.
   	git branch -d release
   5.	git checkout master
   	git pull origin master # 拉取最新master版本
   6.创建版本tag 
   	git tag -a v2 -m "第二版,斗地主功能"
       git push origin --tags
   7.运维人员就可以下载代码做上线了
   	git clone -b v2 地址
   ```

   ### 6.给开源项目贡献代码

   1. fork源代码,将别人的代码fork到自己的远程仓库里去

   2. 从自己的远程仓库git clone https://github.com/DengJackNo1/tornado.git

   3. 在电脑本地修改bug

   4. git add  git commit -m "修复bug/贡献代码" git push origin master ,提交到自己的仓库 . 

   5. 给源代码的作者提交 修复bug 的申请(create new pull request)

      ![1570667910147](D:\md_down_png\1570667910147.png)

      ![1570667998108](D:\md_down_png\1570667998108.png)

      ![1570668024072](D:\md_down_png\1570668024072.png)

      之后作者会检查,这个bug怎么样.即可.

   ## 6.git的配置文件

   ### 1.项目配置文件: 项目/.git/config

   ```python
   git config --local user.name "dyx"
   git config --local user.email "xxx@com"
   
   ```

   ### 2.全局配置文件 : 当前用户目录/.gitconfig

   ```python
   git config --global user.name "xxxx"
   ```

   ### 3.系统配置文件:  /etc/.gitconfig

   ```python
   git config --system user.name "xxx"
   注意 : 需要有root权限
   ```

   ### 4.应用场景

   ```python
   git config --local merge.tool bc3
   git config --local meretool.path "/usr/local/bin/bccom"
   git config --local mergeool.keepBackup false
   
   git remote add origin 地址: 默认添加到本地配置文件(--local)
   ```

   ### 5.window的配置文件

   ```python
   全局配置文件通常在【C:\Users\Administrator\.gitconfig】
   ```

   

   ### 5.免密码登陆

   #### 1.url体现

   ```python
   原来的地址 : https://github.com/DengJackNo1/web_framework.git
   修改的地址:  https://用户名:密码@github.com/DengJackNo1/web_framework.git
               
   git remote add origin https://用户名:密码@github.com/DengJackNo1/web_framework.git
   git push origin master
   ```

   #### 2.ssh实现(公钥私钥)

   ```python
   1.生成公钥和私钥,默认放在 ~/.ssh 目录下, id_rsa.pub -->公钥   id_rsa -->私钥
   	ssh-keygen(git终端)
   2.拷贝公钥的内容,设置到github中.	右上角  用户->sttings-->SSH and GPG keys-->SSH keys -->new SSH key-->粘贴公钥的内容
   3.在git本地中配置ssh地址
   	git remote add origin shh地址
   4.以后使用
   	git push origin master
   ```

   #### 3.git自动管理凭证

   ```python
   在项目所在目录 git bash
   git config  credential.helper store  建议只对当前仓库 有效
   or git config --global credential.helper store
   在输入一次密码后,自动保存.
   ```

   

   ### 6. git忽略文件 .gitignore

   ```python
   让git不再管理当前目录的某些文件
   #####.gitignore文件######
   a.py
   b.py  # 忽略 a.py  b.py文件
   .gitignore
   *.py  # 忽略 py格式的文件
   files/  # 忽略fiels文件夹下所有文件
   非
   !a.py   # a.py文件除外,git需要管理
   
   *.py[c|a|d] # 正则 , pyc pya pyd 后缀忽略.
   ########################
   ```

   更多参考 : https://github.com/github/gitignore

   ## 7. 任务管理

   1. issues

   ![1570670145803](D:\md_down_png\1570670145803.png)

   2. wiki 项目wiki ,项目的所有文档都写在wiki内(项目描述)

      ![1570670267464](D:\md_down_png\1570670267464.png)

      

   ## 8 . 总结

   ```python
   git init
   git add .   git add filanme
   git commit -m "xxx"
   git status
   git config --global user.email "1821333144@qq.com"
   git config --global user.name "DengJack"
   git log
   git reflog
   git reset --hard
   添加远程连接(别名)
   git remote add origin 地址
   推送代码
   git push origin dev
   下载代码
   git clone 地址 (所有的分支)
   拉取代码
   git pull origin dev
   	等价于
       git fetch origin dev
       git merge origin/dev
   保持代码提交整洁(变基)
   git rebase 分支 # -->保持代码提交简洁 ,和 merge 差不多 . 
   记录图形展示
   git log --graph --pretty=format:"%h %s"
   创建并切换到分支
   git checkout -b dev
   ```

   

   ### 1.版本控制

   ```python
   git init  
   
   配置
   
   git config  -- global  user
   
   git config  -- global  email
   
   git add 文件 
   
   git add .  
   
   git  commit -m  '描述'
   
   git log   
   
   git reflog   
   
   git reset --hard   
   
   git status
   
   
   
   git remote add  origin 'url'
   
   git push origin  master 
   
   git pull  origin master 
   
   git clone  url 
   ```

   ### 2.分支

   ```python
   git branch 查看分支
   
   git branch dev  创建dev分支
   
   git checkout dev    切换分支
   
   git merge dev    合并分支
   
   git branch -d debug  删除分支
   
   
   
   个人开发的流程
   
   分支  master  dev
   
   开发时在dev上开发 
   
   写完新的功能 合并到master上
   
   出现bug：
   
   1. 创建debug分支
   2. 切换到debug分支，修改bug
   3. 修改完成后提交，切换到master，合并debug的分支
   4. 切换到dev分支 合并master/debug分支
   5. 删除debug的分支
   
   注意：合并分支有冲突，手动解决冲突
   
   团队合作开发
   
   分支  master dev  star  maple
   
   每个人在自己的分支上操作
   
   开发完功能 本地代码推送到自己的分支上
   
   创建pull request 合并到dev上
   
   领导审核代码  接收合并
   
   dev  ——》 master
       1.动态生成一级菜单
   
       2.动态生成二级菜单
   
   ```

   ### 3.git版本回退

   ```python
   git reset --hard 版本号
   
   git log 
   
   git reflog
   ```

   ### 4. 克隆 推送 拉取

   ```python
   git clone https://gitee.com/old_boy_python_stack_21/teaching_plan.git  克隆远程仓库
   
   git push origin master 推送本地代码到远程仓库
   
   git pull origin master  拉取远程仓库代码到本地仓库
   ```

   

   ##git log
   使用git log命令，什么参数都没有的话，会以下面的格式输出所有的日志（我当前的git仓库只有三个提交）。如果日志特别多的话，在git bash中，按向下键来查看更多，按q键退出查看日志。

   ```cmd
   $ git log
   commit c08099d1cf05fdc541752b049a91b92bdcf78a12
   Author: zdk <zdk@menhoo.com>
   Date:   Mon Jun 19 23:08:07 2017 +0800
   	add hello.txt to git rep
   
   commit 723687a41685667a01dbd6254eb148d19501c3f1
   Author: zdk <zdk@menhoo.com>
   Date:   Sun Jun 18 22:27:29 2017 +0800
   	add c.txt
   
   commit 1a29bde9519195f14e98270c29d125e9d18b8d87
   Author: zdk <zdk@menhoo.com>
   Date:   Sun Jun 11 22:40:21 2017 +0800
   	新增了a.txt和b.txt文件
   
   
   git log --oneline
   --oneline参数可以将每条日志的输出为一行，如果日志比较多的话，用这个参数能够使结果看起来比较醒目。为了节约日志的篇幅，我后面也会频繁地使用这个参数。
   
   $ git log --oneline
   c08099d add hello.txt to git rep
   723687a add c.txt
   1a29bde 新增了a.txt和b.txt文件
   
   git log -[length]
   -[length]参数用于指定显示多少条日志
   
   $ git log --oneline -2
   c08099d add hello.txt to git rep
   723687a add c.txt
   ```

   ```python
   git log --skip=[skip]
   --skip=[skip]参数用来指定跳过前几条日志。下面的命令用来查看第二和第三条日志
   
   $ git log --skip=1 -2 --oneline
   723687a add c.txt
   1a29bde 新增了a.txt和b.txt文件
   
   
   git log --pretty=raw
   我在上面多次使用--oneline是为了节约文章篇幅，使文章看起来尽量整洁。而--pretty=raw则会显示出关于每次提交的更多信息
   
   $ git log --pretty=raw -1
   commit c08099d1cf05fdc541752b049a91b92bdcf78a12
   tree 5ef6cd7051101c4294cb92980f0cf3740478e120
   parent 723687a41685667a01dbd6254eb148d19501c3f1
   author zdk <zdk@menhoo.com> 1497884887 +0800
   committer zdk <zdk@menhoo.com> 1497884887 +0800
   
       add hello.txt to git rep
   
   ```

   ```python
   git log -p
   -p参数输出的信息会更多，用来显示提交的改动记录，相当于多次使用git show [commit_id]的结果。
   
   
   $ git log -1 -p
   commit c08099d1cf05fdc541752b049a91b92bdcf78a12
   Author: zdk <zdk@menhoo.com>
   Date:   Mon Jun 19 23:08:07 2017 +0800
   
       add hello.txt to git rep
   
   diff --git a/hello.txt b/hello.txt
   new file mode 100644
   index 0000000..ce01362
   --- /dev/null
   +++ b/hello.txt
   @@ -0,0 +1 @@
   +hello
   ```

   ### 5. 搜索git日志

   #### 通过作者搜索

   ```python
   $ git log --author yourname
   可以筛选出yourname用户提交的所有日志。这里的yourname可以包含通配符，从Author: zdk <zdk@menhoo.com>的信息中匹配信息。
   ```

   #### 通过提交关键字搜索

   ```python
   $ git log --grep keywords
   可以从提交的关键字中抓取匹配的commit项。
   ```

   #### 通过文件名搜索

   ```python
   git log -p -- RELEASE-NOTE.md
   有时候，我们想查某个文件的所有修改记录，可以根据文件名来过滤一下只跟这个文件有关的提交，就可以使用-p参数
   
   注意，这个--后面跟的是完整的文件名的相对地址，不是模糊匹配。如果你的文件的相对地址是config/my.config的话，你就需要用下面的命令
   git log -p -- config/my.config
   
   ```

   

   

   

   

   

   

   

## 一、新建代码库

> ```bash
> # 在当前目录新建一个Git代码库
> $ git init
> 
> # 新建一个目录，将其初始化为Git代码库
> $ git init [project-name]
> 
> # 下载一个项目和它的整个代码历史
> $ git clone [url]
> ```

## 二、配置

Git的设置文件为`.gitconfig`，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。

> ```bash
> # 显示当前的Git配置
> $ git config --list
> 
> # 编辑Git配置文件
> $ git config -e [--global]
> 
> # 设置提交代码时的用户信息
> $ git config [--global] user.name "[name]"
> $ git config [--global] user.email "[email address]"
> ```

## 三、增加/删除文件

> ```bash
> # 添加指定文件到暂存区
> $ git add [file1] [file2] ...
> 
> # 添加指定目录到暂存区，包括子目录
> $ git add [dir]
> 
> # 添加当前目录的所有文件到暂存区
> $ git add .
> 
> # 添加每个变化前，都会要求确认
> # 对于同一个文件的多处变化，可以实现分次提交
> $ git add -p
> 
> # 删除工作区文件，并且将这次删除放入暂存区
> $ git rm [file1] [file2] ...
> 
> # 停止追踪指定文件，但该文件会保留在工作区
> $ git rm --cached [file]
> 
> # 改名文件，并且将这个改名放入暂存区
> $ git mv [file-original] [file-renamed]
> ```

## 四、代码提交

> ```bash
> # 提交暂存区到仓库区
> $ git commit -m [message]
> 
> # 提交暂存区的指定文件到仓库区
> $ git commit [file1] [file2] ... -m [message]
> 
> # 提交工作区自上次commit之后的变化，直接到仓库区
> $ git commit -a
> 
> # 提交时显示所有diff信息
> $ git commit -v
> 
> # 使用一次新的commit，替代上一次提交
> # 如果代码没有任何新变化，则用来改写上一次commit的提交信息
> $ git commit --amend -m [message]
> 
> # 重做上一次commit，并包括指定文件的新变化
> $ git commit --amend [file1] [file2] ...
> ```

## 五、分支

> ```bash
> # 列出所有本地分支
> $ git branch
> 
> # 列出所有远程分支
> $ git branch -r
> 
> # 列出所有本地分支和远程分支
> $ git branch -a
> 
> # 新建一个分支，但依然停留在当前分支
> $ git branch [branch-name]
> 
> # 新建一个分支，并切换到该分支
> $ git checkout -b [branch]
> 
> # 新建一个分支，指向指定commit
> $ git branch [branch] [commit]
> 
> # 新建一个分支，与指定的远程分支建立追踪关系
> $ git branch --track [branch] [remote-branch]
> 
> # 切换到指定分支，并更新工作区
> $ git checkout [branch-name]
> 
> # 切换到上一个分支
> $ git checkout -
> 
> # 建立追踪关系，在现有分支与指定的远程分支之间
> $ git branch --set-upstream [branch] [remote-branch]
> 
> # 合并指定分支到当前分支
> $ git merge [branch]
> 
> # 选择一个commit，合并进当前分支
> $ git cherry-pick [commit]
> 
> # 删除分支
> $ git branch -d [branch-name]
> 
> # 删除远程分支
> $ git push origin --delete [branch-name]
> $ git branch -dr [remote/branch]# 本地存在一个分支，名称叫：develop_chen，但远程没有怎么办？
> git push origin develop_chen这样就在远程建立一个和本地一样的分支 git branch --set-upstream-to=origin/develop  develop  本地分支和远程分支简历跟踪关系
> ```

## 六、标签

> ```bash
> # 列出所有tag
> $ git tag
> 
> # 新建一个tag在当前commit
> $ git tag [tag]
> 
> # 新建一个tag在指定commit
> $ git tag [tag] [commit]
> 
> # 删除本地tag
> $ git tag -d [tag]
> 
> # 删除远程tag
> $ git push origin :refs/tags/[tagName]
> 
> # 查看tag信息
> $ git show [tag]
> 
> # 提交指定tag
> $ git push [remote] [tag]
> 
> # 提交所有tag
> $ git push [remote] --tags
> 
> # 新建一个分支，指向某个tag
> $ git checkout -b [branch] [tag]
> ```

## 七、查看信息

> ```bash
> # 显示有变更的文件
> $ git status
> 
> # 显示当前分支的版本历史
> $ git log
> 
> # 显示commit历史，以及每次commit发生变更的文件
> $ git log --stat
> 
> # 搜索提交历史，根据关键词
> $ git log -S [keyword]
> 
> # 显示某个commit之后的所有变动，每个commit占据一行
> $ git log [tag] HEAD --pretty=format:%s
> 
> # 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
> $ git log [tag] HEAD --grep feature
> 
> # 显示某个文件的版本历史，包括文件改名
> $ git log --follow [file]
> $ git whatchanged [file]
> 
> # 显示指定文件相关的每一次diff
> $ git log -p [file]
> 
> # 显示过去5次提交
> $ git log -5 --pretty --oneline
> 
> # 显示所有提交过的用户，按提交次数排序
> $ git shortlog -sn
> 
> # 显示指定文件是什么人在什么时间修改过
> $ git blame [file]
> 
> # 显示暂存区和工作区的代码差异
> $ git diff
> 
> # 显示暂存区和上一个commit的差异
> $ git diff --cached [file]
> 
> # 显示工作区与当前分支最新commit之间的差异
> $ git diff HEAD
> 
> # 显示两次提交之间的差异
> $ git diff [first-branch]...[second-branch]
> 
> # 显示今天你写了多少行代码
> $ git diff --shortstat "@{0 day ago}"
> 
> # 显示某次提交的元数据和内容变化
> $ git show [commit]
> 
> # 显示某次提交发生变化的文件
> $ git show --name-only [commit]
> 
> # 显示某次提交时，某个文件的内容
> $ git show [commit]:[filename]
> 
> # 显示当前分支的最近几次提交
> $ git reflog# 从本地master拉取代码更新当前分支：branch 一般为master$ git rebase [branch]
> ```

## 八、远程同步

> ```bash
> $ git remote update  --更新远程仓储
> # 下载远程仓库的所有变动
> $ git fetch [remote]
> 
> # 显示所有远程仓库
> $ git remote -v
> 
> # 显示某个远程仓库的信息
> $ git remote show [remote]
> 
> # 增加一个新的远程仓库，并命名
> $ git remote add [shortname] [url]
> 
> # 取回远程仓库的变化，并与本地分支合并
> $ git pull [remote] [branch]
> 
> # 上传本地指定分支到远程仓库
> $ git push [remote] [branch]
> 
> # 强行推送当前分支到远程仓库，即使有冲突
> $ git push [remote] --force
> 
> # 推送所有分支到远程仓库
> $ git push [remote] --all
> ```

## 九、撤销

> ```bash
> # 恢复暂存区的指定文件到工作区
> $ git checkout [file]
> 
> # 恢复某个commit的指定文件到暂存区和工作区
> $ git checkout [commit] [file]
> 
> # 恢复暂存区的所有文件到工作区
> $ git checkout .
> 
> # 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
> $ git reset [file]
> 
> # 重置暂存区与工作区，与上一次commit保持一致
> $ git reset --hard
> 
> # 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
> $ git reset [commit]
> 
> # 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
> $ git reset --hard [commit]
> 
> # 重置当前HEAD为指定commit，但保持暂存区和工作区不变
> $ git reset --keep [commit]
> 
> # 新建一个commit，用来撤销指定commit
> # 后者的所有变化都将被前者抵消，并且应用到当前分支
> $ git revert [commit]
> 
> # 暂时将未提交的变化移除，稍后再移入
> $ git stash
> $ git stash pop
> 关于现场保护
> 
> 使用场景：正在开发新功能，突然来了一个bug，需要马上修复，但是新功能没有做完，又不想产生一个无谓的commit提交节点，可以使用现场保护，bug修复完成之后，可以进行现场恢复，继续进行开发。
> git stash: 保护现场
> git stash pop: 现场恢复
> ```

## 十、其他(合并两个分支：Merge)

　　1，将开发分支代码合入到master中

```
git checkout dev           #切换到dev开发分支
git pull

git checkout master
git merge dev              #合并dev分支到master上
git push origin master     #将代码推到master上
```

 

　　2，将master的代码同步更新到开发分支中

　　merge方法：保证主干提交线干净(可以安全回溯)

```
git checkout master
git pull
git checkout dev
git merge master
git pull origin dev
```

> ```bash
> # 生成一个可供发布的压缩包
> $ git archive
> ```

补充10.29

```
【修复gitignoe文件】

    git rm -r --cached .
    git add .
    git commit -m 'update .gitignore'
    git  push

【代码开发错分支】

    git add .      (把所有改动暂存)
    git stash     (把暂存的文件提交到git的暂存栈)
    git checkout  本该提交代码的分支
    git stash pop (将暂存栈中的代码放出来)

【回滚到某个commit】

    git reset --hard af559448cccfb991e455c47682ca763ee508330e
    git push origin develop -f

【 放弃本地修改 强制更新为远程指定分支】

    git fetch --all
    git reset --hard origin/develop

【 从git里面提取两个commit之间变更过的文件，打包下载】
上线代码时，覆盖修改过的代码，很实用

git archive --format=zip HEAD `git diff --name-only 8bbf69c253801228ff504ab080ce7cf44a924971 a27d045d8c60d6c62a4061b94763886577e1c0eb` & my_diff.zip

```

