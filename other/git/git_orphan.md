# GIT 项目删除所有提交记录，还原一个干净的仓库

如果一个项目分支太多，提交记录太过杂乱，提交记录还有敏感信息，想要还原一个干净的仓库，该如何做呢？

此时就需要`git checkout --orphan`命令了

1. 首先查看仓库有哪些分支（本地和远程）

```git
# 所有分支（包括本地和远程分支）
git branch -a
# 或者分别查看本地和远程分支
# 查看本地分支
git branch
# 查看远程分支
git branch -v
```

2. 保留最新的一个分支（不限于 master，想要保留的分支即可），删除本地其余分支和远程分支（远程分支保留 master 和 HEAD）

```git
# 删除本地分支
git branch -D 本地分支名
# 删除远程分支
git push origin --delete 远程分支名称
```

3. 创建新的分支

```git
git checkout --orphan <new_branch>
```

> Create a new orphan branch, named <new_branch>, started from <start_point> and switch to it. The first commit made on this new branch will have no parents and it will be the root of a new history totally disconnected from all the other branches and commits.

4. 然后像一个正常的分支一样进行添加 提交 推送(因为是首次，需要强制推送)等操作

```git
# 将文件添加到新的分支<new_branch>上
git add -A
git commit -am "commit message"
# 删除本地分支
git branch -D 本地分支(步骤2中保留的分支名称)
# 将新创建的分支<new_branch>改成master
git branch -m master
# 推送到远程仓库
git push origin -f master
```

参考: 本地 git 安装目录文件(/Git/mingw64/share/doc/git-doc/git-checkout.html)
