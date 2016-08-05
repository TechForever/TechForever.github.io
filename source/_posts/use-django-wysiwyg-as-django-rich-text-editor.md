title: 最好用的Django富文本编辑器
date: 2016-08-04 14:57:52
tags:
---


Django不自带富文本编辑器，但是富文本编辑器是做CRM系统不可缺的工具。我们前后尝试过多个富文本编辑器， 包括但不限于`Ueditor`, `Django-Summernote`等。
最后找到一个最合适的方案： <a target="_blank" href="https://github.com/django-ckeditor/django-ckeditor">django-ckeditor</a>
![](http://obdp0ndxs.bkt.clouddn.com/ckeditor.png)

<!-- more -->

## Django WYSIWYG
我们在接入CKEDITOR时用到另一个工具：<a target="_blank" href="https://github.com/pydanny-archive/django-wysiwyg">django-wysiwyg</a>
此物作用是将Django的各种WYSIWYG编辑器进行统一封装，做成一个接口统一的接口。其只提供接口，具体要用的各个编辑器本身， 还是需要分别安装的。
此工具主要提供几个`tag`和一个`change_form.html`页面。 
* 当在我们自己写的APP中使用时, 只需按照其文档，调用`tag`即可将`textarea`标签转换为`settings`中配置好的富文本编辑器，无需修改任何`py`代码。
* 而`change_form.html`是用于替换`Django Admin`中的`change form template`的。
原理是继承于原`admin`的`change_form.html`并添加了WYSIWYG相关代码。


## 安装步骤
1. 安装相关库
``` bash
pip install django-wysiwyg
pip install django-ckeditor
```
1. 在INSTALLED_APPS中添加相应的库
``` python
INSTALLED_APPS = [

    ……

    'ckeditor',
    'ckeditor_uploader',
    'django_wysiwyg',

    ……

]
```

1. 确保项目的STATIC_ROOT, STATIC_URL, MEDIA_ROOT, MEDIA_URL等正确配置

1. settings中添加相应配置
``` python
    DJANGO_WYSIWYG_FLAVOR = "ckeditor"  # 使用哪个符文本编辑器
    CKEDITOR_UPLOAD_PATH = "ck_uploads/"  # 文件上传路径
    CKEDITOR_UPLOAD_SLUGIFY_FILENAME = True
    CKEDITOR_IMAGE_BACKEND = 'pillow'  # 使用pillow处理图片
    CKEDITOR_ALLOW_NONIMAGE_FILES = False  # 只允许上传图片
    CKEDITOR_JQUERY_URL = "%s/jslib/jquery-1.11.0.min.js" % STATIC_URL  # 指定jquery文件路径,否则会使用google cdn.
    DJANGO_WYSIWYG_MEDIA_URL = "%s/ckeditor/" % STATIC_URL  # 指定ckeditor 的js和css所在路径
```

1. urls中添加相关uploader url
``` python
    url(r'^ckeditor/', include('ckeditor_uploader.urls')),
```

1. CKEDITOR推荐配置：
``` python
{
    'skin': 'moono',
    # 'skin': 'office2013',
    'toolbar_Basic': [
        ['Source', '-', 'Bold', 'Italic']
    ],
    'toolbar_YouCustomToolbarConfig': [
        {
            'name': 'basicstyles',
            'items': [
                'Bold', 'Italic', 'Underline', 'Strike',
                'Subscript', 'Superscript', '-', 'RemoveFormat'
            ]
        },
        {
            'name': 'paragraph',
            'items': [
                'NumberedList', 'BulletedList', '-', 'Outdent', 'Indent',
                '-', 'Blockquote', 'CreateDiv', '-', 'JustifyLeft',
                'JustifyCenter', 'JustifyRight', 'JustifyBlock',
                '-', 'BidiLtr', 'BidiRtl', 'Language'
            ]
        },
        {'name': 'links', 'items': ['Link', 'Unlink', 'Anchor']},
        {
            'name': 'insert',
            'items': [
                'Image', 'Flash', 'Table', 'HorizontalRule',
                'Smiley', 'SpecialChar', 'PageBreak', 'Iframe'
            ]
        },
        '/',
        {
            'name': 'clipboard',
            'items': [
                'Cut', 'Copy', 'Paste', 'PasteText',
                'PasteFromWord', '-', 'Undo', 'Redo'
            ]
        },
        {'name': 'editing', 'items': [
            'Find', 'Replace', '-', 'SelectAll']},
        {
            'name': 'forms',
            'items': [
                'Form', 'Checkbox', 'Radio', 'TextField', 'Textarea',
                'Select', 'Button', 'ImageButton', 'HiddenField'
            ]
        },
        {
            'name': 'styles',
            'items': [
                'Styles', 'Format', 'Font', 'FontSize'
            ]
        },
        {'name': 'colors', 'items': ['TextColor', 'BGColor']},
        {
            'name': 'tools',
            'items': [
                'Maximize',
                'ShowBlocks',
                'Preview',
                'Source',
            ]
        },
    ],
    # put selected toolbar config here
    'toolbar': 'YouCustomToolbarConfig',
    # 'toolbarGroups': [{ 'name': 'document', 'groups': [ 'mode', 'document', 'doctools' ] }],
    # 'height': 291,
    # 'width': '100%',
    # 'filebrowserWindowHeight': 725,
    # 'filebrowserWindowWidth': 940,
    # 'toolbarCanCollapse': True,
    # 'mathJaxLib': '//cdn.mathjax.org/mathjax/2.2-latest/MathJax.js?config=TeX-AMS_HTML',
    'tabSpaces': 4,
    'filebrowserImageUploadUrl': '/ckeditor/upload/',
    'extraPlugins': ','.join(
        [
            # you extra plugins here
            'div',
            'autolink',
            'autoembed',
            'embedsemantic',
            'autogrow',
            'image2',
            # 'devtools',
            'widget',
            'lineutils',
            'clipboard',
            'dialog',
            'dialogui',
            'elementspath'
        ]),
}
```

1. 在需要使用富文本编辑器的地方调用WYSIWYG 提供的tag:
``` python
{% load wysiwyg %}  # 加载tag
{% wysiwyg_setup %}  # 引入富文本编辑器的js, css等
{% wysiwyg_editor "id_detail" "id_detail_editor" CKEDITOR_CONFIGS|safe  %}  # 在需要使用富文本编辑器的元素后调用, 注意配置必须用safe filter传入， 否则会转义, js会报错
```

## 关于图片上传
1. 务必安装并正确配置ckeditor_uploader
1. 在CKEDITOR的配置中， 必须配置`filebrowserImageUploadUrl`
1. 推荐使用image2插件来替换原来的image插件， 更简洁好用
1. CKEDITOR是可以支持图片拖拽和复制的， 会自动上传。详见`uploadimage`插件。
