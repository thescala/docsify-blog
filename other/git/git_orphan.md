## 1. GIT 项目删除所有提交记录，还原一个干净的仓库

如果一个项目分支太多，提交记录太过杂乱，提交记录还有敏感信息，想要还原一个干净的仓库，该如何做呢？

此时就需要`git checkout --orphan`命令了

1. 首先查看仓库有哪些分支（本地和远程）

```sh
# 所有分支（包括本地和远程分支）
git branch -a
# 或者分别查看本地和远程分支
# 查看本地分支
git branch
# 查看远程分支
git branch -v
```

2. 保留最新的一个分支（不限于 master，想要保留的分支即可），删除本地其余分支和远程分支（远程分支保留 master 和 HEAD）

```sh
# 删除本地分支
git branch -D 本地分支名
# 删除远程分支
git push origin --delete 远程分支名称
```

3. 创建新的分支

```sh
git checkout --orphan <new_branch>
```

> Create a new orphan branch, named <new_branch>, started from <start_point> and switch to it. The first commit made on this new branch will have no parents and it will be the root of a new history totally disconnected from all the other branches and commits.

4. 然后像一个正常的分支一样进行添加 提交 推送(因为是首次，需要强制推送)等操作

```sh
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


## 2. 删除掉本地不存在的远程分支

多人合作开发时，如果远程的分支被其他开发删除掉，在本地执行 `git branch --all` 依然会显示该远程分支，可使用下列的命令进行删除：

```sh
# 使用 pull 命令，添加 -p 参数
$ git pull -p

# 等同于下面的命令
$ git fetch -p
$ git fetch --prune origin
```

## 3. 下载分支

```sh
# 初始化
git init
# 建立远程链接
git remote add origin https://gitXXX.com/XXX/test.git
# 拉取远程分支到本地,假设https://gitXXX.com/XXX/test.git有分支gh-pages
git fetch origin gh-pages
# 本地建立分支并切换到该分支，与远程分支建立链接
git checkout -b gh-pages origin/gh-pages
# 拉取分支内容到本地分支
git pull origin gh-pages
```