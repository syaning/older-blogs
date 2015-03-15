title: Django视图和URL配置
date: 2014-04-03 15:07:36
categories: Python
tags:
---
### 1. 简单视图和URL配置
```python
from django.http import HttpResponse
 
def hello(request):
    return HttpResponse('Hello world')
```
```python
urlpatterns = patterns('',
    (r'^hello/$', hello),
)
```
这里注意网站根目录可以使用^$（表示一个空字符串）进行配置，如：
```python
urlpatterns = patterns('',
    ('^$', my_homepage_view),
)
```
<!-- more -->

### 2. 动态URL配置
```python
from django.http import Http404, HttpResponse
import datetime
 
def hours_ahead(request, offset):
    try:
        offset = int(offset)
    except ValueError:
        raise Http404()
    dt = datetime.datetime.now() + datetime.timedelta(hours=offset)
    html = "<html><body>In %s hour(s), it will be %s.</body></html>" % (offset, dt)
    return HttpResponse(html)
```
```python
urlpatterns = patterns('',
    (r'^time/plus/(\d{1,2})/$', hours_ahead),
)
```

### 3. 视图字符串
通常的url配置里是导入视图，然后再url里配置视图对象。另外一种做法是不使用视图对象，而是使用视图字符串,这样就不必导入视图，例如：
```python
urlpatterns = patterns('',
    (r'^hello/$', 'mysite.views.hello'),
)
```
使用这种方法的时候，可以使用公共前缀URL，例如：
```python
urlpatterns = patterns('mysite.views',
    (r'^hello/$', 'hello'),
    (r'^time/$', 'current_datetime'),
)
```
当所需要的视图有多个前缀的时候，可以使用多个公共前缀，例如：
```python
urlpatterns = patterns('mysite.views',
    (r'^hello/$', 'hello'),
    (r'^time/$', 'current_datetime'),
)
 
urlpatterns += patterns('weblog.views',
    (r'^tag/(\w+)/$', 'tag'),
)
```

### 4. 调试模式特例
有时候可能需要在调试模式下修改 URLconf 的行为，这个时候只需要在运行时检查DEBUG配置项即可，例如：
```python
urlpatterns = patterns('',
    (r'^$', views.homepage),
    (r'^(\d{4})/([a‐z]{3})/$', views.archive_month),
)
 
if settings.DEBUG:
    urlpatterns += patterns('',
        (r'^debuginfo/$', views.debug),
    )
```

### 5. 使用关键字参数
先来看一个例子：
```python
urlpatterns = patterns('',
    (r'^articles/(\d{4})/$', views.year_archive),
    (r'^articles/(\d{4})/(\d{2})/$', views.month_archive),
)
```
在这里小括号括住的部分是传递给视图的参数，因为参数是未命名的，因此视图接受参数的时候是按照其位置顺序的，这样不仅缺乏灵活性，而且参数不具备可读性，下面我们使用关键字参数来解决这些问题，如：
```python
urlpatterns = patterns('',
    (r'^articles/(?P<year>\d{4})/$', views.year_archive),
    (r'^articles/(?P<year>\d{4})/(?P<month>\d{2})/$', views.month_archive),
)
```

### 6. 向视图函数传递额外参数
首先来看个例子：
```python
########## urls.py ##########
from django.conf.urls.defaults import *
from mysite import views
 
urlpatterns = patterns('',
    (r'^foo/$', views.foo_view),
    (r'^bar/$', views.bar_view),
)
 
########## views.py ##########
from django.shortcuts import render_to_response
from mysite.models import MyModel
 
def foo_view(request):
    m_list = MyModel.objects.filter(is_new=True)
    return render_to_response('template1.html', {'m_list': m_list})
 
def bar_view(request):
    m_list = MyModel.objects.filter(is_new=True)
    return render_to_response('template2.html', {'m_list': m_list})
```
我们会发现这两个视图基本上类似，只是使用了不同的模板，那么我们就希望能有一种方法来把这两个视图综合起来，从而避免代码的冗余，一个可行的解决方案是将两个url映射到同一个视图，同时将url作为参数传递给视图，然后通过在视图里判断url来确定使用的模板。这样做当然可行，但是假如我们修改了一个url，那么我们就必然得修改视图的代码，从而无法做到模块分离。此时通过将模板作为参数传递给视图是个很好的解决方法，如：
```python
########## urls.py ##########
from django.conf.urls.defaults import *
from mysite import views
 
urlpatterns = patterns('',
    (r'^foo/$', views.foobar_view, {'template_name': 'template1.html'}),
    (r'^bar/$', views.foobar_view, {'template_name': 'template2.html'}),
)
 
########## views.py ##########
from django.shortcuts import render_to_response
from mysite.models import MyModel
 
def foobar_view(request, template_name):
    m_list = MyModel.objects.filter(is_new=True)
    return render_to_response(template_name, {'m_list': m_list})
```
通过这种方法我们可以伪造URLconf参数，例如：
```python
urlpatterns = patterns('',
    (r'^mydata/birthday/$', views.my_view, {'month': 'jan', 'day': '06'}),
    (r'^mydata/(?P<month>\w{3})/(?P<day>\d\d)/$', views.my_view),
)
```
在这里 /mydata/birthday/ 完全等价于 /mydata/jan/06/ ，视图函数只关心获得参数，而不必关心这些参数是捕捉到的还是额外提供的。另外应当注意的是假如捕捉到的参数和额外的参数有着同样的参数名字，那么额外提供的参数将会被使用。

### 7. 通用视图
通用视图是基于额外参数的，我们先来看一个例子：
```python
########## urls.py ##########
from django.conf.urls.defaults import *
from mysite import views
 
urlpatterns = patterns('',
    (r'^events/$', views.event_list),
    (r'^blog/entries/$', views.entry_list),
)
########## views.py ##########
from django.shortcuts import render_to_response
from mysite.models import Event, BlogEntry
 
def event_list(request):
    obj_list = Event.objects.all()
    return render_to_response('mysite/event_list.html', {'event_list': obj_list})
 
def entry_list(request):
    obj_list = BlogEntry.objects.all()
    return render_to_response('mysite/blogentry_list.html', {'entry_list': obj_list})
```
我们会发现这两个视图非常类似，现在加入我们加入了一个users的url，那么就得在定义一个user_list的视图，这样并不是一个好的方式，我们可以通过以下方式进行高度的抽象创建通用视图，从而减少代码冗余：
```python
########## urls.py ##########
from django.conf.urls.defaults import *
from mysite import models, views
 
urlpatterns = patterns('',
    (r'^events/$', views.object_list, {'model': models.Event}),
    (r'^blog/entries/$', views.object_list, {'model': models.BlogEntry}),
)
########## views.py ##########
from django.shortcuts import render_to_response
 
def object_list(request, model):
    obj_list = model.objects.all()
    template_name = 'mysite/%s_list.html' % model.__name__.lower()
    return render_to_response(template_name, {'object_list': obj_list})
```

### 8. 缺省视图参数
有些情况下我们需要给视图提供缺省的参数值，例如：
```python
########## urls.py ##########
from django.conf.urls.defaults import *
from mysite import views
 
urlpatterns = patterns('',
    (r'^blog/$', views.page),
    (r'^blog/page(?P<num>\d+)/$', views.page),
)
########## views.py ##########
def page(request, num='1'):
    # do something
```
这里注意视图捕获的参数总是字符串，因此我们使用的是字符串‘1’而不是数字1.

### 9. 请求方法分支
在url与视图的配置中，特定的url映射到特定的视图，而没有考虑此事请求方法是GET还是POST，或者是其它。一种可行的做法是在视图处理函数中，通过 request.method 来判断请求所使用的方法，根据不同的方法做出不同的处理，但是这样将多个处理方法写在同一个视图里并不是一个好的做法，我们可以这样：
```python
########## views.py ##########
from django.http import Http404, HttpResponseRedirect
from django.shortcuts import render_to_response
 
def method_splitter(request, *args, **kwargs):
    get_view = kwargs.pop('GET', None)
    post_view = kwargs.pop('POST', None)
    if request.method == 'GET' and get_view is not None:
        return get_view(request, *args, **kwargs)
    elif request.method == 'POST' and post_view is not None:
        return post_view(request, *args, **kwargs)
    raise Http404
 
def some_page_get(request):
    assert request.method == 'GET'
    do_something_for_get()
    return render_to_response('page.html')
 
def some_page_post(request):
    assert request.method == 'POST'
    do_something_for_post()
    return HttpResponseRedirect('/someurl/')
########## urls.py ##########
from django.conf.urls.defaults import *
from mysite import views
 
urlpatterns = patterns('',
(r'^somepage/$', views.method_splitter, {'GET': views.some_page_get, 'POST': views.some_page_p}),
)
```

### 10. 包装视图函数
先试想一个场景，假如有些视图需要用户登录后才能使用，那么再这些视图的开始都需要检测用户的登录状态，这样就造成了代码冗余，此时我们可以通过包装视图函数来解决，例如：
```python
def requires_login(view):
    def new_view(request, *args, **kwargs):
        if not request.user.is_authenticated():
            return HttpResponseRedirect('/accounts/login/')
        return view(request, *args, **kwargs)
    return new_view
```
函数requires_login传入一个视图函数view，然后返回一个新的视图函数new_view，这个新的视图函数new_view在函数requires_login内定义，处理request.user.is_authenticated()这个验证,从而决定是否执行原来的view函数。之后我们可以这样配置：
```python
urlpatterns = patterns('',
    (r'^view1/$', requires_login(my_view1)),
    (r'^view2/$', requires_login(my_view2)),
    (r'^view3/$', requires_login(my_view3)),
)
```

### 11. 包含其它URLconf
一个URLconf可以包含其它的URLconf模块，例如：
```python
urlpatterns = patterns('',
    (r'^weblog/', include('mysite.blog.urls')),
    (r'^photos/', include('mysite.photos.urls')),
    (r'^about/$', 'mysite.views.about'),
)
```
这里要注意有include的url配置没有$作为结尾。同样，我们也可以传递额外的参数给使用了include的配置。