# Django
INSTALL_APP
• django.contrib.admin :管理后台
• django.contrib.auth :身份验证系统
• django.contrib.contenttypes :内容类型框架
• django.contrib.sessions :会话框架
• django.contrib.messages :消息框架
• django.contrib.staticfiles :管理静态文件的框架
ROOT_URLCONF 
- MVC
• 模型(M)是数据的表述。它不是真正的数据,而是数据的接口。使用模型从数据库中获取数据时,
无需知道底层数据库错综复杂的知识。模型通常还会为数据库提供一层抽象,这样同一个模型就能使用不同的数据库。
• 视图(V)是你看到的界面。它是模型的表现层。在电脑中,视图是你在浏览器中看到的 Web 应用的页面,或者是桌面应用的 UI。视图还提供了收集用户输入的接口。
• 控制器(C)控制模型和视图之间的信息流动。它通过程序逻辑判断通过模型从数据库中获取什么信息,以及把什么信息传给视图。它还通过视图从用户那里收集信息,并且实现业务逻辑:变更视图,
或者通过模型修改数据,或者二者兼具。
- MTV
• M 表示“模型”,即数据访问层。这一层包含所有与数据相关的功能:访问数据的方式、验证数据的方式、数据的行为、数据之间的关系。
• T 表示“模板”,即表现层。这一层包含表现相关的决策:在网页或其他文档类型中如何显示某个东
西。
• V 表示“视图”,即业务逻辑层。这一层包含访问模型和选择合适模板的逻辑。你可以把视图看做模型和模板之间的桥梁。
django.http.HttpResponse  Http404
模板Template
- 字典和Context
django.template.Template
context -> template = html
template中可以使用python内置函数
\>>> from django.template import Template, Context
\>>> t = Template('{{ var }} -- {{ var.upper }} -- {{ var.isdigit }}')
\>>> t.render(Context({'var': 'hello'}))
'hello -- HELLO -- False'
可以使用列表，不可负索引
\>>> t = Template('Item 2 is {{ items.2 }}.')
\>>> c = Context({'items': ['apples', 'bananas',
'carrots']})
\>>> t.render(c)
'Item 2 is carrots.'
模板系统遇到变量名中的点号时会按照下述顺序尝试查找:
• 字典查找(如 foo["bar"] )
• 属性查找(如 foo.bar )
• 方法调用(如 foo.bar() )
• 列表索引查找(如 foo[2] )
??? alert_data    silent_variable_failure
- 模板标签
{% if not and or %}必须
{% elif %}可选
{% else %}可选
{% endif %}必须
not > and > or 优先级不能使用括号，可以用嵌套代替
{% for i in list %}/{% for k,v in dict.items %}必须
{% empty %}可选
{% forloop.last counter counter0 revconter revconter0 first %}循环内部变量
{% forloop.parentloop... %} 父级循环
{% endfor %}必须
ifequal/ifnotequal
{% ifequal a b %}支持变量，数值，字符串
{% endifequal %}
{# 单行注释 #}
{% comment %}
多行注释
{% endcomment %}
{% block name %}{% endblock %}
{% include '.html' %} 将另个html导进来 
{% extend 'base.html' %} 基于某个html生成
- 过滤器
{{ name|upper }}
{{ bio|truncatewords:"30" }}显示 bio 变量的前 30 个词
• addslashes :在反斜线、单引号和双引号前面添加一个反斜线。可用于转义字符串。例如: {{ val-ue|addslashes }} 。
• date :根据参数中的格式字符串格式化 date 或 datetime 对象。例如: {{ pub_date|date:"F j, Y"
}} 。
• length :返回值的长度。对列表来说,返回元素的数量。对字符串来说,返回字符的数量。如果变量未定义,返回 0 。
pluralize 模板过滤器,它会根据找到的图书数量输出正确的单复数。
TEMPLATES = [
{
'BACKEND': 'django.template.backends.django.DjangoTemplates',
'DIRS': [],
'APP_DIRS': True,
'OPTIONS': {... 一些选项 ...
},
},
]
• DIRS 定义一个目录列表,模板引擎按顺序在里面查找模板源文件。os.basedir
• APP_DIRS 设定是否在安装的应用中查找模板。按约定, APPS_DIRS 设为 True 时, DjangoTemplates 会在INSTALLED_APPS 中的各个应用里查找名为“templates”的子目录。这样,即使 DIRS 为空,模板引擎还能查找应用模板。
• OPTIONS 是一些针对后端的设置。
django.shortcuts.render
- 模板的模板 模板继承
base.html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN">
<html lang="en">
<head>
<title>{% block title %}{% endblock %}</title>
</head>
<body>
<h1>My helpful timestamp site</h1>
{% block content %}{% endblock %}
{% block footer %}
<hr>
<p>Thanks for visiting my site.</p>
{% endblock %}
</body>
</html>
index.html
{% extends "base.html" %}
{% block title %}The current time{% endblock %}
{% block content %}
<p>It is now {{ current_date }}.</p>
{% endblock %}
- 模板继承
• 如果模板中有 {% extends %} ,必须是模板中的第一个标签。否则,模板继承不起作用。
• 一般来说,基模板中的 {% block %} 标签越多越好。记住,子模板无需定义父模板中的全部块,因此
可以为一些块定义合理的默认内容,只在子模板中覆盖需要的块。钩子多总是好的。
• 如果发现要在多个模板中重复编写相同的代码,或许说明应该把那些代码移到父模板中的一个 {%
block %} 标签里。
• 如果需要从父模板中的块里获取内容,使用 {{ block.super }} ,这是一个“魔法”变量,提供父模板中
渲染后的文本。向块中添加内容,而不是完全覆盖时就可以这么做。
• 在同一个模板中不能为多个 {% block %} 标签定义相同的名称。之所以有这个限制,是因为 block 标
签是双向的。即, block 标签不仅标识供填充的空位,还用于定义填充父模板中空位的内容。如果一
个模板中有两个同名的块,那么父模板就不知道使用哪个块里的内容。
• 传给 {% extends %} 的模板名称使用与 get_template() 相同的方法加载。即,模板在 DIRS 设置定义的
目录中,或者在当前 Django 应用的“templates”目录里。
• 多数情况下, {% extends %} 的参数是字符串,不过如果直到运行时才知道父模板的名称,也可以用变
量。通过这一点可以做些动态判断。
模型Model
Model(data).save()
Model.objects.all()[0:1]
Model.objects.fliter(name__contains="press").order_by()双下划线用法
Model.objects.get()
Model.objects.order_by()
Model.objects.filter(id=52).update(name='Apress Publishing')
管理后台ModelAdmin
python manage.py createsuperuser
把模型添加到 Django 管理后台中
admin.py中
from django.contrib import admin
from .models import Publisher, Author, Book
// 修改管理界面的显示内容，也可以通过Model的__str__()方法定义
class AuthorAdmin(admin.ModelAdmin):
list_display = ('first_name', 'last_name', 'email')
// 搜索框
search_fields = ('first_name', 'last_name')
class BookAdmin(admin.ModelAdmin):
list_display = ('title', 'publisher', 'publication_date')
list_filter = ('publication_date',)
// 日期选择
date_hierarchy = 'publication_date'
ordering = ('-publication_date',)
// 编辑表单
filter_horizontal
field
// 将Model加入管理界面
admin.site.register(Publisher)
admin.site.register(Author)
admin.site.register(Book)
空值
publication_date = models.DateField(blank=True, null=True)
自定义字段的标注
email = models.EmailField(blank=True, verbose_name='e-mail')
表单Form
django.http.request.HttpRequest()
request.path get_host get_full_path is_secure MATE
- 简单的表单
html里定义表单以及 action method name
view里通过name获取内容 
返回response还可以加如验证信息 html里根据验证结果渲染不同页面
- 表单类 Form
使用is_bound is_valid验证
使用.errors获取错误信息
使用cleand_data 获取数据 一个字典
生成不同的html as_table as_ul ...
<form action="" method="post">
<table>
{{ form.as_table }}
</table>
{% csrf_token %}
<input type="submit" value="Submit">
</form>
class ContactForm(forms.Form):
subject = forms.CharField(max_length=100)
email = forms.EmailField(required=False, label='Your e-mail address')
message = forms.CharField(widget=forms.Textarea)
自定义验证规则
def clean_message(self):
message = self.cleaned_data['message']
num_words = len(message.split())
if num_words < 4:
raise forms.ValidationError("Not enough words!")
return message
通过widget改变字段的渲染方式,将表单的text 改为textarea
通过required=False设定可以留空
通过max_length
label设定标注
通过css自定义外观html里通过{% form.subject %}
<form action="" method="post">
<div class="field">
{{ form.subject.errors }}
<label for="id_subject">Subject:</label>
{{ form.subject }}
</div>
<div class="field">
{{ form.email.errors }}
6.12 自定义表单的外观 
<label for="id_email">Your e-mail address:</label>
{{ form.email }}
</div>
<div class="field">
{{ form.message.errors }}
<label for="id_message">Message:</label>
{{ form.message }}
</div>
{% csrf_token %}
<input type="submit" value="Submit">
设定初始值form = ContactForm(initial={'subject': 'I love your site!'})