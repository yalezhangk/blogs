# 1、git merge 合并分支
1. **\<master\> $: git merge dev  ->  将dev分支合并到当前分支master上**

# 2、git rebase 变基
1. **\<dev\> $: git rebase master -> 将当前分支dev提交合并到master分支上**
2. git rebase 会将当前分支的提交复制到指定的分支之上。
3. 在我们正在 rebase 的提交上，我们可以执行以下 6 个动作：
- reword：修改提交信息；
- edit：修改此提交；
- squash：将提交融合到前一个提交中；
- fixup：将提交融合到前一个提交中，不保留该提交的日志消息；
- exec：在每个提交上运行我们想要 rebase 的命令；
- drop：移除该提交。

# 3、git reset 重置
1. git reset 能让我们不再使用当前台面上的文件，让我们可以控制 HEAD 应该指向的位置。

# 4、git fetch 取回代码、git pull 拉取并merge
1. git fetch 只是单纯地从远端仓库下载新的数据而已
2. git pull 实际上是两个命令合成了一个：git fetch 和 git merge

# 5、git reflog 日志信息
1. git reflog 展示已经执行过的所有动作的日志。包括合并、重置、还原，基本上包含你对你的分支所做的任何修改。

# 6、git commit 修改用户信息
1. git commit --amend --author="xxx <xxxx@xxx.xxx>"
2. --author='user_name <email>'

# 7、git diff
1. <hci>$ **git diff origin/master..HEAD**
2. **当前在hci分支，比较master到hci分支的区别变化**

# 8、git cherry-pick
1. 将一个分支的**commit变动应用于其他分支**。
2. 对于多分支的代码库，将代码从一个分支转移到另一个分支是常见需求。
3. 这时分两种情况。一种情况是，你需要另一个分支的所有代码变动，那么就采用合并（git merge）。
4. 另一种情况是，你只需要**部分代码变动（某几个提交），这时可以采用 Cherry pick。**

```bash
# 切换到 master 分支
$ git checkout master
# Cherry pick 操作(将其他分支的f_commit)应用到当前master分支
# -x: 在提交信息的末尾追加一行(cherry picked from commit ...)
$ git cherry-pick f_commit -x
# 转移多个提交
$ git cherry-pick commit_A commit_B
# 转移A到B之间的commit(左开右闭，不包含A)
$ git cherrry-pick A..B
# 转移A到B之间的commit(包含A)
$ git cherry-pick A^..B
```

# 9、git revert 撤销某次commit
1. git revert comm_id 撤销指定的版本，撤销也会作为一次提交进行保存。

# 10、git tag 打标签
1. git tag ls 列出所有tag
2. git branch luminous-12.2.10 v12.2.10 基于tag:v12.2.10切出分支v12.2.10
3. git ls | grep module.py 搜索module.py文件
