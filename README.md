# django-jet
python
使用django-jet来实现代替原来的admin后台管理。
django-jet官网文档地址：http://jet.geex-arts.com/

1、安装django
pip install django

2、开启一个新的项目

django-admin startproject app .

3、安装django-jet

pip install django-jet

4、使用
将'jet'应用添加到你的Django项目的设置文件settings.py中的INSTALLED_APPS设置中(注意'jet'应位于'django.contrib.admin'之前)

INSTALLED_APPS = (
        ...
        'jet',
        'django.contrib.admin',
        ...
)

 确认在settings.py中启用了上下文处理器django.template.context_precessors.request(Djang 1.8以上版本)

在django 1.8之前应该使用不能的方式指定上下文处理器。即使用django.core.context_preocessor.request代替django.template.context_precessors.request

from django.conf import global_settings

TEMPLATE_CONTEXT_PROCESSORS = global_settings.TEMPLATE_CONTEXT_PROCESSORS + (
        'django.core.context_processors.request',
)

在项目的urls.py文件的urlpatterns中添加url模版(为了关联到相关路由)

urlpatterns = patterns(
    '',
    url(r'^jet/', include('jet.urls', 'jet')),  # Django JET URLS
    url(r'^admin/', include(admin.site.urls)),
    ...
)

 创建数据表

python manage.py migrate jet
# or
python manage.py syncdb

收集静态文件(产品环境中)
python manage.py collectstatic

5、仪表盘安装#

仪表盘位于一个单独的应用中。所以当完成标准的JET安装后它还不能工作。需要按照以下步骤启用仪表盘应用：

将'jet.dashboard'应用添加到你的Django项目的设置文件settings.py中的INSTALLED_APPS设置中(注意'jet.dashboard'应位于'jet'之前)

INSTALLED_APPS = (
    ...
    'jet.dashboard',
    'jet',
    'django.contrib.admin',
    ...
)
在项目的urls.py文件的urlpatterns中添加url模版(为了关联到相关路由)

urlpatterns = patterns(
    '',
    url(r'^jet/', include('jet.urls', 'jet')),  # Django JET URLS
    url(r'^jet/dashboard/', include('jet.dashboard.urls', 'jet-dashboard')),  # Django JET dashboard URLS
    url(r'^admin/', include(admin.site.urls)),
    ...
    
安装python包For Google Analytics widgets only
pip install google-api-python-client==1.4.1


创建数据表

python manage.py migrate dashboard
# or
python manage.py syncdb.

收集静态文件(产品环境中)
python manage.py collectstatic

仪表盘已经安装了。学习Dashboard章节以自定义仪表盘。


6、配置#

配置文件##
配置是指在settings.py中一些可用的选项:

JET_DEFAULT_THEME###
Django JET允许用户改变默认的主题。这个特点主要是用来自定义颜色方案而不是制作一个完全不同的主题。这个选项实际上的作用是使Django加载不同的css文件。

可用的内置主题如下：
<ul>
<li>default(默认)
<li>green(绿色)
<li>light-violet(浅紫色)
<li>light-green(浅绿色)
<li>light-blue(浅蓝)
<li>light-gray(浅灰)
</ul>
可以使用参数改变主题
JET_DEFAULT_THEME = 'light-gray'

JET_THEMES###

可以允许用户自行改变管理面板的配色方案。这个选项会添加配色方案选择器到用户的下拉菜单。如果要禁用这个选项只需使JET_THEMS是一个空的list即可。

JET_THEMES = [
    {
        'theme': 'default', # theme folder name
        'color': '#47bac1', # color of the theme's button in user menu
        'title': 'Default' # theme title
    },
    {
        'theme': 'green',
        'color': '#44b78b',
        'title': 'Green'
    },
    {
        'theme': 'light-green',
        'color': '#2faa60',
        'title': 'Light Green'
    },
    {
        'theme': 'light-violet',
        'color': '#a464c4',
        'title': 'Light Violet'
    },
    {
        'theme': 'light-blue',
        'color': '#5EADDE',
        'title': 'Light Blue'
    },
    {
        'theme': 'light-gray',
        'color': '#222',
        'title': 'Light Gray'
    }
]

自定义JET_THEME
可以通过在/static/jet/css/thems/下添加新的文件夹的方式自行添加配色方案。你可以使用/jet/static/jet/css/themes/light-violet/文件夹作为一个例子(在Django JET代码中获得)。_variables.scss中包含了所有可以自行以的变量。你需要编译这个目录下的所有.scss文件才能使用自己的主题。

菜单压缩
Compact Or Not

如果您没有很多的应用或者模块却显示二级菜单可能会使您感到厌烦。关于这点您可以使用菜单的压缩模式它会在侧边菜单栏直接显示应用和模块而不再需要移动鼠标到应用上而再展示相关的模块。
JET_SIDE_MENU_COMPACT = True
这个选项默认为False。

自定义菜单
默认情况下JET将应用和它的模块按照字母表的顺序展示在侧边菜单栏。如果您想展示特定的应用和模块或者改变他们的顺序则可以使用JET_SIDE_MENU_CUSTOM_APPS来设置

JET_SIDE_MENU_CUSTOM_APPS = [
   ('core', [ # Each list element is a tuple with application name (app_label) and list of models
       'User',
       'MenuItem',
       'Block',
   ]),
   ('shops', [
       'Shop',
       'City',
       'MetroStation',
   ]),
   ('feedback', [
       'Feedback',
   ]),
]
如果您想展示某个应用的所有模块，可以使用__all__关键字

JET_SIDE_MENU_CUSTOM_APPS = [
    ('core', ['__all__']),
    ...
]
如果有多个管理站点并且想要给不同的站点指定不同的应用菜单，可以使用站点名称作为字典的key，将菜单的列表作为value来设定。

可以使用jet _custom_apps_example这个管理命令来生成JET_SIDE_MENU_CUSTOM_APPS的示例。这个示例中会包括所有的应用和模块。你可以这样使用它:
python manage.py jet_custom_apps_example

JET_CHANGE_FORM_SIBLING_LINKS
Paste_Image.png
在页面上添加前进或者后退的导航按钮可以让您在编辑完成后直接返回而不用先到变更列表。如果这个功能影响到了性能的话可以在设置中禁用。
JET_CHANGE_FORM_SIBLING_LINKS = True
默认情况下这个选项为True

JET_INDEX_DASHBOARD
设置管理主页的仪表盘用到的类。这个特性可以使用户建立自己的带有自定义模块和已经安装的布局的仪表盘。
JET_INDEX_DASHBOARD = 'jet.dashboard.dashboard.DefaultIndexDashboard'

JET_APP_INDEX_DASHBOARD
和JET_INDEX_DASHBOARD的功能相同，但是作用的是应用的页面。

JET_APP_INDEX_DASHBOARD = 'jet.dashboard.dashboard.DefaultAppIndexDashboard'
自动补全
默认情况下Django JET会对输入的查询条件提供全部可能的选项。当存在大量的可用的选项时这个结果并不一定是您所想要的。为此，Django JET允许您使用AJAX动态的加载结果。

配置
为了达成这个功能您需要做以下的事情：
<ul>
<li>指定需要使用AJAX方式查询的模块字段。将下面这个将可以使用AJAX查询的字段的字段名作为元组或列表返回的静态方法加入的模块中。

@staticmethod
def autocomplete_search_fields():
    return 'field1', 'field2'

# for single field

@staticmethod
def autocomplete_search_fields():
    return 'field1',
一个示例：

class Address(models.Model):
    name = models.CharField(_('name'), max_length=255)
    city = models.ForeignKey(City, verbose_name=_('city'), related_name='addresses')
    zip = models.IntegerField(_('zip/postal code'))

    class Meta:
        verbose_name = _('address')
        verbose_name_plural = _('addresses')
        unique_together = ('name', 'city')

    def __str__(self):
        return self.name

    @staticmethod
    def autocomplete_search_fields():
        return 'name', 'city__name'
<li>如果有外键关联的话可以使用自定义的AJAX筛选类jet.filters.RelatedFieldAjaxListFilter

from jet.filters import RelatedFieldAjaxListFilter

class PersonAdmin(admin.ModelAdmin):
    list_filter = (
        ...
        ('address', RelatedFieldAjaxListFilter),
    )
<li>现在管理界面所有的选择框会使用AJAX的方式来查询并展示可用的选项。
这些是为双外键和多对多关系的字段所做的。
</ul>

为某些字段去除自动补全
自动补全在大多数情况下是适用的，但有时你希望限制给某个空间提供的数据条数。这种情况下你可以通过下面这种方式禁用自动补全：

class YourForm(forms.ModelForm):
    def __init__(self, *args, **kwargs):
        super(YourForm, self).__init__(*args, **kwargs)
        if SOME_CASE(self.instance):
            self.fields['FIELD_NAME'].autocomplete = False
            self.fields['FIELD_NAME'].queryset = Model.queryset.some_filter()
紧凑内联
默认情况下Django管理界面提供了内联方式以供用户在同一页面编辑相关联的几种模型-StackedInline和TabularInline。在没有那么多对象时大部分使用的都是StackedInline的方式。如果相关联的模型很多，TabularInline可以帮助到您，但是当相关联的模型有很多字段的话并不方便关联他们。为了解决这个问题JET内置了CompactInline类。

 

Paste_Image.png
用法
CompactInline的工作方式就像Django内置的内联方式一样。只需要继承自jet.admin.CompactInline内联类即可。

from django.contrib import admin
from people.models import County, State, City
from jet.admin import CompactInline


class StateCountiesInline(admin.TabularInline):
    model = County
    extra = 1
    show_change_link = True


class StateCitiesInline(CompactInline):
    model = City
    extra = 1
    show_change_link = True


class StateAdmin(admin.ModelAdmin):
    inlines = (StateCountiesInline, StateCitiesInline)
过滤器
RelatedFieldAjaxListFilter
参见Autocomplete文档

django-admin-rangefilter
为了纠正django-admin-rangefilter包的兼容性问题，你需要使用JET的过滤器管理类jet.filter.DateRangeFilter来代替rangefilter.filter.DateRangeFilter

#from rangefilter.filter import DateRangeFilter
from jet.filters import DateRangeFilter


class MyUserAdmin(UserAdmin):
    ...
    list_filter = (
        ('date_joined', DateRangeFilter),
    )
    
 仪表盘
自定义仪表盘
Django JETDashboard尽可能的与django-admin -tools dashboard兼容以使django-admin-tools模块移植到Django JET上。大多数情况下可以在模块的模版中更改python的导入和移除之前的继承。
Dashboard相当于Dashboard类的实例，DashboardModule是其的子类。所有的自定义Dashboard应当继承jet.dashboard.dashboard.Dashboard并且使用init_with_context来填充控件。你应该将你的控件添加到children和available_children属性
进行下面的步骤之前要先确认您已安装了Dashboard

开始自定义Dashboard
<ul>
<li>在合适的位置创建dashboard.py文件(例如项目的根目录下)，编辑一下内容

from django.utils.translation import ugettext_lazy as _
from jet.dashboard import modules
from jet.dashboard.dashboard import Dashboard, AppIndexDashboard


class CustomIndexDashboard(Dashboard):
    columns = 3

    def init_with_context(self, context):
        self.available_children.append(modules.LinkList)
        self.children.append(modules.LinkList(
            _('Support'),
            children=[
                {
                    'title': _('Django documentation'),
                    'url': 'http://docs.djangoproject.com/',
                    'external': True,
                },
                {
                    'title': _('Django "django-users" mailing list'),
                    'url': 'http://groups.google.com/group/django-users',
                    'external': True,
                },
                {
                    'title': _('Django irc channel'),
                    'url': 'irc://irc.freenode.net/django',
                    'external': True,
                },
            ],
            column=0,
            order=0
        ))
<li>将settings.py文件的路径添加到创建的dashboard.py文件中(假设dashboard.py在项目根目录下)

JET_INDEX_DASHBOARD = 'dashboard.CustomIndexDashboard'
现在您有了一个只有一个LinkList控件的dashboard。如果您有另外一个dashboard并且已经渲染完成的话您需要重启dashboard。访问Dashboard Modules学习其他可以应用于自定义dashboard的组件或者从Custom Dashboard Module建立自己的控件。

Dashboard Modules
内置的Dashboard Modules
LinkList
 

Paste_Image.png
class jet.dashboard.modules.LinkList(title=None, children=[], **kwargs)
用法

from django.utils.translation import ugettext_lazy as _
from jet.dashboard import modules
from jet.dashboard.dashboard import Dashboard, AppIndexDashboard


class CustomIndexDashboard(Dashboard):
    columns = 3

    def init_with_context(self, context):
        self.available_children.append(modules.LinkList)
        self.children.append(modules.LinkList(
            _('Support'),
            children=[
                {
                    'title': _('Django documentation'),
                    'url': 'http://docs.djangoproject.com/',
                    'external': True,
                },
                {
                    'title': _('Django "django-users" mailing list'),
                    'url': 'http://groups.google.com/group/django-users',
                    'external': True,
                },
                {
                    'title': _('Django irc channel'),
                    'url': 'irc://irc.freenode.net/django',
                    'external': True,
                },
            ],
            column=0,
            order=0
        ))
children = []

children的属性包括链接，你可以把他作为参数传到构造器中以制作你自己的原装的链表。
children是一个字典的数组

[
     {
         'title': _('Django documentation'),
         'url': 'http://docs.djangoproject.com/',
         'external': True,
     },
     ...
]
layout = 'stacked'

指定控件的布局，允许值为stacked或inline

AppList
 

Paste_Image.png
class jet.dashboard.modules.AppList(title=None, model=None, context=None, **kwargs)

展示应用和其包含的模块以及链接。每个模块有响应的“创建”和“更改”链接
例子:

from django.utils.translation import ugettext_lazy as _
from jet.dashboard import modules
from jet.dashboard.dashboard import Dashboard, AppIndexDashboard


class CustomIndexDashboard(Dashboard):
    columns = 3

    def init_with_context(self, context):
        self.children.append(modules.AppList(
            _('Applications'),
            exclude=('auth.*',),
            column=0,
            order=0
        ))
exclude = None

指定不展示的模块，exclude是一个形如app_label.model的字符串的数组。支持用*来代表所有模块(auto.**)

models = None
同exclude

ModelList
 

Paste_Image.png
class jet.dashboard.modules.ModelList(title=None, model=None, context=None, **kwargs)

展示模块以及链接。每个模块有响应的“创建”和“更改”链接
例子：

from django.utils.translation import ugettext_lazy as _
from jet.dashboard import modules
from jet.dashboard.dashboard import Dashboard, AppIndexDashboard


class CustomIndexDashboard(Dashboard):
    columns = 3

    def init_with_context(self, context):
        self.children.append(modules.ModelList(
            _('Models'),
            exclude=('auth.*',),
            column=0,
            order=0
        ))
exclude = None

指定不展示的木块，同AppList的exclude

models = None

同上

RecentActions
 

Paste_Image.png
class jet.dashboard.modules.RecentActions(title=None, limit=10, **kwargs)

展示最近的管理操作，包含实体名，操作类型，操作人，日期
例子：

from django.utils.translation import ugettext_lazy as _
from jet.dashboard import modules
from jet.dashboard.dashboard import Dashboard, AppIndexDashboard


class CustomIndexDashboard(Dashboard):
    columns = 3

    def init_with_context(self, context):
        self.children.append(modules.RecentActions(
            _('Recent Actions'),
            10,
            column=0,
            order=0
        ))
exclude_list = None

不展示的模块，同AppList的exclude

include_list = None

同上

limit = 10

展示条数

Feed
 

Paste_Image.png
class jet.dashboard.modules.Feed(title=None, feed_url=None, limit=None, **kwargs)

展示订阅资讯以及以下信息：记录标题，日期，到完整内容的链接
例子：

from django.utils.translation import ugettext_lazy as _
from jet.dashboard import modules
from jet.dashboard.dashboard import Dashboard, AppIndexDashboard


class CustomIndexDashboard(Dashboard):
    columns = 3

    def init_with_context(self, context):
        self.children.append(modules.Feed(
            _('Latest Django News'),
            feed_url='http://www.djangoproject.com/rss/weblog/',
            limit=5,
            column=0,
            order=0
        ))
feed_url = None

订阅资讯的url(用户可自行修改)

limit = None

展示条数

Google Analytics Widgets
需要另外安装Google Analytics控件

 

Paste_Image.png
安装
<ul>
<li>安装Python包
pip install google-api-python-client==1.4.1
<ul>指定到Google Analytics client_secrets.json的路径(从Google获得)

JET_MODULE_GOOGLE_ANALYTICS_CLIENT_SECRETS_FILE = os.path.join(PROJECT_DIR, 'client_secrets.json')
<li>在urls.py文件的开头导入包

from jet.dashboard.dashboard_modules import google_analytics_views
</ul>
例子：

from django.utils.translation import ugettext_lazy as _
from jet.dashboard.dashboard import Dashboard, AppIndexDashboard
from jet.dashboard.dashboard_modules import google_analytics


class CustomIndexDashboard(Dashboard):
    columns = 3

    def init_with_context(self, context):
       self.available_children.append(google_analytics.GoogleAnalyticsVisitorsTotals)
       self.available_children.append(google_analytics.GoogleAnalyticsVisitorsChart)
       self.available_children.append(google_analytics.GoogleAnalyticsPeriodVisitors)
Yandex Metrika Widgets
Yandex Metrika Widgets需要额外安装

 

Paste_Image.png
安装
<ul>
<li>设置 Yandex Metrika的CLIENT_ID和CLIENT_SECRET(从 Yandex Metrika的网站获得)

JET_MODULE_YANDEX_METRIKA_CLIENT_ID = 'YANDEX_METRIKA_CLIENT_ID'
JET_MODULE_YANDEX_METRIKA_CLIENT_SECRET = 'YANDEX_METRIKA_CLIENT_SECRET'
<li>在urls.py文件中导入包

from jet.dashboard.dashboard_modules import yandex_metrika_views
例子

from django.utils.translation import ugettext_lazy as _
from jet.dashboard.dashboard import Dashboard, AppIndexDashboard
from jet.dashboard.dashboard_modules import yandex_metrika


class CustomIndexDashboard(Dashboard):
    columns = 3

    def init_with_context(self, context):
       self.available_children.append(yandex_metrika.YandexMetrikaVisitorsTotals)
       self.available_children.append(yandex_metrika.YandexMetrikaVisitorsChart)
       self.available_children.append(yandex_metrika.YandexMetrikaPeriodVisitors)
class jet.dashboard.dashboard_modules.yandex_metrika.YandexMetrikaVisitorsTotals(title=None, period=None, **kwargs)

Yandex Metrika 控件可以展示一段特定时期的访问人数。时间区间可以是今天、上周、上月、上季度、上一年等。

period = None

展示的时间区间，允许值为表示天数的整数

class jet.dashboard.dashboard_modules.yandex_metrika.YandexMetrikaVisitorsChart(title=None, period=None, show=None, group=None, **kwargs)

展示特定时间区间的访客/观众等人数的图表。

group=None

设置数据的单位，可能的值为day/week/month

period=None

天数的整数值

show=None

展示的数据，可能值为visitors/visits/page_views

class jet.dashboard.dashboard_modules.yandex_metrika.YandexMetrikaPeriodVisitors(title=None, period=None, group=None, **kwargs)

和上一个差不多:)

自定义Dashboard Module
按照以下步骤来创建你自己的dashboard module
<ul>
<li>继承Dashboard Module
<li>创建模块模版
<li>(可选)添加模块视图
</ul>
当然您也可以参照代码中在jet/dashboard/modules.py和jet/dashboard/dashboard_modules/位置内置的模块。

继承Dashboard Module
<ul>
<li>在你的django应用中创建dashboard modules文件dashboard_modules.py(或者其他名字)
<li>在创建的文件中创建dashboard modules类并继承dashboard module类。从http://jet.readthedocs.io/en/latest/dashboard_api.html#dashboard-module查看可用的模块属性。init_with_context方法相当于控件的构造方法，可以用来加载数据和穿实话模块的状态。你可以将数据存储到模块的字段中并传到模版中。
例子(dashboard_modules.py)

from jet.dashboard.modules import DashboardModule
from contact.models import Ticket


class RecentTickets(DashboardModule):
    title = 'Recent tickets'
    title_url = Ticket.get_admin_changelist_url()
    template = 'contact/dashboard_modules/recent_tickets.html'
    limit = 10

    def init_with_context(self, context):
        self.children = Ticket.objects.order_by('-date_add')[:self.limit]
<li>可选的，您可以在管理界面添加可自定义的设置和内容。为自定义设置的settings_form应设置，实现settings_dict和load_settings方法。如要自定义内容的话应该设置child_form, child_name和child_name_plural，并且store_children方法应返回True。你可以从数据库加载数据来验证__init__方法。
</ul>
 

Paste_Image.png
 

自定义设置和可编辑的列表的例子：

class LinkList(DashboardModule):
    title = 'Links'
    template = 'jet.dashboard/modules/link_list.html'
    layout = 'stacked'
    children = []
    settings_form = LinkListSettingsForm
    child_form = LinkListItemForm
    child_name = 'Link'
    child_name_plural = 'Links'

    def __init__(self, title=None, children=list(), **kwargs):
        children = list(map(self.parse_link, children))
        kwargs.update({'children': children})
        super(LinkList, self).__init__(title, **kwargs)

    def settings_dict(self):
        return {
            'layout': self.layout
        }

    def load_settings(self, settings):
        self.layout = settings.get('layout', self.layout)

    def store_children(self):
        return True

    def parse_link(self, link):
        if isinstance(link, (tuple, list)):
            link_dict = {'title': link[0], 'url': link[1]}
            if len(link) >= 3:
                link_dict['external'] = link[2]
            return link_dict
        elif isinstance(link, (dict,)):
            return link


class LinkListSettingsForm(forms.Form):
    layout = forms.ChoiceField(label='Layout', choices=(('stacked', 'Stacked'), ('inline', 'Inline')))


class LinkListItemForm(forms.Form):
    url = forms.CharField(label='URL')
    title = forms.CharField(label='Title')
    external = forms.BooleanField(label='External link', required=False)
创建模块模版
在模块类中指定的位置创建模版。模块的实例将传到这个模版中并被命名为module以便从其中获得数据

{% load humanize %}

<ul>
    {% for ticket in module.children %}
        <li>
            <span class="float-right">
                <span class="dim">
                    {{ ticket.date_add|naturalday }} <span class="icon-clock tooltip" title="{{ ticket.date_add }}"></span>
                </span>
            </span>

            {% if ticket.forwarded %}
                <span class="icon-tick" style="color: #8ecb8e;"></span>
            {% else %}
                <span class="icon-cross" style="color: #dba4a4;"></span>
            {% endif %}

            <a href="{{ ticket.get_admin_url }}">{{ ticket.name }}</a>
        </li>
    {% empty %}
        <li>
            Nothing to show
        </li>
    {% endfor %}
</ul>
添加模块视图(可选)
如果您的dashboard modules需要有自己的属兔您可以将他们按照下面的方法注册并且存储到文件中(如dashboard_modules_view.py)。

from django.conf.urls import url
from django.contrib import messages
from django.shortcuts import redirect
from jet.dashboard import dashboard
from core.utils.utils import DatabaseManager


def update_database(request):
    database_manager = DatabaseManager()
    database_manager.update_database()

    messages.success(request, 'Database was successfully updated')

    return redirect(request.META.get('HTTP_REFERER'))

# This method registers view's url
dashboard.urls.register_urls([
    url(
        r'^update_database/',
        update_database,
        name='update-database'
    ),
])
您应该在dashboard urls导入到主urls.py文件中之前导入这个文件

from django.conf import settings
from django.conf.urls import include, url
from django.contrib import admin

# Import dashboard module views
from core import dashboard_modules_views

urlpatterns = [
    url(r'^admin/', include(admin.site.urls)),
    url(r'^jet/', include('jet.urls', 'jet')),
    url(r'^jet/dashboard/', include('jet.dashboard.urls', 'jet-dashboard')),
    ...
]
之后你可以按照下面的方法reverse url
{% url "jet-dashboard:update-database" %}








