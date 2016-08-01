title: RethinkDB 实时数据库
date: 2016-08-01 13:46:35
permalink: RethinkDB-database-for-the-realtime
tags:
---


RethinkDB 是一个为构建实时应用程序而生的开源数据库，它可以实时地向你的应用程序推送数据

![](http://img.l.jifangcheng.com/rethinkdb-1.png)

<!--more-->

## 什么是 RethinkDB
* 一个为构建实时应用程序而生的开源数据库
* NoSQL 非关系型数据库，它存储 JSON 数据
* 易拓展的分布式数据库
* 拥有自动故障转移和强大容错能力的高性能数据库

## RethinkDB X Python 快速开始
### 安装 RethinkDB
macOS 可以使用 `brew install rethinkdb` 来快速安装 RethinkDB，其它的系统可以看官网的文档 [https://rethinkdb.com/docs/install/](https://rethinkdb.com/docs/install/)

### 安装 Python 连接 RethinkDB 的库
使用 pip 就可以了 `pip install rethinkdb`

### 使用 RethinkDB
* 首先启动 RethinkDB

```
$ rethinkdb
```

* 在 python 里面引入 RethinkDB 的库就可以操作它了

```
$ python

>>> import rethinkdb as r
```

* 连接数据库

```
r.connect("localhost", 28015).repl()
```

*注意：`repl` 方法仅适合在 shell 里面调试使用，它会给你当前的 shell 环境设置一个默认的连接，使得你不用每次都给 `run` 方法传一个 `connection` 对象进去。但是，在生产环境中你不能使用这种方法，你执行的每个查询命令都必须要给 `run` 方法传一个 `connection` 对象进去，你可以看一下这个[简单的示例](https://github.com/rethinkdb/rethinkdb-example-flask-backbone-todo/blob/master/todo.py)*

* 创建一张表

在 `test` 数据库里创建一张 `authors` 表

```
r.db("test").table_create("authors").run()
```

你应该会得到和下面类似的结果：

```
{
    "config_changes": [
        <table configuration data>
    ],
    "tables_created": 1
}
```

*RethinkDB 默认创建了一个名为 `test` 的数据库，你可以使用 `r.db_create(db_name)` 来创建一个数据库*

#### 插入数据

使用 `insert()` 方法来插入数据，插入的数据都是 JSON 格式的

```
r.table("authors").insert([
    { "name": "William Adama", "tv_show": "Battlestar Galactica",
      "posts": [
        {"title": "Decommissioning speech", "content": "The Cylon War is long over..."},
        {"title": "We are at war", "content": "Moments ago, this ship received..."}
      ]
    },
    { "name": "Laura Roslin", "tv_show": "Battlestar Galactica",
      "posts": [
        {"title": "The oath of office", "content": "I, Laura Roslin, ..."},
        {"title": "They look like us", "content": "The Cylons have the ability..."}
      ]
    }
]).run()
```

*注意：因为我们上面连接时使用了 `repl` 方法，所以这里的 `run` 不用传一个 `connection` 对象进去。但是生产环境里面不能使用这种方法，下面是生产环境应该使用的方法的一个示例*

```
rdb_conn = r.connect(host=RDB_HOST, port=RDB_PORT, db=DB)

r.table("authors").insert(……).run(rdb_conn)
```

#### 数据查询

* 查询所有数据

```
cursor = r.table("authors").run()
for document in cursor:
    print(document)
```

这个操作会返回 `authors ` 表里面的所有数据，每条数据都会附带它的 `id`

```
{u'name': u'hello', u'id': u'6ca50999-75eb-4af4-b9b0-bbac478dc884'}
```

* 条件查询

下面的操作会返回 `name ` 是 `William Adama` 的数据

```
cursor = r.table("authors").filter(r.row["name"] == "William Adama").run()
for document in cursor:
    print(document)
```

下面的操作会返回数据里面 `posts ` 大于 2 条记录的数据

```
cursor = r.table("authors").filter(r.row["posts"].count() > 2).run()
for document in cursor:
    print(document)
```

* 查询单条记录

查询 `id` 为 `7644aaf2-9928-4231-aa68-4e65e31bf219` 的数据

```
r.db('test').table('authors').get('7644aaf2-9928-4231-aa68-4e65e31bf219').run()
```

#### 实时数据

使用 `changes()` 方法来监听数据的变化

```
cursor = r.table("authors").changes().run()
for document in cursor:
    print(document)
```

#### 更新数据

使用 `update()` 方法来删除数据

更新所有数据

```
r.table("authors").update({"type": "fictional"}).run()
```

结合 `filter ` 更新指定数据

```
r.table("authors").
    filter(r.row['name'] == "William Adama").
    update({"rank": "Admiral"}).run()
```

更新数组的数据

```
r.table('authors').filter(r.row["name"] == "Jean-Luc Picard").
    update({"posts": r.row["posts"].append({
        "title": "Shakespeare",
        "content": "What a piece of work is man..."})
    }).run()
```

#### 删除数据

使用 `delete()` 方法来删除数据

```
r.table("authors").
    filter( r.row["posts"].count() < 3 ).
    delete().run()
```

## RethinkDB 驱动
RethinkDB 官方提供了对 JavaScript, Python, Ruby, Java 四种语言的支持，还有很多社区贡献的第三方的对其它语言的支持，你可以在下面的这个网址里面找到
[https://rethinkdb.com/docs/install-drivers/](https://rethinkdb.com/docs/install-drivers/)

## 参考链接
* [RethinkDB: the open-source database for the realtime web](https://rethinkdb.com)
* [RethinkDB Documentation](https://rethinkdb.com/docs/)
* [https://github.com/rethinkdb/rethinkdb](https://github.com/rethinkdb/rethinkdb)


![](http://img.l.jifangcheng.com/rethinkdb-2.png)