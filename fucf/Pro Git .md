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

