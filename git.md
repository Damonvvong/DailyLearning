# git 相关

## git rebase
#### git rebase 是什么

![](media/14993525683619.jpg)


通常情况下会采用 `git merge master feature` 命令来合并上图所示的两个分支。过程如下图。

![](media/14993525286763.jpg)

那 `git rebase` 呢 ? 如下所示：


```
git checkout feature
git rebase master
```

![](media/14993525899117.jpg)

和 `git merge` 不同的是，`git rebase` 并没有把两个分支的最后一个**commit**合并再提交一个**merge commit**。而是将新增的几个**commit**直接添加到需要合并的分支的最后。
最后如果执行 `git merge master feature`，那么master的指针会指到feature指针所指的地方，这称为**fast-forward（快进）合并**，这个merge没有新的merge commit 也没有多余的 branch line.

#### git rebase 还能干吗

**交互式操作**

rebase 支持交互式操作，允许你更改并入新分支的 commit。

- 把 `-i` 传入 `git rebase` 选项来开始一个交互式的rebase过程

```git
git checkout feature
git rebase -i master
```

- 它会打开一个文本编辑器，显示所有将被移动的提交

```vim
pick 33d5b7a Message for commit #1
pick 9480b3d Message for commit #2
pick 5c67e61 Message for commit #3
```

- 根据提示修改操作，比如：

```vim
pick 33d5b7a Message for commit #1
fixup 9480b3d Message for commit #2
pick 5c67e61 Message for commit #3
```
- 最后效果如下

![](media/14993529981747.jpg)


**多分支操作**

多分支操作使用的是 `--onto`命令，示例如下
Take the client branch, figure out the patches since it diverged from the server branch, and replay these patches in the client branch as if it was based directly off the master branch instead
关于 `git rebase --onto master server client`
![](media/14993531300895.jpg)
以上命令的意思是：“1.取出 client 分支 2.找出 server 和 client两分支的父节点 3.把从刚才找出的节点开始，client所有的修改在 master 分支上重放一遍“，这理解起来有一点复杂，不过效果非常酷。结果如下：
![](media/14993531389197.jpg)



#### git rebase 要注意什么

- [不要对在你的仓库外有副本的分支执行 `git rebase`](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)

总的原则是，只对尚未推送或分享给别人的本地修改执行变基操作清理历史，从不对已推送至别处的提交执行变基操作，这样，你才能享受到两种方式带来的便利。


#### git rebase vs. git merge

有一种观点认为，仓库的提交历史即是 记录实际发生过什么。 它是针对历史的文档，本身就有价值，不能乱改。 从这个角度看来，改变提交历史是一种亵渎，你使用_谎言_掩盖了实际发生过的事情。 如果由合并产生的提交历史是一团糟怎么办？ 既然事实就是如此，那么这些痕迹就应该被保留下来，让后人能够查阅。

另一种观点则正好相反，他们认为提交历史是 项目过程中发生的事。 没人会出版一本书的第一版草稿，软件维护手册也是需要反复修订才能方便使用。 持这一观点的人会使用 rebase 及 filter-branch 等工具来编写故事，怎么方便后来的读者就怎么写。

#### 总结

`git rebase`像是一把双刃剑，用的好可以使得项目的commit非常整洁，而且没有太多的branch line。但是一旦错误使用，其实是让操作更加复杂。所以，对于不太了解 `git rebase` 的时候需要谨慎使用。

如果你还没有完全熟悉 git rebase，你还可以在一个临时分支中执行 rebase。这样的话，如果你意外地弄乱了你 feature 分支的历史，你还可以查看原来的分支然后重试。

```git
git checkout feature
git checkout -b temporary-branch
git rebase -i master
# [清理目录]
git checkout master
git merge temporary-branch
```

#### 参考
[merging-vs-rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)
[rebasing](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)

---- 

Tips：你在写代码时不用担心造成孤立的commit —— 因为你后面一定能修复。

