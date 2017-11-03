---
title: git学习(3):分支和标签管理
date: 2017-01-30 16:58:11
tags: git
categories: git
---

### 分支管理
用了git的分支之后，只想狠狠地给他打个call，和svn的分支比起来，git简直快了指数呗。说起来，svn的分支功能，我基本没有使用过，因为实在是不太方便。
分支就像是两个平行宇宙，我在这个分支进行修改，并不会影响到另外一个分支。根据需要的话，修改完毕之后，可以将两个分支合并。分支在实际使用中具体表现在，如果我在开发一个新功能，只开发到一半。这个时候同事也想用这个代码库开发别的功能，但是开发到一半的代码库无疑会影响到新的开发。这个时候，如果你新建了一个分支，在自己的分支上做开发，别人就可以在原来的分支上开发，这样互不影响。都到都开发完毕了，再把两个分支合并，岂不美哉。
#### 创建与合并分支
通俗来讲，分支就像一个平行宇宙，每个平行宇宙都有一条时间线，我们每次`commit`就是串起了一条时间线。目前为止，我们只有一条时间线，即`master`分支。严格来讲，`HEAD`并不是指向提交，而是指向分支`master`，然后`master`才是指向提交。所以,`HEAD`指向的是当前分支。
我们可以这样理解，新建了一个分支，其实就是在`master`这条时间线的某个时间节点分出了两条平行的时间线，一条还是按`master`原有的轨迹行走，另一条就按另一个方向前进。我们将这个时间线，也就是分支命名为`dev`。
等到`dev`上的修改全部完成了，我们可以把`dev`这个分支和`master`分支合并。最简单的情况是这样的，如果`master`这个分支没有进行任何修改，那么我们可以直接将`master`指向`dev`这个分支。然后，我们就可以把`dev`这个分支删除了，其实也就是把`dev`的指针给删除掉。
接下来是实际操作：
我们要先创建一个分支，然后用`git checkout <name>`切换到这个分支，如果同时加上`-b`这个参数，就是创建的同时并切换分支：
```bash
git checkout -b dev
```
相当于相面两句代码：
```bash
git branch dev
git checkout dev
```
同时，我们可以用`git branch`查看当前分支。
等到`dev`分支的修改都完成后，我们需要把`dev`上的东西合并到`master`。首先用`git checkout master`切换到`master`分支，然后输入：
```bash
git merge dev
```
`git merge`命令用于合并指定分支到当前分支。因为`master`没有修改，即是`Fast-forward`（快进模式）。直接把`master`指向`dev`的当前提交，所以速度非常地快。
合并完成以后，就可以删除`dev`分支了：
```bash
git branch -d dev
```
如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。
#### 解决冲突
但是现实使用中，`master`没有进行修改的情况是非常少见的。一般都是`dev`分支修改的同时，`master`分支也被修改了。这种情况下，我们就无法执行“快速合并”操作。如果我们在`master`分支运行`git merge dev`命令，git就会提示我们发生冲突，合并失败。
为了解决这种问题，我们必须先手动解决冲突后，再进行合并。我们使用`git status`可以查看冲突的文件，
在这条命令下，我们是可以看到冲突文件的内容的。Git用"<<<<<<<"，"======="，">>>>>>>"标记出不同分支的内容。我们手动修改工作区的冲突文件后，再合并，就成功了。
这时候，我们使用带参数的`git log`命令可以查看分支情况：
```bash
git log --graph --pretty=oneline --abbrev-commit
```
最后，`git branch -d dev`删除`dev`分支。
#### 分支管理策略
通常情况下，git会尽可能地使用`Fast forward`模式，但是这样操作的话，在删除分支之后，分支的信息就会丢失。如果我们想在分支删除以后还保留分支信息的话，可以加上`--no-ff`参数来强制禁用`Fast forward`模式。一般来说，能使用快进模式来合并分支的话，都是`master`没有更改的情况，所以接下来合并命令的前提是`master`分支没有更改，我们要把更改了的`dev`分支合并到`master`上：
```bash
git merge --no-ff -m "merge with no-ff" dev
```
参数`-m`后面跟的是这次合并的注释。
在实际开发中，我们要遵循以下几个原则：
1.`master`分支是一个非常稳定的分支，我们平时不应该在上面直接做修改。
2.平时我们应该都在`dev`分支上面做修改，比如我们要发布一个1.0新版本，应该在`dev`分支上完成1.0后，再合并到`master`分支。
3.每个小伙伴应该都有自己的`dev`分支，不要混用。
#### bug分支
很多情况下，我们的生产环境也就是`master`分支出现了bug，理论上，我们应该要新建一个`bug1`分支来修改bug，等到bug修复完成后，再把`bug1`合并到`master`分支。但是这个时候，我们手头的`dev`分支还在工作，而且只进行到一半。如果把工作到一半的`dev`分支合并到`master`，这当然是不可取的。写到一半的代码势必会影响到`master`的正常工作，这可如何是好。
不必惊慌，这个一个非常常见的情况，git也给了相应的处理措施。我们可以使用`git stash`命令来把工作现场给储存起来，等以后恢复工作现场继续工作。在使用了`git stash`后，再用`git status`来查看工作区，会发现工作区是干净的，这说明，`dev`分支被保存起来了。
接下来我们要修改`master`分支上的bug，我们需要先切换到`master`分支上，然后新建一个`bug1`分支：
```bash
git checkout master
git checkout -b bug1
```
修复成功后，再切换到`master`分支，将`bug1`分支合并到`master`分支，然后删除：
```bash
git checkout master
git merge --no-ff -m "mix bug1" bug1
git branch -d bug1
```
这样，bug就修复完毕了，我们要接着干活，所以我们想要恢复刚刚的`dev`工作现场,我们先切换回`dev`，然后用`git stash list`命令查看所有工作现场：
```bash
git checkout dev
git stash list
```
恢复`stash`的命令有两种，一种是`git stash apply`，这种恢复后`stash`不会删除。要删除的话，可以使用`git stash pop`恢复。
如果我们之前进行了多次`stash`保存操作，恢复的时候可以用`git stash list`查看，然后恢复指定`stash`:
```bash
git stash apply stash@{0}
```
#### 多人协助
在进行多人共同开发一个项目的时候，不可避免要使用到远程仓库了。当我们从远程仓库克隆的时候，其实git已经把我们本地工作目录的`master`和远程`master`关联起来了，而且远程仓库的默认名称是`origin`，我们可以使用`git remote`来查看远程仓库的信息，加上`-v`参数可以让信息更加详细。
要推送分支到远程的命令是：
```bash
git push origin 分支名
```
当我们从远程仓库`git clone git@github.com:用户名/仓库名.git`克隆时，默认情况下，我们只能看到`master`分支，别的分支是么有的。如果我们要在本地的`dev`上开发，在本地创建和远程分支对应的分支:
```bash
git checkout -b dev origin/dev
```
这个时候，我们就可以在本地修改`dev`分支，然后快乐地推送到远程了。
但是我们有一个很烦人的小伙伴，我们叫他小辉，小辉现在也在本地修改`dev`分支，然后也推送了版本到远程仓库。
如果我们恰好修改了同一个文件，我在小辉之后推送的话，就会报错，那是因为我们文件内容冲突了。解决这个方法其实也很简单，我们可以使用`git pull`命令把最新的提交从`origin/dev`上抓下来，解决冲突再合并就OK了。可是我们再次`git pull`的时候还是报错了，提示`no tracking information`,这是因为本地的`dev`没有关联到远程，使用命令：
```bash
git branch --set-upstream dev origin/dev
```
### 标签管理
#### 创建标签
每个标签`tags`都是和一个`commit`绑定在一起的，他的优势就是，容易记住。因为`commit`都是一大串数字，而`tags`是可以自定义的，所以就很...ok。
创建标签非常简单，我们需要切换到要打标签的分支,然后是用`git tag <name>`就可以打一个标签。默认标签是打在刚提交的`commit`上的。但是小辉非要给以前的`commit`打标签，好吧，这也是可以的。我们只要使用命令找到历史提交的`commit id`就可以了：
```bash
 git log --pretty=oneline --abbrev-commit
 git tag <name> commitid
```
 我们可以使用`git tag`查看标签,使用`git show <tagname>`查看标签信息。还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：
```bash
git tag -a <name> -m "tagv1.0" commitid
```
还可以通过-s用私钥签名一个标签：
```bash
git tag -s v2.0 -m "tagv2.0" commitid
```
#### 操作标签
如果标签打错了，我们也可以删除：
```bash
git tag -d <tagname>
```
这些操作都是在本地的，所有不用担心影响到远程仓库，如果我们想把标签推送到远程仓库，可以推送一个本地标签：
```
git push origin <tagname>
```
也可以一次性推送所有未推送标签:
```
git push origin --tags
```
如果推送到远程以后，发现标签错了，想要删除，也可以：
```bash
git tag -d <tagname>
git push origin:refs/tags/<tagname>
```
成功删除。