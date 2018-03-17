title: npm 私有仓库搭建
date: 2016-07-26 13:38:31
permalink: build-private-npm-repository-server
tags:
---

## 为什么搭建私有的 npm 仓库
* 发布和管理公司内部的私有的、暂不公开的 npm 包
* 加速 npm 包的下载，npm 官方仓库在国内太慢了

## 了解 sinopia
[https://github.com/rlidwka/sinopia](https://github.com/rlidwka/sinopia)
sinopia 是一个用于搭建私有的 npm 仓库的软件，我们将借助它来搭建 npm 私有仓库。

sinopia 安装和使用都极其简单，它还有以下的特性：

* 支持安装 npm 包（npm install, npm upgrade, etc.）
* 支持发布 npm 包（npm publish）
* 支持 npm unpublish
* 即将支持 npm tag
* 不支持 npm deprecate
* 支持 npm adduser
* 支持在浏览器端搜索，不支持命令行搜索
* 不支持 npm star, npm unstar，因为这对私有 npm 包没有意义
* sinopia 没有使用数据库来存储，它使用本地的文件系统来存储

## 使用 sinopia
### 快速开始

```shell
# 安装和使用
$ npm install -g sinopia
$ sinopia

# npm 配置，使用 sinopia 搭建的 npm 仓库来下载 npm 包
$ npm set registry http://localhost:4873/
```

sinopia 界面
![sinopia](http://img.l.jifangcheng.com/sinopia-1.png)

<!-- more -->

服务器搭建完成之后，就可以使用 `http://localhost:4873/` 作为仓库地址来下载和发布 npm 包了。

当你使用 `npm install xx` 后，sinopia 会检查本地有没有 xx 这个包，如果有，直接使用本地的缓存；如果没有，它会从 npm 官方仓库下载这个包，并将这个包缓存在本地以便二次下载。sinopia 并不会同步 npm 官方仓库的所有内容，它只会缓存你使用 `npm install ` 下载过的内容。

如果要发布私有包，使用 `npm publish --registry=http://localhost:4873/` 就可以了，发布好了你就可以在 web 端界面上看到它了。

### 配置
添加一个新用户：`npm adduser --registry http://localhost:4873/`，接下来输入用户名和密码就可以了，用户可以用来做权限管理

配置文件详解

```yaml
#
# 你可以在这里找到更多配置示例：
# https://github.com/rlidwka/sinopia/tree/master/conf
#

# 存储 npm 包的位置
storage: ./storage

# 认证
# max_users 设置成 -1 拒绝使用 npm adduser 添加用户，不过在公司内部我们可以使用 htpasswd 这个文件来初始化用户
# 具体的会在下面说到
auth:
  htpasswd:
    file: ./htpasswd
    max_users: -1

# 配置上游的 npm 服务器，主要是用于当仓库中不存在请求的那个包时去上游服务器拉取
uplinks:
  npmjs:
    url: https://registry.npmjs.org/

packages:
  # 正则表达式匹配包来控制相应的权限
  # access 表示哪一类用户可以对匹配的项目进行安装(install)
  # publish 表示哪一类用户可以对匹配的项目进行发布(publish)
  # proxy 当本地没有包的时候使用的代理，对应上面的 uplinks
  # 一共有以下几种权限：
  # $all 所有人都可以
  # $anonymous 只有匿名者可以进行对应操作（通常无用）
  # $authenticated 只有经过认证的可以进行操作（私有的一定要用这个）

  '@*/*':
    access: $all
    publish: $authenticated

  '*':
    access: $all
    publish: $authenticated
    proxy: npmjs

# 日志的配置
logs:
  - {type: stdout, format: pretty, level: http}
  #- {type: file, path: sinopia.log, level: info}
```

关于 auth 的配置，为了达到私有的目的，我们把 max_users 设置成 -1 来拒绝添加用户，不过在公司内部我们依然可以使用 htpasswd 这个文件来初始化用户，其格式如下，每一行一个用户：

``` shell
username:{SHA}经过加密的后密码值:autocreated 2016-07-25T08:55:48.859Z（创建时间）
```

加密算法：将密码使用 SHA1 算法哈希之后再使用 base64 转换再输出就可以了。
使用 python 实现的密码加密示例，下面是密码 `admin` 加密后的值

```python
>>> base64.b64encode(hashlib.sha1('admin').digest())
'0DPiKuNIrrVmD8IUCuw1hQxNqZc='
```

配置完成后重启一下 sinopia 服务器就可以了。

更多配置可以去 [https://github.com/rlidwka/sinopia/blob/master/conf/full.yaml](https://github.com/rlidwka/sinopia/blob/master/conf/full.yaml) 看看。

### 使用 Docker 来搭建 sinopia
sinopia 提供了一个 Docker 的镜像 [https://registry.hub.docker.com/u/keyvanfatehi/sinopia/](https://registry.hub.docker.com/u/keyvanfatehi/sinopia/)

我们可以使用这个镜像来快速搭建一个 sinopia

#### 快速开始

* 安装镜像 `docker pull keyvanfatehi/sinopia:latest`
* 创建容器 `docker run --name sinopia -d -p 4873:4873 keyvanfatehi/sinopia:latest`
* 现在就算搭建完成了，你可以访问 `http://<docker_host>:4873/` 来查看效果了

更详细的文档可以去 [https://github.com/kfatehi/docker-sinopia](https://github.com/kfatehi/docker-sinopia) 看看

#### 使用 docker-compose
通过配置 docker-compose 我们可以更优雅地搭建 sinopia，下面是一个示例：

```yaml
version: '2'
services:
  sinopia:
    image: keyvanfatehi/sinopia
    ports:
      - "4873:4873"
    # 启动的时候执行的命令（启动 sinopia）
    command: /opt/sinopia/start.sh

    # 我们在宿主机和 docker 里面同步了 config.yaml 和 htpasswd 这两个文件
    # 并且把 docker 里面存储的 npm 包也同步到宿主机上面
    volumes:
      - ./config.yaml:/opt/sinopia/config.yaml
      - ./htpasswd:/opt/sinopia/htpasswd
      - /data/storage/:/opt/sinopia/storage/

```

写好了 docker-compose.yml 后就可以通过 `docker-compose up` 来启动 sinopia 服务。
