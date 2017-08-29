---
title: '[转]git常用命令'
id: 287
categories:
  - Git
date: 2016-10-02 20:57:47
tags:
---

[Git常用命令](http://www.cnblogs.com/vman/articles/Git_cmds.html)
<div class="clear"></div>
<div class="postBody">
<div id="cnblogs_post_body">

**查看、添加、提交、删除、找回，重置修改文件**

git help &lt;command&gt; # 显示command的help

git show # 显示某次提交的内容 git show $id

git co -- &lt;file&gt; # 抛弃工作区修改

git co . # 抛弃工作区修改

git add &lt;file&gt; # 将工作文件修改提交到本地暂存区

git add . # 将所有修改过的工作文件提交暂存区

git rm &lt;file&gt; # 从版本库中删除文件

git rm &lt;file&gt; --cached # 从版本库中删除文件，但不删除文件

git reset &lt;file&gt; # 从暂存区恢复到工作文件

git reset -- . # 从暂存区恢复到工作文件

git reset --hard # 恢复最近一次提交过的状态，即放弃上次提交后的所有本次修改

git ci &lt;file&gt; git ci . git ci -a # 将git add, git rm和git ci等操作都合并在一起做　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　git ci -am "some comments"

git ci --amend # 修改最后一次提交记录

git revert &lt;$id&gt; # 恢复某次提交的状态，恢复动作本身也创建次提交对象

git revert HEAD # 恢复最后一次提交的状态

**查看文件diff**

git diff &lt;file&gt; # 比较当前文件和暂存区文件差异 git diff

git diff &lt;<span id="MathJax-Element-1-Frame" class="MathJax" tabindex="0" data-mathml="&lt;math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;&gt;&lt;mi&gt;i&lt;/mi&gt;&lt;mi&gt;d&lt;/mi&gt;&lt;mn&gt;1&lt;/mn&gt;&lt;mo&gt;&amp;gt;&amp;lt;&lt;/mo&gt;&lt;/math&gt;"><span id="MathJax-Span-1" class="math"><span id="MathJax-Span-2" class="mrow"><span id="MathJax-Span-3" class="mi">i</span><span id="MathJax-Span-4" class="mi">d</span><span id="MathJax-Span-5" class="mn">1</span><span id="MathJax-Span-6" class="mo">&gt;&lt;</span></span></span><span class="MJX_Assistive_MathML"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi>i</mi><mi>d</mi><mn>1</mn><mo>&gt;&lt;</mo></math></span></span> id2&gt; # 比较两次提交之间的差异

git diff &lt;branch1&gt;..&lt;branch2&gt; # 在两个分支之间比较

git diff --staged # 比较暂存区和版本库差异

git diff --cached # 比较暂存区和版本库差异

git diff --stat # 仅仅比较统计信息

**查看提交记录**

git log git log &lt;file&gt; # 查看该文件每次提交记录

git log -p &lt;file&gt; # 查看每次详细修改内容的diff

git log -p -2 # 查看最近两次详细修改内容的diff

git log --stat #查看提交统计信息

**tig**

Mac上可以使用tig代替diff和log，`brew install tig`

**Git 本地分支管理**

**查看、切换、创建和删除分支**

git br -r # 查看远程分支

git br &lt;new_branch&gt; # 创建新的分支

git br -v # 查看各个分支最后提交信息

git br --merged # 查看已经被合并到当前分支的分支

git br --no-merged # 查看尚未被合并到当前分支的分支

git co &lt;branch&gt; # 切换到某个分支

git co -b &lt;new_branch&gt; # 创建新的分支，并且切换过去

git co -b &lt;new_branch&gt; &lt;branch&gt; # 基于branch创建新的new_branch

git co $id # 把某次历史提交记录checkout出来，但无分支信息，切换到其他分支会自动删除

git co $id -b &lt;new_branch&gt; # 把某次历史提交记录checkout出来，创建成一个分支

git br -d &lt;branch&gt; # 删除某个分支

git br -D &lt;branch&gt; # 强制删除某个分支 (未被合并的分支被删除的时候需要强制)

** 分支合并和rebase**

git merge &lt;branch&gt; # 将branch分支合并到当前分支

git merge origin/master --no-ff # 不要Fast-Foward合并，这样可以生成merge提交

git rebase master &lt;branch&gt; # 将master rebase到branch，相当于： git co &lt;branch&gt; &amp;&amp; git rebase master &amp;&amp; git co master &amp;&amp; git merge &lt;branch&gt;

** Git补丁管理(方便在多台机器上开发同步时用)**

git diff &gt; ../sync.patch # 生成补丁

git apply ../sync.patch # 打补丁

git apply --check ../sync.patch #测试补丁能否成功

** Git暂存管理**

git stash # 暂存

git stash list # 列所有stash

git stash apply # 恢复暂存的内容

git stash drop # 删除暂存区

**Git远程分支管理**

git pull # 抓取远程仓库所有分支更新并合并到本地

git pull --no-ff # 抓取远程仓库所有分支更新并合并到本地，不要快进合并

git fetch origin # 抓取远程仓库更新

git merge origin/master # 将远程主分支合并到本地当前分支

git co --track origin/branch # 跟踪某个远程分支创建相应的本地分支

git co -b &lt;local_branch&gt; origin/&lt;remote_branch&gt; # 基于远程分支创建本地分支，功能同上

git push # push所有分支

git push origin master # 将本地主分支推到远程主分支

git push -u origin master # 将本地主分支推到远程(如无远程主分支则创建，用于初始化远程仓库)

git push origin &lt;local_branch&gt; # 创建远程分支， origin是远程仓库名

git push origin &lt;local_branch&gt;:&lt;remote_branch&gt; # 创建远程分支

git push origin :&lt;remote_branch&gt; #先删除本地分支(git br -d &lt;branch&gt;)，然后再push删除远程分支

**Git远程仓库管理**

_<span class="wp_keywordlink">[GitHub](http://blog.jobbole.com/6492/ "GitHub如何运作：时间并不决定一切")</span>_

git remote -v # 查看远程服务器地址和仓库名称

git remote show origin # 查看远程服务器仓库状态

git remote add origin git@ github:robbin/robbin_site.git # 添加远程仓库地址

git remote set-url origin git@ github.com:robbin/robbin_site.git # 设置远程仓库地址(用于修改远程仓库地址) git remote rm &lt;repository&gt; # 删除远程仓库

**创建远程仓库**

git clone --bare robbin_site robbin_site.git # 用带版本的项目创建纯版本仓库

scp -r my_project.git git@ git.csdn.net:~ # 将纯仓库上传到服务器上

mkdir robbin_site.git &amp;&amp; cd robbin_site.git &amp;&amp; git --bare init # 在服务器创建纯仓库

git remote add origin git@ github.com:robbin/robbin_site.git # 设置远程仓库地址

git push -u origin master # 客户端首次提交

git push -u origin develop # 首次将本地develop分支提交到远程develop分支，并且track

git remote set-head origin master # 设置远程仓库的HEAD指向master分支

也可以命令设置跟踪远程库和本地库

git branch --set-upstream master origin/master

git branch --set-upstream develop origin/develop

</div>
</div>