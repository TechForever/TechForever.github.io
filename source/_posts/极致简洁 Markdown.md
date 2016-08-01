title: 极致简洁 Markdown
date: 2016-07-28 17:23:37
permalink: Extreme-simplicity-Markdown
tags:
---



Markdown 是一种轻量级标记语言，它允许人们使用易读易写的纯文本格式编写文档，然后转换成有效的 XHTML/HTML 文档


<!-- more -->

## 什么是 Markdown

![Markdown](http://img.l.jifangcheng.com/markdown-1.png)

### 宗旨
Markdown 的目标是实现「易读易写」。

Markdown 的语法全由一些符号所组成，这些符号经过精挑细选，其作用一目了然。比如：在文字两旁加上星号，看起来就像强调。Markdown 的列表看起来，嗯，就是列表。Markdown 的区块引用看起来就真的像是引用一段文字，就像你曾在电子邮件中见过的那样。

### Markdown 和 HTML

* Markdown 不是想要取代 HTML，甚至也没有要和它相近，它的语法种类很少，只对应 HTML 标记的一小部分。
* Markdown 的格式语法只涵盖纯文本可以涵盖的范围。
* Markdown 的构想不是要使得 HTML 文档更容易书写，它的理念是，能让文档更容易读、写和改。
* HTML 是一种发布的格式，Markdown 是一种书写的格式。

## 为什么使用 Markdown

* 纯文本，所以兼容性极强，可以用所有文本编辑器打开
* 专注写作，而不是关注排版
* 格式转换方便，Markdown 的文本可以轻松转换为 html、PDF等
* Markdown 的标记语法有极好的可读性

## Markdown 语法简介
### 标题 `#`
以 `#` 开始，一个 `#` 对应一级标题，最多可以有 6 级标题

```
#       一级标题 H1
##      二级标题 H2
###     三级标题 H3
####    四级标题 H4
#####   五级标题 H5
######  六级标题 H6
```

### 强调 `*`
用 `*` 包裹文字表示强调，一字 `*` 包裹的表示斜体，两个表示粗体

![Markdown](http://img.l.jifangcheng.com/markdown-6.png)

### 列表 `*`
单个 `*` 表示这是一个无序列表，用数字表示这是一个有序列表

![Markdown](http://img.l.jifangcheng.com/markdown-5.png)

### 引用 `>`
使用 `>` 表示这段文字是一个引用

![Markdown](http://img.l.jifangcheng.com/markdown-7.png)

### 链接和图片
链接的格式：`[链接内容](链接地址)`
图片的格式和链接类似，只不过在前端加一个 `!`：`![图片描述](图片地址)`

![Markdown](http://img.l.jifangcheng.com/markdown-8.png)

### 代码
使用 \` \` 表示行内代码，\`\`\` 表示多行代码

![Markdown](http://img.l.jifangcheng.com/markdown-9.png)

## <i class="fa fa-github" aria-hidden="true"></i> GitHub Flavored Markdown
GitHub Flavored Markdown 是 GitHub 使用的 Markdown 格式，对 Markdown 增加了一些新功能

### 代码高亮
\`\`\` 后面加上编程语言可以让代码按照你指定的语言进行高亮

<pre>
```javascript
function fancyAlert(arg) {
  if(arg) {
    $.facebox({div:'#foo'})
  }
}
```
</pre>

### 任务列表
下面的的这种格式会被渲染成一个任务列表

```
- [x] @mentions, #refs, [links](), **formatting**, and <del>tags</del> supported
- [x] list syntax required (any unordered or ordered list supported)
- [x] this is a complete item
- [ ] this is an incomplete item
```

![Markdown](http://img.l.jifangcheng.com/markdown-3.png)

### 表格
下面的这种格式会被渲染成一个表格

```
First Header | Second Header
------------ | -------------
Content from cell 1 | Content from cell 2
Content in the first column | Content in the second column
```

![Markdown](http://img.l.jifangcheng.com/markdown-4.png)

### 引用 SHA 值
对任何 commit 的 SHA 值的引用都会自动转换成这个 commit 的链接

```
16c999e8c71134401a78d4d46435517b2271d6ac
mojombo@16c999e8c71134401a78d4d46435517b2271d6ac
mojombo/github-flavored-markdown@16c999e8c71134401a78d4d46435517b2271d6ac
```

### 引用 Issue
你可以直接引用一个 Issue 或者 Pull Request 的编号，它们会被自动的转换成链接

```
#1
mojombo#1
mojombo/github-flavored-markdown#1
```

### @
使用 `@` 符号来提醒一个人查看这个消息

### URL 地址自动链接
任何的链接（比如 `http://www.github.com/`）都会自动转换成可以点击的链接

### 删除线
使用 `~~这个~~` 表示删除线，会呈现出 <del>这样</del> 的效果

### Emoji
<i class="fa fa-github" aria-hidden="true"></i> GitHub 当然也支持 🐶 Emoji

## 一些有用的链接
* Markdown 项目主页：[https://daringfireball.net/projects/markdown/](https://daringfireball.net/projects/markdown/)
* [Mastering Markdown · GitHub Guides](https://guides.github.com/features/mastering-markdown/)
* [Markdown 语法说明(简体中文版)](http://wowubuntu.com/markdown/)
