title: django-xadmin
date: 2016-08-05 16:13:41
category: "Django"
tags:
---


Django xadmin是Django admin的替代品，功能更强大。能通过配置快速实现chart是我选择他的主要原因。另外还有第三方插件可以用于扩展

<!-- more -->


## Chart
xadmin 默认使用Flot.js为chart组建， 该库为基于jquery的一个chart库， 调用起来比较方便， 但是文档质量较差，功能较弱，体验也不太理想。
比较方便的是该插件提供了一个强大的views方法， 能通过配置轻松的构建出AJAX数据API，且template上也将数据API与相应的chart元素作好绑定。
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
    * option: flot.js的其他参数， 会覆盖默认的参数, 官方文档未提及， 但是这个参数对于自定义chart非常重要

* API
```
class xadmin.plugins.chart.ChartsPlugin(admin_view)

class xadmin.plugins.chart.ChartsView(request, *args, **kwargs)
```
### data_charts 的key
此key无实际作用，为了控制各chart的顺序， 最好使用collection.OrderedDict来初始化

### y-field
此参数可以是一个callable的方法， 可以是modaladmin中已定义好的一个函数名。

### order
只能是数据中的一个字段， 且是查询时排序， 不是很方便。

### 直接上代码， 我们如何利用该插件实现员工调休统计图表：
``` python
class WorkDayOffAdmin(object):
    data_charts = OrderedDict((
        (
            "all",
            {
                'title': u"总览",
                'x-field': 'burnish_user__name',
                'y-field': (
                    'work_day_offs',
                    'extra_works',
                    'total_hours',
                ),
                'order': ('hours', ),
                'option':{
                    'series': {
                        'bars': {
                            'show': True,
                            'barWidth': 0.5,
                        }
                    },
                    'xaxis': {
                        'mode': "categories",
                        'tickLength': 0,
                    },
                }
            },
        ),
        (
            "last",
            {
                'title': u"剩余换休",
                'x-field': 'burnish_user__name',
                'y-field': (
                    'total_hours',
                ),
                'order': ('hours', ),
                'option':{
                    'series': {
                        'bars': {
                            'show': True,
                            'barWidth': 0.5,
                        }
                    },
                    'xaxis': {
                        'mode': "categories",
                        'tickLength': 0,
                    },
                }
            },
        ),
        (
            "extra_works",
            {
                'title': u"加班",
                'x-field': 'burnish_user__name',
                'y-field': (
                    'extra_works',
                ),
                'order': ('hours', ),
                'option':{
                    'series': {
                        'bars': {
                            'show': True,
                            'barWidth': 0.5,
                        }
                    },
                    'xaxis': {
                        'mode': "categories",
                        'tickLength': 0,
                    },
                }
            },
        ),
        (
            "work_offs",
            {
                'title': u"已换休",
                'x-field': 'burnish_user__name',
                'y-field': (
                    'work_day_offs',
                ),
                'order': ('hours', ),
                'option':{
                    'series': {
                        'bars': {
                            'show': True,
                            'barWidth': 0.5,
                        }
                    },
                    'xaxis': {
                        'mode': "categories",
                        'tickLength': 0,
                    },
                },
            },
        ),
    ))
    list_display = (
        'burnish_user',
        'from_date',
        'end_date',
        'hours',
        'status',
        'reason',
    )
    list_filter = (
        'burnish_user',
        'status',
    )
    search_fields = (
        'burnish_user__name',
        'reason',
    )
    list_editable = (
        'from_date',
        'end_date',
        'hours',
        'status',
    )
    date_hierarchy = 'from_date'
    total_hours_dealed = None
    extra_works_dealed = None
    work_day_offs_dealed = None

    def total_hours(self, data):
        if not self.total_hours_dealed:
            self.total_hours_dealed = []
        user_id = data.burnish_user.id
        if user_id  in self.total_hours_dealed:
            return  None
        self.total_hours_dealed.append(user_id)
        return WorkDayOff.objects.filter(
            burnish_user_id=user_id,
            status='sure',
        ).aggregate(
            total_hours=Sum('hours')
        )['total_hours'] or 0
    total_hours.short_description = '剩余换休'

    def extra_works(self, data):
        if not self.extra_works_dealed:
            self.extra_works_dealed= []
        user_id = data.burnish_user.id
        if user_id  in self.extra_works_dealed:
            return  None
        self.extra_works_dealed.append(user_id)
        return WorkDayOff.objects.filter(
            burnish_user_id=user_id,
            status='sure',
            hours__gt=0,
        ).aggregate(
            total_hours=Sum('hours')
        )['total_hours'] or 0
    extra_works.short_description = '总计加班'

    def work_day_offs(self, data):
        if not self.work_day_offs_dealed:
            self.work_day_offs_dealed= []
        user_id = data.burnish_user.id
        if user_id  in self.work_day_offs_dealed:
            return  None
        self.work_day_offs_dealed.append(user_id)
        return abs(WorkDayOff.objects.filter(
            burnish_user_id=user_id,
            status='sure',
            hours__lt=0,
        ).aggregate(
            total_hours=Sum('hours')
        )['total_hours'] or 0)
    work_day_offs.short_description = '总计休假'
```
![](http://obdp0ndxs.bkt.clouddn.com/admin_charts.png)
