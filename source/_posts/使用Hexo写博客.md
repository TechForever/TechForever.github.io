title: 使用Hexo写博客
date: 2016-07-04 14:05:08
permalink: use-Hexo
tags: "other"
category: "other"
---

<!-- more -->

## 为什么用Hexo

1.  静态博客，部署在github上，不需要数据库，部署和迁移方便
2.  使用github管理代码，我们都用github，协作方便
3.  博客使用markdown格式存储，方便编辑
4.  有许许多多的插件，扩展方便

## 使用

1.  安装node和Hexo
2.  安装Burnish博客
3.  写博客
4.  发布
5.  保存源码

### 安装node和Hexo

[Hexo安装教程](https://hexo.io/docs/)
推荐使用nvm来安装 <https://github.com/creationix/nvm>

### 安装Burnish博客
```
# clone 代码
git clone https://github.com/BurnishTechCN/BurnishTechCN.github.io.git blog

# 更新git submodule管理的主题
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
# 生成新的markdown文件，在source/_post/文件夹下
hexo new "your title"

# 写博客，markdown语法
vim source/_post/your title.md

# 生成博客
hexo generate --watch

# 预览
hexo server

# 发布
hexo deploy

# 将代码push到source分支
git push origin source:source
```
## ps

### master和source分支的区别

master分支是github pages展示的静态资源文件，这些文件是由source分支的源代码生成的
有更新需要随时push到github的source分支

### 使用git submodule来管理主题
```
# 添加submodule, submoduel已经fork到了BurnichTechCN团队
git submodule add https://github.com/BurnishTechCN/maupassant-hexo.git themes/maupassant

# 更新主题配置
cd themes/maupassant
vim _config.yml
git commit -a
git push

# 其他命令参考https://git-scm.com/book/zh/v2/Git-工具-子模块
```

## Trouble Shotting
* `hexo generate` 时报`hightlight.js`异常
    * 将`_config.yml`中的 auto_detect置为`false`再执行`hexo generate`。 成功后再将其置回`true`(开启代码高亮功能)。


* 每次更新source时务必将submodule也同时更新, 否则有可能导致submodule的版本被覆盖
```
git checkout source
git pull --rebase origin source
git git submodule update --recursive
```

* 如何同时push submodule
```
git push --recurse-submodules=on-demand
```

## 感谢

[hexo](https://hexo.io)
[主题maupassant](https://github.com/tufu9441/maupassant-hexo)
