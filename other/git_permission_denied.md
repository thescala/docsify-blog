# Git 拉取、推送等出现 remote: User permission denied 解决

默认情况下，git 使用 https 推送等操作时会要求输入账号密码并保存到 windows 凭据下。

查看配置信息：

```git
$ git config --global -l
```

可以看到类似信息：

```sh
core.symlinks=false
core.autocrlf=input
core.fscache=true
color.diff=auto
color.status=auto
......
credential.helper=manager
```

重点是`credential.helper=manager`

git 提示`remote: User Permission denied`,查看 windows 普通凭证下对应账号删除或修改，再次进行拉取或提交等操作时，重新输入账号密码。
如果还不行，则进行如下操作：
删除凭证管理

```git
$ git config --global --unset credential.helper
# 或者
$ git config --system --unset credential.helper
```

然后重新设置凭证

```git
$ git config --global credential.helper manager
```
