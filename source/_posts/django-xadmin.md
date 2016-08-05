title: django-xadmin
date: 2016-08-05 16:13:41
category: "Django"
tags:
---


Django xadmin是Django admin的替代品，功能更强大。能通过配置快速实现chart是我选择他的主要原因。另外还有第三方插件可以用于扩展

<!-- more -->


## Chart
在Model OptionClass 中设定data_charts属性，该属性为dict类型，key是图表的标示名称，value是图表的具体设置属性，示例:
``` python
class RecordAdmin(object):
    data_charts = {
        "user_count": {'title': u"User Report", "x-field": "date", "y-field": ("user_count", "view_count"), "order": ('date',)},
        "avg_count": {'title': u"Avg Report", "x-field": "date", "y-field": ('avg_count',), "order": ('date',)}
    }
```

* 图表的主要属性为:
    * title : 图表的显示名称
    * x-field : 图表的 X 轴数据列, 一般是日期, 时间等
    * y-field : 图表的 Y 轴数据列, 该项是一个 list, 可以同时设定多个列, 这样多个列的数据会在同一个图表中显示
    * order : 排序信息, 如果不写则使用数据列表的排序

* API
```
class xadmin.plugins.chart.ChartsPlugin(admin_view)

class xadmin.plugins.chart.ChartsView(request, *args, **kwargs)
```
