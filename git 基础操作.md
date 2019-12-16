## git merge

是将指定分支合并到当前分支

```git
// 基础操作
git merge <branch>
```

### 基础

**git merge** 默认采用快进（fast-forward）的方式。**如果 merge 的时候没有冲突，没有新的 commit 节点产生，只是移动分支的指针来实现 merge**

### 无节点 merge

> 由于多分支的开发，会导致 merge 节点错综复杂，所以需要无节点merge，即 commit 是一条线，视觉清晰，可读性强。

实现无节点 merge，其实就是使用 merge 的默认特性快进（fast-forward），因为快进不会产生 commit 节点，只会移动分支的 HEAD 指针。

问题是：快进的特性是在没有冲突的情况下产生的，而执行 merge 大概率会产生冲突，这时候就需要使用 **git rebase**

### 有节点 merge

如果 merge 有冲突，解决完冲突后，add 冲突的文件再 commit，就会产生一个 merge 节点。

如果需要强制产生 merge 节点，使用 **--no-ff** 参数强制产生 merge 节点。

no-ff: no-fast-forward

```git
git merge --no-ff -m <merge 节点注释> <branch>
// 产生一个 <merge 节点注释> 的commit节点
```

### 单节点 merge

> 需要将多个 merge 压缩成一个 commit 节点

使用压缩参数：squash，作用是将被 merge 的节点压缩在一起，在 master 分支上只显示一个 commit 节点

```git
git merge --squash <branch>
git commit -m <merge 节点注释>
// 只产生一个 <merge> 的 commit 节点
```

![Git merge 各个参数](static/merge.webp)

## git rebase

变基（改变基线）：变基的作用就是修整历史，将分支历史并入主线。

注意：不要通过rebase对任何已经提交到公共仓库中的commit进行修改。rebase 要现在 dev 分支上合并，然后切换到主分支，在 merge dev 分支

```
// 之前
m0 -- m1 -- m2 -- m3    master
      |
      b1 -- b2 -- b3    dev

// 之后
m0 -- m1 -- m2 -- m3 -- d
```

```git
git co dev
git rebase -i master
```

原理：

1. 首先找到两个分支 dev 和 master 的最近共同祖先 m1
2. 对比当前 dev 分支相比 m1 的历次提交，提取修改，保存为临时文件
3. 将分支指向 master 的最新 m3
4. 依次应用修改

其中 “依次应用修改” 指的是：

**-i** 参数打开交互界面，在 vim 编辑器中进行操作

可以编辑对 dev 上这几个 commit 的处理，如输入 pick 为保留，输入 squash 则将该 commit 内容并入上一个 commit 等。在完成操作选择后（这里我们可以选择 fixup d1 和 d2，并 reword d3），输入 :wq 保存退出，会进入一个新的 vim 窗口，在此你可以进一步编辑新的 commit message，保存后 rebase 即可生效。

## branch

分支命令

```
git branch <新分支名> // 创建新分支
git branch // 列出本地以存在的分支
git branch -r // 列出远端分支
git branch -a // 列出远端和本地分支
```

## checkout

切换分支

```
git checkout <branch> // 切换分支
git checkout -b <branch>  // 切换并创建分支
git checkout <filepath> // 撤回已保存的文件， . 表示都不保存
```

## fetch

git fetch 是将远程主机的最新内容拉到本地，用户再去确认是否合并到分支当中。

```git
git fetch <远程主机名> <branch> // 拉取远程主机的更新
```

取回更新后，会返回一个 **FETCH_HEAD**，指的是某个 branch 在服务器上的最新状态

## pull

拉取服务器更新并合并，是 fetch + merge 的简写

```git
git fetch <远程主机名> <branch> // 拉取最近更新
git merge FETCH_HEAD  // 合并当前内容到当前内容中
```

## reset

撤回代码和合并

```
index：索引 暂存区
working：工作区

--soft 回退并保留被标记为 add 的状态(git status 是绿色的状态)
--mixed 默认参数，重置索引，但不重置工作树，更改后的文件被标记为未提交
--hard 重置索引和工作树，并且中间所有的已提交、未提交的代码被抛弃
--merge 和 --hard 类似，保留工作区和索引之间的差异
--keep 和 --hard 类似，保留工作区和 HEAD 之间的差异
```

## revert

还原一个提交，并且产生一个新提交来记录本次还原。