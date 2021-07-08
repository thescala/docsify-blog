## Docker 常用用法

### 容器生命周期管理

- [run](https://www.runoob.com/docekr/docker-run-command.html)
- [start/stop/restart](https://www.runoob.com/docekr/docker-start-stop-restart-command.html)
- [kill](https://www.runoob.com/docekr/docker-kill-command.html)
- [rm](https://www.runoob.com/docekr/docker-rm-command.html)
- [pause/unpause](https://www.runoob.com/docekr/docker-pause-unpause-command.html)
- [create](https://www.runoob.com/docekr/docker-create-command.html)
- [exec](https://www.runoob.com/docekr/docker-exec-command.html)

### 容器操作

- [ps](https://www.runoob.com/docekr/docker-ps-command.html)
- [inspect](https://www.runoob.com/docekr/docker-inspect-command.html)
- [top](https://www.runoob.com/docekr/docker-top-command.html)
- [attach](https://www.runoob.com/docekr/docker-attach-command.html)
- [events](https://www.runoob.com/docekr/docker-events-command.html)
- [logs](https://www.runoob.com/docekr/docker-logs-command.html)
- [wait](https://www.runoob.com/docekr/docker-wait-command.html)
- [export](https://www.runoob.com/docekr/docker-export-command.html)
- [port](https://www.runoob.com/docekr/docker-port-command.html)

### 容器 rootfs 命令

- [commit](https://www.runoob.com/docekr/docker-commit-command.html)
- [cp](https://www.runoob.com/docekr/docker-cp-command.html)
- [diff](https://www.runoob.com/docekr/docker-diff-command.html)

### 镜像仓库

- [login](https://www.runoob.com/docekr/docker-login-command.html)
- [pull](https://www.runoob.com/docekr/docker-pull-command.html)
- [push](https://www.runoob.com/docekr/docker-push-command.html)
- [search](https://www.runoob.com/docekr/docker-search-command.html)

### 本地镜像管理

- [images](https://www.runoob.com/docekr/docker-images-command.html)
- [rmi](https://www.runoob.com/docekr/docker-rmi-command.html)
- [tag](https://www.runoob.com/docekr/docker-tag-command.html)
- [build](https://www.runoob.com/docekr/docker-build-command.html)
- [history](https://www.runoob.com/docekr/docker-history-command.html)
- [save](https://www.runoob.com/docekr/docker-save-command.html)
- [load](https://www.runoob.com/docekr/docker-load-command.html)
- [import](https://www.runoob.com/docekr/docker-import-command.html)

### info|version

- [info](https://www.runoob.com/docekr/docker-info-command.html)
- [version](https://www.runoob.com/docekr/docker-version-command.html)

补充：

1、docker 忘记添加重启参数？

`docker container update --restart=always <container_name>`

或者

直接修改容器配置：
停止容器，根据`/var/lib/docker/containers/<container-id>`查找该容器的 hostconfig.json 配置文件
直接修改该配置中 RestartPolicy 的参数
`"RestartPolicy":{"Name":"no","MaximumRetryCount":0}`
修改为
`"RestartPolicy":{"Name":"always","MaximumRetryCount":0}`
