title: Atom - The hackable text editor
date: 2016-07-27 17:11:03
permalink: Atom-The-hackable-text-editor
category: 技术
tags: 编辑器 js
---


[Atom](https://github.com/atom/atom) 起初是一个 GitHub 内部的小项目，后来发展壮大成为一个正式的开源项目。Atom 基于 [Electron](https://github.com/electron/electron)（Electron 是一个可以让你用 JavaScript, HTML 和 CSS 来写跨平台桌面应用程序的框架），Atom 是用 [CoffeeScript](http://coffeescript.org) 写的，相当于一个 Web App，也正是因为这样，其可定制程度非常高。

如果你以前用过 Sublime Text，你会发现 Atom 和 Sublime 非常像，连快捷键都基本一样，但是它们真的没有关系。


## 为什么使用 Atom

### 开源
GitHub 开源了 [Atom](https://github.com/atom/atom) 和构建它的内核 [Electron](https://github.com/electron/electron)，开源两年以来所取得的成绩非常耀眼：

* 110 万月度活跃用户
* 1590 万次下载
* 300 个新版本发布
* 超过 1144 位贡献者
* 6.2 万次代码提交
* 5897 个 Pull Request
* 920 万次的月度访问量
* GitHub 上接近 3 万个 Star
* Atom 社区也快速发展

![Atom](http://img.l.jifangcheng.com/atom-2.png)

<!-- more -->

### 跨平台
你可以在 macOS，Windows 以及各种 Linux 发行版本上使用 Atom

### apm: Atom Package Manager
apm 是 Atom 官方的内建的包管理器，你可以在 [https://atom.io/packages](https://atom.io/packages) 查看所有的 Atom 扩展（插件，主题）

你可以在 Atom 的偏好设置里面添加、更新、删除和管理 Atom 的扩展

apm 还提供了一个命令行工具，你可以使用 `apm install xx` 来为 Atom 安装扩展，你可以使用 `apm --help` 来查看 apm 支持的所有命令

关于 apm 的更多文档你可以去 [https://github.com/atom/apm](https://github.com/atom/apm) 看看

### 漂亮的界面
你可以使用 Atom 官方提供的主题和社区贡献的主题来美化你的 Atom，如果你还不喜欢，你甚至可以自己来自定义你的 Atom 界面

![](http://img.l.jifangcheng.com/atom-5.png)
![](http://img.l.jifangcheng.com/atom-6.png)
![](http://img.l.jifangcheng.com/atom-7.png)
![](http://img.l.jifangcheng.com/atom-8.png)
![](http://img.l.jifangcheng.com/atom-9.png)
![](http://img.l.jifangcheng.com/atom-10.png)
![](http://img.l.jifangcheng.com/atom-11.png)

### The hackable text editor

#### 更智能的自动补全
不仅仅是语言关键字的补全，还可以补全路径，使用过的包名，函数，变量名等等

![](http://img.l.jifangcheng.com/atom-3.png)

#### 多行编辑
类似于 Sublime，Atom 也支持多行编辑，它允许你同时编辑多行文本。比如说你需要把一个文档里面的 `('some text')` 替换为 `("some text")`，多行编辑就派上用场了，选中关键词后使用 ctrl/cmd + d 选择下一个这个词，使用 ctrl/cmd + k 可以跳过某个关键词，全部选中关键词后就可以对它们进行编辑了

![](http://img.l.jifangcheng.com/atom-4.png)

#### 多窗口
你可以在多窗口中编辑不同的文件

![](http://img.l.jifangcheng.com/atom-13.png)

也可以在多窗口中编辑相同的文件

![](http://img.l.jifangcheng.com/atom-14.png)

#### VIM 模式
Atom 官方提供了对 VIM 编辑模式的支持

![](http://img.l.jifangcheng.com/atom-12.png)

你可以在 Atom 中使用 VIM 的基本功能（没有插件的 VIM）

#### 代码片段
你可以在你的用户目录下的 `.atom/snippets.cson` 中定义代码片段，定义了代码片段之后你就可以使用你定义的快捷单词 + tab 键来插入一段代码

```cson
# .source.python 表明下面的这些代码片段只能在 python 文件中使用
'.source.python':
  'Header':
    'prefix': 'head'
    'body': '''
      #!/usr/bin/env python
      # coding=utf-8
    '''

  'ipdb':
    'prefix': 'ipdb'
    'body': "__import__('ipdb').set_trace()"
```


#### 其它
* ctrl/cmd + p 快速切换文件
* ctrl/cmd + shift + p 打开功能面板
* ctrl + g 快速跳转到文件的某一行
* ctrl/cmd + x 删除一整行
* ctrl/cmd + r 在文件内的函数定义间跳转


### Sublime VS Atom
我以前也是 Sublime 的用户，后来转到了 Atom，我对这两个编辑器都还算是了解，所以来说一下各自相比于对方的优缺点。

Sublime 的优点

* 启动速度快，更稳定


Atom 的优点

* 活跃的开源社区
* 强大的官方的包管理器
* 高度可定制
* 更好的 VIM 模式（Sublime 3 的 VIM 模式居然还有 BUG）

## 一些不足
* 启动速度慢
* 编辑大型文件显得有些吃力

## 一些好用的 Atom 插件
### 通用
* [trailing-spaces](https://atom.io/packages/trailing-spaces) - 高亮多余的空格
* [vim-mode](https://atom.io/packages/vim-mode) - vim 模式
* [ex-mode](https://atom.io/packages/ex-mode) - vim 模式的拓展
* [minimap](https://atom.io/packages/minimap) - 右侧小地图
* [autocomplete-paths](https://atom.io/packages/autocomplete-paths) - 对路径选择进行增强
* [atom-beautify](https://atom.io/packages/atom-beautify) - 自动美化代码格式

### 前端
* [Nuclide](http://nuclide.io) - 用于编写 React Native
* [Emmet](https://atom.io/packages/emmet) - 快速编写 HTML
* [tag](https://atom.io/packages/tag) - Sublime 风格的 HTML 标签自动闭合
* [color-picker](https://atom.io/packages/color-picker) - CSS 颜色取色器
* [autoprefixer](https://atom.io/packages/autoprefixer) - 自动补全 CSS 前缀
* [linter-jshint](https://atom.io/packages/linter-jshint) - JavaScript 语法检查

### Python
* [autocomplete-python](https://atom.io/packages/autocomplete-python) - 对Python提示进行增强
* [linter-flake8](https://atom.io/packages/linter-flake8) - Python 语法检查
* [python-tools](https://atom.io/packages/python-tools) - 一些有用的 Python 开发工具集
