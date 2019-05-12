[TOC]
# Pro Git
Author：fucf

## 第一章 起步


## 第二章 Git基础

### 记录每次更新到仓库
工作目录下文件的两种状态：
* 已跟踪（文件由git管理）
* 未跟踪（文件不由git管理）
* note：初次克隆某个仓库时，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。

Git文件的生命周期：
![Git文件的生命周期](https://it-learn.oss-cn-beijing.aliyuncs.com/github/reading-notes/15545194798768.jpg)

* 检查当前文件状态：`git status`
* 跟踪新文件：`git add`
* 状态简览：`git status -s`或`git status --short`
* `git status`也可以用`git st`替代，但是作用效果验证和`git status -s`一致（note by Sam）
* 新添加的未跟踪文件前面有`??`标记
* 新添加到暂存区中的文件前面有`A`标记。
* 修改过的文件前面有`M`标记。
* `M`有两个可以出现的位置，出现在右边的`M`表示该文件被修改了但是还没放入暂存区，出现在靠左边的`M`表示该文件被修改了并放入了暂存区。


#### 查看已暂存和未暂存的修改 git diff
以文件补丁的格式显示哪些更新还没有暂存？有哪些更新已经暂存起来准备好了下次提交？
* 查看尚未暂存的文件改动：`git diff`(note by sam:查看的是已被Git跟踪，但是尚未暂存的文件更新记录)
* 查看已暂存的将要添加到下次提交里的内容：`git diff -cached` 或者`git diff --staged` (note by Sam:查看的与上次commit相比，这次暂存的所有文件的改动)
#### 提交更新 git commit

```
git commit -m ''
git commit -am ''
git commit --amend
```
#### 移除文件 git rm
要从Git中移除某个文件，就必须要从已跟踪文件清单中移除（确切的说，是从暂存区域移除），然后提交。可以用`git rm`命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。 
1. 该文件未被git跟踪：

```
rm README
```
2. 该文件已经修改过并且一定加入到暂存区。如果我们想同时从工作目录和暂存区删除该文件，则执行命令：

```
git rm -f README
```  
3. 我们想把文件从Git仓库中删除（亦即从暂存区域移除），但仍希望保留在当前工作目录中。可以使用`--cached`选项：

```
git rm --cached README
```

#### 移动文件 git mv

```
$ git mv file_from file_to
```
运行`git mv`相当于运行了下面三条命令：

```
$ mv file_from file_to
$ git rm file_to
$ git add file_to
```

### 查看提交历史 git log
常用选项：
* -p：显示每次提交的内容差异
* -n：显示最近的n次提交
* --stat:显示每次提交的简略的统计信息
* --pretty：

#### 撤销操作
```
git commit --amend
```
这个命令会将暂存区中的文件提交。如果自上次提交以来你还未做任何修改（例如，在上次提交后马上执行了此命令），那么快照会保持不变，而你所修改的只是提交信息。
最终你只会有一个提交，第二次提交将会替代第一次提交的结果。

#### 取消暂存的文件




## 第三章 Git分支
 
### 3.3 Git分支-分支管理
**git branch**: 得到当前所有分支的一个列表。其中的`*`代表现在检出的那个分支（也就是说，当前`HEAD`指针所指向的分支）。
 
```
$ git branch
issue53
*master
testing
```

**git branch -v**:查看每一个分支的最后一次提交。

```
$ git branch -v
  iss53   93b412c fix javascript issue
* master  7a98805 Merge branch 'iss53'
  testing 782fd34 add scott to the author list in the readmes
```
**git branch --merged**: 显示已经合并到当前分支的分支。

```
## 假设之前已经合并过issue分支，没有合并过testing分支
$ git branch --merged
  iss53
* master
```
**git branch --unmerged**: 显示没有合并到当前分支的分支

```
$ git branch --no-merged
  testing
```

### 3.4 Git-分支开发工作流


### 3.5 Git-远程分支

#### 远程分支
远程跟踪分支是远程分支状态的引用。 它们是你不能移动的本地引用，当你做任何网络通信操作时，它们会自动移动。 远程跟踪分支像是你上次连接到远程仓库时，那些分支所处状态的书签。

假设你的网络里有一个在 git.ourcompany.com 的 Git 服务器。 如果你从这里克隆，Git 的 clone 命令会为你自动将其命名为 origin，拉取它的所有数据，创建一个指向它的 master 分支的指针，并且在本地将其命名为 origin/master。 Git 也会给你一个与 origin 的 master 分支在指向同一个地方的本地 master 分支，这样你就有工作的基础。

“origin” 并无特殊含义
远程仓库名字 “origin” 与分支名字 “master” 一样，在 Git 中并没有任何特别的含义一样。 同时 “master” 是当你运行 git init 时默认的起始分支名字，原因仅仅是它的广泛使用，“origin” 是当你运行 git clone 时默认的远程仓库名字。 如果你运行

```
 git clone -o booyah
```
 
那么你默认的远程分支名字将会是 booyah/master。

#### 推送
#### 跟踪分支
跟踪分支是与远程分支有直接关系的本地分支。从一个远程跟踪分支检出一个本地分支会自动创建所谓的 “跟踪分支”（它跟踪的分支叫做 “上游分支”）。 

设置已有的本地分支跟踪一个刚刚拉取下来的远程分支，或者想要修改正在跟踪的上游分支，你可以在任意时间使用 `-u` 或` --set-upstream-to` 选项运行 git branch 来显式地设置。

```
$ git branch -u origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
```

**git branch --vv：**将所有的本地分支列出来并且包含更多的信息，如每一个分支正在跟踪哪个远程分支与本地分支是否是领先、落后或是都有。

```
$ git branch -vv
  iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
  master    1ae2a45 [origin/master] deploying index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
  testing   5ea463a trying something new
```

需要重点注意的一点是这些数字的值来自于你从每个服务器上最后一次抓取的数据。 这个命令并没有连接服务器，它只会告诉你关于本地缓存的服务器数据。 如果想要统计最新的领先与落后数字，需要在运行此命令前抓取所有的远程仓库。 可以像这样做：
```
$ git fetch --all; 
git branch -vv
```

#### 拉取
`git fetch`命令从服务器上抓取本地没有的数据时，它并不会修改工作目录中的内容。它只会获取数据然后让你自己合并。

`git pull` = `git fetch` + `git merge`.

由于 git pull 的魔法经常令人困惑所以通常单独显式地使用 fetch 与 merge 命令会更好一些。

#### 删除远程分支
```
$ git push origin --delete serverfix
To https://github.com/schacon/simplegit
 - [deleted]         serverfix
```

## 3.6 变基
#### git rebase [basebranch] [topicbranch] 
![](https://it-learn.oss-cn-beijing.aliyuncs.com/github/reading-notes/basic-rebase-1.png)
合并分支的两种做法：
* git merge:将两个分支的最新快照（C3和C4）以及二者最近的共同祖先（C2）进行合并，合并的结果是生成一个新的快照（并提交）。

![](https://it-learn.oss-cn-beijing.aliyuncs.com/github/reading-notes/basic-rebase-2.png)

* git rebase：提取在 C4 中引入的补丁和修改，然后在 C3 的基础上应用一次。 
![](https://it-learn.oss-cn-beijing.aliyuncs.com/github/reading-notes/basic-rebase-3.png)

在 Git 中，这种操作就叫做 变基。 你可以使用 rebase 命令将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样。

运行如下代码，他的原理为：
1. 首先找到这两个分支（即当前分支 experiment、变基操作的目标基底分支 master）的最近共同祖先 C2
2. 对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件
3. 将当前分支指向目标基底 C3, 最后以此将之前另存为临时文件的修改依序应用。（译注：写明了 commit id，以便理解，下同）

```
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```

现在回到master分支，进行一次快进合并：

```
$ git checkout master
$ git merge experiment
```

![](https://it-learn.oss-cn-beijing.aliyuncs.com/github/reading-notes/basic-rebase-4.png)

#### git rebase --onto
![](https://it-learn.oss-cn-beijing.aliyuncs.com/github/reading-notes/interesting-rebase-1.png)

假设你希望将 client 中的修改合并到主分支并发布，但暂时并不想合并 server 中的修改，因为它们还需要经过更全面的测试。 这时，你就可以使用 git rebase 命令的 --onto 选项，选中在 client 分支里但不在 server 分支里的修改（即 C8 和 C9），将它们在 master 分支上重放：

```
$ git rebase --onto master server client
```
以上命令的意思是：“取出 client 分支，找出处于 client 分支和 server 分支的共同祖先之后的修改，然后把它们在 master 分支上重放一遍”。 
![](https://it-learn.oss-cn-beijing.aliyuncs.com/github/reading-notes/interesting-rebase-2.png)

现在可以快进合并 master 分支了。（如图 快进合并 master 分支，使之包含来自 client 分支的修改）：

```
$ git checkout master
$ git merge client
```
![](https://it-learn.oss-cn-beijing.aliyuncs.com/github/reading-notes/interesting-rebase-3.png)

接下来你决定将server分支中的修改也整合进来。使用`git rebase [baseBranch] [topicBranch]`命令可以直接将特性分支（即本例中的server）变基到目标分支（即master）上。这样做能省去你先切换到 server 分支，再对其执行变基命令的多个步骤。
```
git rebase master server 
```
如图 将 server 中的修改变基到 master 上 所示，server 中的代码被“续”到了 master 后面。
![](https://it-learn.oss-cn-beijing.aliyuncs.com/github/reading-notes/interesting-rebase-4.png)

然后就可以快进合并主分支master了。
```
$ git checkout master
$ git merge server
```
