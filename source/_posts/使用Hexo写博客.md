title: 使用 Hexo 写博客
date: 2016-07-04 14:05:08
permalink: use-Hexo
tags: "other"
category: "other"
---

## 为什么用Hexo

1.  静态博客，部署在 github 上，不需要数据库，部署和迁移方便
2.  使用 github 管理代码，我们都用 github，协作方便
3.  博客使用 markdown 格式存储，方便编辑
4.  有许许多多的插件，扩展方便

## 使用

1.  安装 node 和 Hexo
2.  安装 Burnish 博客
3.  写博客
4.  发布
5.  保存源码

<!--more-->

### 安装 node 和 Hexo

[Hexo 安装教程](https://hexo.io/docs/)
推荐使用 nvm 来安装 <https://github.com/creationix/nvm>

### 安装博客
```
# clone 代码
git clone https://github.com/TechForever/TechForever.github.io.git blog

# 更新 git submodule 管理的主题
git submodule update --init --recursive

# 在全局安装hexo命令行工具
npm install hexo -g

# 安装依赖
cd blog && git checkout source && npm install

# 生成博客
hexo generate --watch

# 预览
hexo server
```
### 写博客
```
# 生成新的 markdown 文件，在 source/_post/ 文件夹下
hexo new "your title"

# 写博客，markdown 语法
vim source/_post/your title.md

# 生成博客
hexo generate --watch

# 预览
hexo server

# 发布
hexo deploy

# 将代码 push 到 source 分支
git push origin source:source
```

### master 和 source 分支的区别

master 分支是 github pages 展示的静态资源文件，这些文件是由 source 分支的源代码生成的
有更新需要随时 push 到 github 的 source 分支

### 使用 git submodule 来管理主题
```
# 添加 submodule，submodule 已经 fork 到了 Tech Forever 团队
git submodule add https://github.com/TechForever/hexo-theme-next.git

# 更新主题配置
cd themes/next
vim _config.yml
git commit -a
git push

# 其他命令参考 https://git-scm.com/book/zh/v2/Git-工具-子模块
```

## Trouble Shotting
* `hexo generate` 时报 `hightlight.js` 异常
    * 将 `_config.yml` 中的 auto_detect 置为 `false` 再执行 `hexo generate`。 成功后再将其置回 `true`（开启代码高亮功能）。


* 每次更新 source 时务必将 submodule 也同时更新, 否则有可能导致 submodule 的版本被覆盖
```

git checkout source
git pull --rebase origin source
git submodule update --recursive
```

* 如何同时 push submodule
```
git push --recurse-submodules=on-demand
```

## 感谢

[hexo](https://hexo.io)
[主题 maupassant](https://github.com/tufu9441/maupassant-hexo)
[主题 Next](https://github.com/iissnan/hexo-theme-next)

## PS
文章排版参照 [中文文案排版指北](https://github.com/sparanoid/chinese-copywriting-guidelines)
