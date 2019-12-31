
# Django  

将模板渲染放到一个独立的服务器，就是设立一个独立的渲染解释器在第三方的服务器，然后将渲染用到的参数  
使用celery或消息队列传递到异步渲染服务器 然后传回，


主目录名称与项目名称一致  
四大文件  
init   初始化文件
urls   路径  基础路由配置文件 允许正则
settings  配置设置
wsgi  通用网管接口
应用是网站独立的一个逻辑模块  单独提供一个逻辑模块的服务

# 模板  
```jinja

加载方式  loader render  render
 变量
{{var}} 
 标签
{% for %}{%endfor%}
{% comment %} 。。。 {% comment %}
{% load static %}
{% static ‘’ %}
## 过滤器

{{变量|过滤器:参数}} 非常多
{{value|upper}}  
{{value|lower}}  
{{value|add：num}}  累加
{{value|floatformat：n}}  
{{value|truncatechar：n}}  保留至n位字符  添加省略号   包含点点点

继承
父模板中
{%block blockname%}{%endblock%}
{% extends 父模板名称 %}
{%block  改写block%}
{%endblock%}

```

django 的url参数是可以自定义convert的  
在多个APP中有可能产生同名的url为了避免翻转url是产生混淆  可以使用应用命名空间  django中存在应用命名空间与实例命名空间两种   
在APP的urls文件中定义app_name=“appname” 即可  
实例命名空间应用与统一url导向不同的APP中为了使用不同的实例APP  在主urls文件找那个的include中填入namespace参数即可  

实例命名空间的前提是要有应用命名空间

include函数使用方法
include（("app.urls","appnamespace"),namespace=None)  后边的是实例命名空间
re_path 与path函数  path不支持正则表达

如果想要添加的查询字符串的参数  则必须受到凭借
```jinja
在DTL中时无法使用break 或者continue语句的

login_url = reverse（“login”） + "?next=/"  
reverse（“urlname”,kwargs={"url_arg":"arg"}） 假设url中有ur添加查询字符串的参  
l参数  name需要使用这种字典的方式将参数传进去  
DTL   django template language  

for 标签
{%for person in persons%}
{%endfor%}

{%for key,item in dic.items%}
{{forloop.counter}}输出当前循环次数 1开始
{{forloop.counter0}}输出当前循环次数 0开始
{{forloop.revcounter}}反向输出当前循环次数 1开始
{{forloop.revcounter0}}反向输出当前循环次数 0开始
{{forloop.first}} 循环的第一行  需要结合  if else使用
{{forloop.laste}} 循环的最后一行
{{forloop.parentloop}} 嵌套循环中上一层循环的值
{%endfor%}

{%for i in list%}
{%endfor %}

{% for page in book %}
{%empty%}
若page为空
{%endfor%}

autoescape标签
{%autoescape False %}  关闭自动转义  会将<> 转义为&lt &gt   
{% endautoscape%} 如果不知道的情况下不要随便关闭  这样才不会有xss漏洞 如果漏洞是可以信任的再开启这个标签来关闭转义

verbatim 标签   避免转义为模板语言   有可能需要其他的模板引擎 例如前段模板引擎  art-template  语法重合  纯js模板语言  这样会避免二者重合
{%verbatim%}    
{%endverbatim%}

include标签
可以直接读并传递取父模板的变量

{%include "part.html" with username="vlaue" %} 将变量传入到模板中


DTL过滤器
在模板中的一些函数，但是无法使用圆括号调用传参  因此使用过滤器来实现一些参数
{{ value|filter：arg }}

add过滤器
{{value|add:"2"}}  可以实现列表的拼接

cut 过滤器 类似python replace 找到字符串删除掉  
{{value|cut:""}}

date过滤器
{{birthday|date:"Y/m/d"}} 将输出2018/02/01
Y 四位年份m两位月份 n一位月份 d两位天 j一位天 g一位小时 h两位小时 G一位24小时 H两位24小时 i两位分 s两位秒 I一位秒 S一位秒

default过滤器
{{value|default:""}} 设置一个默认的值
default_if_none 避免空字典 空列表也被认为空值
{{value|default_if_none:""}}只有value为none时才会将实际的值更换为默认值

{{vlaue|first}} 返回列表元祖字典的第一个元素
{{value|last}} 返回最后一个元素

floatformat 过滤器四舍五入浮点数  如果没有传参就 数字后全是0 就保留整数 传参的话就保留几位
{{value|floatformat:3}} 保留小数点三位数字

join 过滤器 将列表元祖变为字符串 可以指定分隔符
{{value|join:"/"}} 链接列表以/为分隔符

length 过滤器 返回一切可以范围len的数据
{{value|length}} 返回长度

upper 过滤器
lower 过滤器
randm 过滤器 随机从列表元祖选择一个值

safe 过滤器  类似autoescape 表示相信
{{value|safe}} 也是关闭自动转义

slice 过滤器
{{value|slice:"2::"}}

stringtags 过滤器 删除字符串中的所有的html标签
{{value|stringtags}}

truncatechars 过滤器 最多显示多少字符 超过显示。。。
{{value|truncatechars:"5"}} 显示不要超过2个字  本身。。。也是三个字

truncatecharts_html 过滤器 类似truncatechars 但是不会切割html标签

from django.template.defaultfilter import 

```
1. **django 自定义模板锅炉器**
+ 在app包中创建python package “templatetags” 规定如此取名
- 创建一个py文件  没有规定 例如my_filters.py
```py
form django import template
register = template.Library()

def greet(value，word=None)#有参数或者没有 第一个是被过滤 的值 参数最多有两个
        return value+word
register.filter("greet",greet)
#然后将APP注册到settings中  即可使用
```
1. **django 自定义模板锅炉器**
+ 在app包中创建python package “templatetags” 规定如此取名
- 创建一个py文件  没有规定 例如my_filters.py
```py
form django import template
register = template.Library()
register.filter("filter_name") #使用装饰器 不写名的话就是函数名字
def greet(value，word=None)#有参数或者没有 第一个是被过滤 的值 参数最多有两个
        return value+word
#然后将APP注册到settings中  即可使用

register.filter("filter_name")
def time_since(vlaue):
        if not isinstance(value,datetime):
                return value
        now = datetime.now()
        timestamp = (now-vlaue).total_seconds()
```
```jinja
需要在使用前加载
{%load my_filter%}

{{value|greet:"test"}}
```
**模板结构优化**  
+ 结构分开
```jinja
{% include "header.html" %}
        <div calss="content"></div>
{% include "footer.html" %}
```
+ 分为母版 字板
```jinja
{%entends “base.html”%} 
{%block content%}
{%block.super%} 将父模板中的内容输出不会自动换行  想要换行需要添加标签 <p>{%block.super%} </p>
{%endblock%}

子模板代码必须在block中实现  在block外边实现的话不会渲染  给子模板传参的话父模板可以直接使用
```
**加载静态文件**  
```jinja
在多个APP下有多个同名文件时 django建议在当前APP的static下建立文件夹 文件夹名称与本APP名称一致 然后将文件放到其中
使用static前需要先使用
{%load static%}
{%static "appname/log.png"%}
```
CTRL+SHIFT+R 不使用缓存加载页面
```py
#可以在全局设置中设置
STATICFILES_DIRS= {
        os.path.join(BASE_DIR,"static")
}#一般将所有的静态文件将所有的文件存放到一个static目录中去
```
static标签每次使用都需要load django提供方法来将static变为内置标签
在settings 添加如下配置
```py
#TEMPLATES/OPTIONS 下添加 
"builtins":['django.templatestags.static']
```
如果不想注册 django.contrib.staticfilesapp的话 在setting中注释掉
然后手动注册staticurl
```py
#在url文件中
from django.conf import settings
from django.conf.urls.static impoort static
        urlpatterns = [
                path("book",book_view.func)

        ] + static(settings,STATIC_URL,document_root=settings.STATICFILES_DIRS[0] 
        #本身毫无意义 可以加载静态文件 为什么使用+号  是因为static函数返回是列表  因此需要相加

        
```


## 静态路径
在settings中设置静态文件访问路径   在最下方  STATIC_URL = '/static/'  
如果访问路径是/static然后到静态文件的存储路径中找文件不走路由  
在浏览器中通过那个路径能够找到静态文件  
设置静态文件的存储路径   STATICFILES_DIRS=(os.path.join(BASE_DIR，'static'))#base_dir 当前项目绝对路径  
在项目根目录创建static目录用于保存静态文件
在每个应用中也可以创建static目录，用于保存静态文件们   优先使用APP   一般存放在一起
  

指定静态文件在服务器上的存储位置    



```html
{% load static %}  
{%static "staticname"%} 使用该标签访问静态资源
<img src="http://127.0.0.1:8000/static/images/a.jpg"> 方式简单
<img src="{% static 'images/a.jpg'%}">   该方式较为灵活
```


当前使用版本为Django2.1版本  因此在当前版本中与之前有不同出入  
在Django2.1版本中  url函数被替换成path函数  而且不在支持正则表达  如果想要使用正则表达需要使用re_path函数

```python
#在二级路由（也即app的urls文件中），在urlpatterns后，应该加上app_name='app_name(你的app名称)'，否则会报错。想·
from django.urls import path,re_path
urlpatterns = [
    path(r'admin/', admin.site.urls),
    re_path(r'appt01/(?P<num>\d{2})', show1),#此处参数要传到views  test（request，arg01） 
    #使用re_path达到正则表达的目的
    path(r'appt01/<num>', show1),#path的传参方法
]       




#url 函数（regex，views，kwargs，name）  name用来起别名 用于反向解析
#例 url（r'music/',music,kwargs=None,name="name"）
#反向解析在 模板中与在视图上   
#django重定向的特征 从服务器端向新的地址请求，模板上的请求不同
'''1 基本解析 没有参数的普通地址
{% url urlbieming %}
{% url urlbieming  ‘arg1’ ‘arg2’%}
 re_path(r'(\d{2})/(\w+)/$', show1),
#re_path 函数

在视图中进行反向解析的 基本解析
url = reverse（‘别名’）
带参数的解析
url = reverse（‘别名’，args=（args1，args2））
url 在django中可以跟参数



'''




```
# 模型  
配置数据库在setting中：
```py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django',#数据库的名字
        'USER': 'root',
        'PASSWORD': 'root',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }#django中连接mysql数据库会依赖MySQLdb 需要用pymysql.install_as_mysqldb()  在项目的主目录的__init__.py中执行安装mysqldb
}
```
**在django中使用原生SQL**
```py
from django.db import connection
cousor = connection.cousor()
cousor.execute("SQL")
rows = cousor.fetchall()
for i in rows:
    print(i)
```




在Django 中创建模型  

对象关系映射三大特征
数据表与类的映射

编程语言数据类型与表字段之前的映射

关系映射  将表与表和类与类之前的关系形成映射


关于数据库同步操作   
首次连接数据库启动服务是创建  django_migrations 表
./manage.py makemigrations 负责将每个应用下的models.py文件生成一个数据库的中间文件 并将中间文件保存到migrations的目录中  
./manage.py migrate 将之前的中间文件同步到数据库中 如果直接执行会创建django本身的数据库表到数据库中
虽然在models文件中还没有写关系映射 但是在django自身内部就有一套系统表的migrations文件存在  此时是优先创建django默认的表



# Models

models中的每个class都成为模型类或者实体类（entry）数据库一行就是一条记录  就是一个实体  实体完整性 约束一行的唯一性 

```python

class name(models.Model):
        shuxingming = models.字段类型（字段选项  空 默认值等）

"""
#字段类型
CharField（maxlength=50 必须有）
IntergerField（）
FloatField（）
DecimalField（max_digits=7,decimal_places=2）定点位小数
EmailField()
URLField()
ImagesField()
BolleanField()
DateTimeField()


default
null  默认不为空
db_column 给列取一个名


版本切换
./manage.py migrate 默认执行最新的makemigrations生成的数据库中间文件
./manage.py migrate  应用名称  版本号
通过数据库字段导出models类
./manage.py inspectdb > 文件名.py


Entry.objects.create(属性=值，属性=值)  增加记录  向实体创建记录   返回一个整数值 返回影响行数



"""
```

## 模型的增加
```python

增加数据
方式1
每个实体对象都集成至models.Model  因此有很多属性  Entry为类
Entry.object.create(属性=值，属性=值，属性=值) 自动提交  无序考虑事务  返回值为创建好的实体对象
返回为当前的所有属性

方式2
obj = Entry(name='guoqi',age=20)
obj.save()

方式3
dic = {"name":"asd","age":30}
obj1 = classname(**dic)
obj1.save()
```

##  查询
```python
Entry.objects.get(id=5)# 只能用于有且仅有一条结果的情况
Entry.object.filter()
# 1 属性作为条件   id=5，name="test"  多条件就逗号隔开






```






[auth]  
    changepassword  
    createsuperuser  

[contenttypes]  
    remove_stale_contenttypes  

[django]  
    check  
    compilemessages  
    createcachetable  
    dbshell  
    diffsettings  
    dumpdata  
    flush  
    inspectdb  
    loaddata  
    makemessages  
    makemigrations  
    migrate  
    sendtestemail  
    shell  
    showmigrations  
    sqlflush  
    sqlmigrate  
    sqlsequencereset  
    squashmigrations  
    startapp  
    startproject  
    test  
    testserver  

[sessions]  
    clearsessions  

[staticfiles]  
    collectstatic  
    findstatic  
    runserver  启动服务








# 服务器与web
服务器  存储web所需的信息（html，img，js，css，video，audio）  
        处理用户请求（request）和响应（response）  
        执行服务器端的程序：查找数据库    
web要放在服务器上才能被用户访问
框架  为了解决开放新问题而存在的一种结构  提供基本功能，在基本功能之上搭建属于自己的操作
python  web三大框架  Django  flask  tornado



# django 中间件
每个中间件最多写五个方法
process_request
process_response
process_view
process_exception
process_render_template
django版本不同执行顺序不同  请求从上到下  响应从下到上
首先挨个执行process_request 然后进行url匹配  然后返回去执行 process_view  然后执行视图函数   然后执行process_response函数 如果报错 挨个报错执行process——exception  如果使用render process_render_template
  
  用中间件做过什么 权限，用户登录认证，无需在每个视图函数执行装饰器，以及csrftoken 该验证时鞋子啊process_view里边的  在process_request和process_view里边都可以，但是需要验证view是否免除csrf因此需要将该函数写在process_view中，去请求体或者和cookie中获取token完成校验
```py
from django.views.decorators.csrf import csrf_exempt
@csrf_exempt #免除csrf
@csrf_protect#该函数需要csrf
def testview():
    pass

```



# django View
cbv 是基于反射实现的 根据method的不同来是实现内部的方法
## CBV 开发
```py
#基于CBV实现restful模式的api接口
from django.views import View
class StudentView(VIew):
    def get(self,request,*args,**kargs):
        return HttpResponse("GET")
    def post
    def get
    def delete
 #urls.py
 url(r"student/",views.StudentsView.as_view())

#CBV中方法使用或者放弃csrf 在cbv 的函数上添加上边的装饰器是不行的@csrf_exempt #免除csrf  @csrf_protect#该函数需要csrf
#需要导入from django.views.decorators import method_decorator
#使用 @method_decorator（csrf_exempt）装饰CBV的dispatch方法来实现   需要重写dispatch方法  内部调用super
@method_decorator（csrf_exempt，name=“dispatch”）
class studentview（）：
    pass
```


# django restful
根据method的不同执行不同的操作 十大规范  
1. 建议使用https协议  
2. 域名建议域名以   开始
        https://api.source.com   容易被同源策略阻挡 数据是可以访问的  但是会被浏览器组织，需要解决跨域的问题
        https://example.com/api  
        

3. 版本 https://example.com/api/v1    
 https://example.com/api/v2  

4. 路径一切均是资源   使用名词 可以是复数
5. 过滤  用url参数方式传递过滤参数 搜索条件
6. 状态码 利用状态码给前端做提示的但是状态码是不够的，因此一般不仅仅使用状态码，而是结合code来使用 code就是自己定义的状态码
    可以根据要求修改状态码  前段一般对于状态码关注度不高，因此一般只关心code，但是有的人写状态   所以一般是状态码加code
    
7. 错误处理一般状态码是4XX
8. 返回结果  针对不同操作返回不同结果
Method
    GET /order
    返回资源对象的列表
    GET /order/1
    返回单个资源对象
    POST /order
    返回新生成的资源对象
    PUT /order/1
    返回完整的资源对象
    PATCH /order/1
    返回完整的资源对象
    DELETE /order/1
    返回空文档

9. hypermedialink 可以生成 下一次详细访问的url

谈谈对restful规范的认识
    使用前使用后的对比，定义规范来服务前段后端 在api上体现操作
    讲故事  具体使用情况与实际产生效果  后来用没用  等等 结合内部一些知识

```py
#CBV进来先执行dispatch
from rest_framework imort APIView

```
## 关于restframework 
1. 认证
# 内置认证
```py
from rest_framework.exceptions import  AuthenticationFailed
from rest_framework.authentication import BaseAuthentication
from django.shortcuts import HttpResponse
#这是单独开发一个认证类   然后需要将该认证类注册到settings中 就成了全局的认证
class Auth(BaseAuthentication):
    def authenticate(self, request):
       # try:
       if not request.GET.get("token"):
           print("not token")
           raise AuthenticationFailed("faild")
       # HttpResponse
       print("authentication pass")
       return (request._request.user,"123")
        # raise NotImplementedError(".authenticate() must be overridden.")
       # except Exception as e:
       #      return HttpResponse("no token")

    def authenticate_header(self, request):
        """这个方法是认证时报之后返回的响应头

        """
        return "Basic faild"
    返回值有三种  none   下一个认证执行
                抛出异常 AuthenticationFailed
                返回元祖 （e1,e2）第一个元素复制给request.user  第二个元素于赋值给 request.auth

#其中有很多内置的的认证类 
    #在自己写认证类的时候继承BaseAuthentication
    请求流程 1   dispatch 先执行封装request 获取定义的认证类 再利用列表生成式创建对象
            2    执行initial 

```

2. 权限  
**权限划分**  划分用户和管理
    可以在view中根据request.user.user_type划分权限
    如何为不同的视图赋予不同的权限访问
```py
from rest_framework.permissions import  BasePermission
class BasePermission(metaclass=BasePermissionMetaclass):
    """
    A base class from which all permission classes should inherit.
    """

    def has_permission(self, request, view):
        """
        Return `True` if permission is granted, `False` otherwise.
        """
        return True

    def has_object_permission(self, request, view, obj):
        """
        Return `True` if permission is granted, `False` otherwise.
        """
        return True

```
返回值有三种 主要是True或者False 也可以抛出异常但是一般返回False就行了


3. 节流 访问频率控制
# 节流

```py
class BaseThrottle:
    """
    Rate throttling of requests.
    """

    def allow_request(self, request, view):
        """
        Return `True` if the request should be allowed, `False` otherwise.
        """
        raise NotImplementedError('.allow_request() must be overridden')

    def get_ident(self, request):
        """
        Identify the machine making the request by parsing HTTP_X_FORWARDED_FOR
        if present and number of proxies is > 0. If not use all of
        HTTP_X_FORWARDED_FOR if it is available, if not use REMOTE_ADDR.
        """
        xff = request.META.get('HTTP_X_FORWARDED_FOR')
        remote_addr = request.META.get('REMOTE_ADDR')
        num_proxies = api_settings.NUM_PROXIES

        if num_proxies is not None:
            if num_proxies == 0 or xff is None:
                return remote_addr
            addrs = xff.split(',')
            client_addr = addrs[-min(num_proxies, len(addrs))]
            return client_addr.strip()

        return ''.join(xff.split()) if xff else remote_addr

    def wait(self):
        """
        Optionally, return a recommended number of seconds to wait before
        the next request.
        """
        return None


class SimpleRateThrottle(BaseThrottle):
    #需要实现一个get_cache_key 方法
    #然后再settings中添加一个"DEFAULT_THROTTLE_RATES":{"throtle":"3/m"}
    #要在继承的子类中添加一个scope
    """
    A simple cache implementation, that only requires `.get_cache_key()`
    to be overridden.

    The rate (requests / seconds) is set by a `rate` attribute on the View
    class.  The attribute is a string of the form 'number_of_requests/period'.

    Period should be one of: ('s', 'sec', 'm', 'min', 'h', 'hour', 'd', 'day')

    Previous request information used for throttling is stored in the cache.
    """
    cache = default_cache
    timer = time.time
    cache_format = 'throttle_%(scope)s_%(ident)s'
    scope = None
    THROTTLE_RATES = api_settings.DEFAULT_THROTTLE_RATES

    def __init__(self):
        if not getattr(self, 'rate', None):
            self.rate = self.get_rate()
        self.num_requests, self.duration = self.parse_rate(self.rate)

    def get_cache_key(self, request, view):
        """
        Should return a unique cache-key which can be used for throttling.
        Must be overridden.

        May return `None` if the request should not be throttled.
        """
        raise NotImplementedError('.get_cache_key() must be overridden')

    def get_rate(self):
        """
        Determine the string representation of the allowed request rate.
        """
        if not getattr(self, 'scope', None):
            msg = ("You must set either `.scope` or `.rate` for '%s' throttle" %
                   self.__class__.__name__)
            raise ImproperlyConfigured(msg)

        try:
            return self.THROTTLE_RATES[self.scope]
        except KeyError:
            msg = "No default throttle rate set for '%s' scope" % self.scope
            raise ImproperlyConfigured(msg)

    def parse_rate(self, rate):
        """
        Given the request rate string, return a two tuple of:
        <allowed number of requests>, <period of time in seconds>
        """
        if rate is None:
            return (None, None)
        num, period = rate.split('/')
        num_requests = int(num)
        duration = {'s': 1, 'm': 60, 'h': 3600, 'd': 86400}[period[0]]
        return (num_requests, duration)

    def allow_request(self, request, view):
        """
        Implement the check to see if the request should be throttled.

        On success calls `throttle_success`.
        On failure calls `throttle_failure`.
        """
        if self.rate is None:
            return True

        self.key = self.get_cache_key(request, view)
        if self.key is None:
            return True

        self.history = self.cache.get(self.key, [])
        self.now = self.timer()

        # Drop any requests from the history which have now passed the
        # throttle duration
        while self.history and self.history[-1] <= self.now - self.duration:
            self.history.pop()
        if len(self.history) >= self.num_requests:
            return self.throttle_failure()
        return self.throttle_success()

    def throttle_success(self):
        """
        Inserts the current request's timestamp along with the key
        into the cache.
        """
        self.history.insert(0, self.now)
        self.cache.set(self.key, self.history, self.duration)
        return True

    def throttle_failure(self):
        """
        Called when a request to the API has failed due to throttling.
        """
        return False

    def wait(self):
        """
        Returns the recommended next request time in seconds.
        """
        if self.history:
            remaining_duration = self.duration - (self.now - self.history[-1])
        else:
            remaining_duration = self.duration

        available_requests = self.num_requests - len(self.history) + 1
        if available_requests <= 0:
            return None

        return remaining_duration / float(available_requests)


```
中间件 
csrf原理
rest10个规范
面向对象
django请求声明周期
restframework django请求声明周期 dispatch
关于四个 rest_framework 认证流程  request封装   
                        权限流程
                        节流流程

4. 版本

# 版本  
```py
#verison 基类
class BaseVersioning:
    default_version = api_settings.DEFAULT_VERSION
    allowed_versions = api_settings.ALLOWED_VERSIONS
    version_param = api_settings.VERSION_PARAM

    def determine_version(self, request, *args, **kwargs):
        msg = '{cls}.determine_version() must be implemented.'
        raise NotImplementedError(msg.format(
            cls=self.__class__.__name__
        ))

    def reverse(self, viewname, args=None, kwargs=None, request=None, format=None, **extra):
        return _reverse(viewname, args, kwargs, request, format, **extra)

    def is_allowed_version(self, version):
        if not self.allowed_versions:
            return True
        return ((version is not None and version == self.default_version) or
                (version in self.allowed_versions))


class QueryParameterVersioning(BaseVersioning):#官方的version控制
    """
    GET /something/?version=0.1 HTTP/1.1
    Host: example.com
    Accept: application/json
    """
    #version_param
    #default_version 在配置文件中编写
    #is_allowed_version确定是否属于允许的版本
    #上述三个配置项要在配置文件中进行配置
    #一般不使用将version传参
    #而是使用路径中传参
    invalid_version_message = _('Invalid version in query parameter.')

    def determine_version(self, request, *args, **kwargs):
        version = request.query_params.get(self.version_param, self.default_version)#default_version 在配置文件中编写
        if not self.is_allowed_version(version):#is_allowed_version确定是否属于允许的版本
            raise exceptions.NotFound(self.invalid_version_message)
        return version

    def reverse(self, viewname, args=None, kwargs=None, request=None, format=None, **extra):
        url = super().reverse(
            viewname, args, kwargs, request, format, **extra
        )
        if request.version is not None:
            return replace_query_param(url, self.version_param, request.version)
        return url

class URLPathVersioning(BaseVersioning):#该方法为推荐的version控制方法
    """
    To the client this is the same style as `NamespaceVersioning`.
    The difference is in the backend - this implementation uses
    Django's URL keyword arguments to determine the version.

    An example URL conf for two views that accept two different versions.

    urlpatterns = [
        url(r'^(?P<version>[v1|v2]+)/users/$', users_list, name='users-list'),
        url(r'^(?P<version>[v1|v2]+)/users/(?P<pk>[0-9]+)/$', users_detail, name='users-detail')
    ]

    GET /1.0/something/ HTTP/1.1
    Host: example.com
    Accept: application/json
    """
    invalid_version_message = _('Invalid version in URL path.')

    def determine_version(self, request, *args, **kwargs):#获取或者返回version
        version = kwargs.get(self.version_param, self.default_version)
        if version is None:
            version = self.default_version

        if not self.is_allowed_version(version):
            raise exceptions.NotFound(self.invalid_version_message)
        return version

    def reverse(self, viewname, args=None, kwargs=None, request=None, format=None, **extra):#用于反向生成url
        if request.version is not None:
            kwargs = {} if (kwargs is None) else kwargs
            kwargs[self.version_param] = request.version

        return super().reverse(
            viewname, args, kwargs, request, format, **extra
        )


class ParamVersion():# 自定义的version
    def determine_version(self,request,*args,**kwargs):
        version = request.query_params.get("version")
        return version


class Stuview(APIView):#一般使用官方定义的版本就可以  多数情况下使用urlpath 使用是在配置文件配置就行 在路由系统中需要添加正则匹配，视图中需要获取version就行 request.version  或者获取对象 request.versioning_schema 通过对象进行反向生成url 反向生成对象中要进行传参时可以直接将request传进去
    authentication_classes = []
    permission_classes = []
    throttle_classes = [Simplethrottle,]
    versioning_class = ParamVersion
    def get(self,request):
        print(request._request.user)
        print(request.version)
        return  JsonResponse({"di":("information",None)})


```

# 解析器 解析数据  
    发请求 请求体  request.body有值 request.post不一定有值 
    如果请求头中的content-type application.x-www-form-urlencodeed request.POST中才有值 采取request.body中解析数据
    数据格式也是有要求的  请求头有要求  需要是字典类型 
    form表达提交就是上述的请求类型
    ajax提交
```js
//ajax可以定制请求头
$.ajax(
    {
        headers：{"contents-Type" = 'application/json'}
        url : ""
        type:POST,
        data:{name:alex}
    }
)
//此时request.body有值，POST没有值
///此时需要去request.body中获取数据  进行二进制转换  然后再json.loads()
```

## restframework 的数据解析器
```py
from rest_framework.parsers import  JSONParser

class Stuview(APIView):
   
    parser_classes = [JSONParser,]#允许jspn的值
    def get(self,request):

        print(request.body)
        return  JsonResponse({"di":("information",None)})
     def post(self,request):
        parser_classes = [FormParser,]#可以处理多种form形式
        print(request.data)
        #
        # 
        #只有使用时才会调用解析器 获取请求头 获取请求体  根据请求头和支持的请求头现比较  
        # 哪个分析器符合要求交由哪个分析器处理数据
        # print(request._request.body)
        # print(request.body)
        return  JsonResponse({"di":("information",None)})
        #在使用时直接全局配置即可
```
熟悉的请求头  状态码  请求方法


# 序列化 
## queryset 序列化
**没有表关联时的序列化**

```py
class role(models.Model):
    role = models.CharField(max_length=50)
    name = models.CharField(max_length=50)

class Serializer_my(serializers.Serializer):
    id = serializers.IntegerField()
    role = serializers.CharField()
    name = serializers.CharField()

class Stuview(APIView):
    authentication_classes = []
    permission_classes = []
    # throttle_classes = [Simplethrottle,]#节流控制
    # versioning_class = ParamVersion
    # parser_classes = [JSONParser,FormParser]#可以处理多种form形式
    parser_classes = [FormParser,]#可以处理多种form形式

    def post(self,request):

        print(request.data)#只有使用时才会调用解析器 获取请求头 获取请求体  根据请求头和支持的请求头现比较
        # 哪个分析器符合要求交由哪个分析器处理数据
        result = models.role.objects.all()#获取模型数据
        return_result = Serializer_my(instance=result,many=True)
        ret = json.dumps(return_result.data)
        return  HttpResponse(ret)
        # return  JsonResponse({"di":("information",None)})

 class Serializer_Foreinke(serializers.Serializer):
    role = serializers.SerializerMethodField()
    id = serializers.IntegerField()
    uname = serializers.CharField()
    uage = serializers.IntegerField()
    def get_role(self,row):
        return [{"id":1,"age":20}]#处理manytomany时要使用的方法是这个SerializerMethodField 下边定义get来头就行    
        #{"role": [{"id": 1, "age": 20}], "id": 1, "uname": "guoqi", "uage": 20} 返回值   

```
---
```py
########################  Models ########################################
class Role(models.Model):
    role = models.CharField(max_length=50)
    name = models.CharField(max_length=50)
class Group(models.Model):
    group = models.CharField(max_length=50,null=False)
class User(models.Model):
    uname = models.CharField(max_length=50)
    uage = models.IntegerField()
    role = models.ManyToManyField("Role")
    group = models.ForeignKey("Group",on_delete=True)

############################## Serializer  ######################################################

#!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!   特殊方法  ！！！！！！！！！！！！！！！！！
class myfiled(serializers.CharField):
    def to_representation(self, value):
        return myProcess(value)  # 定义自己的字段处理犯方法


#！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！

class Serializer_Foreinke(serializers.Serializer):
    role = serializers.SerializerMethodField()
    id = serializers.IntegerField()
    uname = serializers.CharField()
    uage = serializers.IntegerField()
    def get_role(self,row):
        return [{"role_name":i.role,"role_name_name":i.name}for i in row.role.all()]
class Serializer_model(serializers.ModelSerializer):
    group = serializers.CharField(source="group.group")
    gid = serializers.IntegerField(source="group.id")
    class Meta:
        model = models.User
        # fields = "__all__"
        fields = ["id","uname","uage","gid","group"]
################################      view       #################
class Stuview(APIView):
    authentication_classes = []
    permission_classes = []
    parser_classes = [FormParser,]#可以处理多种form形式
    def post(self,request):
        print(request.data)#只有使用时才会调用解析器 获取请求头 获取请求体  根据请求头和支持的请求头现比较
        result = models.User.objects.all()
        print(result[0].group)
        print(result[0].role.all()[0].name)
        return_result = Serializer_Foreinke(instance=result,many=True)
        ret = json.dumps(return_result.data)
        return  HttpResponse(ret)

#####################生成链接
"""
 {
        "id": 4,
        "uname": "wang",
        "uage": 20,
        "gid": 3,
        "group": "computer1",
        "link": "http://127.0.0.1:8000/rest/firstrest/group/3"
    },
{
        "id": 5,
        "uname": "li",
        "uage": 16,
        "gid": 4,
        "group": "computer2",
        "link": "http://127.0.0.1:8000/rest/firstrest/group/4"
    }"""
#################### url   ##############
path(r'firstrest/group/<px>',Stuview.as_view(),name="sgv")
#########################3

class Serializer_model(serializers.ModelSerializer):
    group = serializers.CharField(source="group.group")
    gid = serializers.IntegerField(source="group.id")
    link = serializers.HyperlinkedIdentityField(view_name="sgv",lookup_field="group_id",lookup_url_kwarg="px")#使用该方法生成针对各个id的链接
    #
    class Meta:
        model = models.User
        # fields = "__all__"
        fields = ["id","uname","uage","gid","group","link"]

class Stuview(APIView):
    authentication_classes = []
    permission_classes = []
    parser_classes = [FormParser,]#可以处理多种form形式
    def post(self,request):
        print(request.data)#只有使用时才会调用解析器 获取请求头 获取请求体  根据请求头和支持的请求头现比较
        result = models.User.objects.all()
        print(result[0].group)
        print(result[0].role.all()[0].name)
        return_result = Serializer_model(instance=result,many=True,context={'request': request})
        #此处需要添加后边的context
        ret = json.dumps(return_result.data,ensure_ascii=False)
        return  HttpResponse(ret)

```
# 对请求数据进行校验
**对请求数据进行校验**  
```py
###############################   源码    #############################3
for field in fields:# 483行
    validate_method = getattr(self, 'validate_' + field.field_name, None)  #此处重要
    primitive_value = field.get_value(data)
    try:
        validated_value = field.run_validation(primitive_value)
        if validate_method is not None:
            validated_value = validate_method(validated_value)  #此处为验证的钩子函数
    except ValidationError as exc:
        errors[field.field_name] = exc.detail
    except DjangoValidationError as exc:
        errors[field.field_name] = get_error_detail(exc)
    except SkipField:
        pass
    else:
        set_value(ret, field.source_attrs, validated_value)
    #也就是说要在实现的的类中实现一个validate_+字段名称的函数
class Serializer_query_data(serializers.Serializer):#上传数据校验
    name = serializers.CharField(error_messages={"required":"名字不能为空"},validators=[])#可以使用validators来自定义类进行延华智能
    # 使用钩子函数自定义验证规则  如果需要钩子函数的话  name如何写钩子函数
    age = serializers.IntegerField()

    def validate_id(self,value):
        pass
```
一个类括号实例化 可以不看  这样会自动创建很多变量
        
分页 自动分页处理

分页原理  第几页  一页多少个  

mysql  limit  offset  越往后越慢 在某个位置向后查看多少条数据 偏移  


加密的分页  只能看上页与下页  但是解决了 即时查看一千万页也很快，记录上次查页的最大id  这样就可以使用索引了
url中会对id进行加密

```py 
###################################      普通分页的方式 ############
class myPager(PageNumberPagination):  #LimitOffsetPagination  limit 分页继承  CursorPagination  加密分页
    page_size = 2#默认每页个数
    page_size_query_param = "size"#传进来修改每页个数的参数名
    max_page_size = 5#最大可修改page个数

class Stuview(APIView):
    authentication_classes = []
    permission_classes = []
    # parser_classes = [FormParser,]#可以处理多种form形式
    def get(self,request,*args,**kwargs):#处理数据验证
        result = models.User.objects.all()#获取数据
        print(result[0].group)
        print(result[0].role.all()[0].name)

        pg = myPager()#实例化对象
        pager = pg.paginate_queryset(queryset=result,request=request,view=self)#分页后的结果
        return_result = Serializer_model(instance=pager, many=True, context={'request': request})#序列化
        # return Response(return_result.data)
        pg.get_paginated_response(return_result.data)
        # return pg.get_paginated_response(return_result.data)#可以返回一个response对象   并且自动添加下一页等url
        return Response(return_result.data)

#####################################   limit分页  ##################3

class myPager(CursorPagination):#加密的分页  http://127.0.0.1:8000/rest/firstrest/?cursor=cj0xJnA9NA%3D%3D  返回的url样式  下次访问直接跳到最大值处 
    ordering = 'id' #必须要写  id 正负 可以控制正反排序

class Stuview(APIView):
    authentication_classes = []
    permission_classes = []
    # parser_classes = [FormParser,]#可以处理多种form形式
    def get(self,request,*args,**kwargs):#处理数据验证
        result = models.User.objects.all()#获取数据   先查询
        print(result[0].group)
        print(result[0].role.all()[0].name)

        pg = myPager()#实例化对象
        pager = pg.paginate_queryset(queryset=result,request=request,view=self)#分页后的结果   然后分页

        return pg.get_paginated_response(return_result.data)#可以返回一个response对象   并且自动添下一页的url   然后序列化返回
 ```





路由系统 会对原生的路由 改版
视图 restframework也可以写fbv  加一个APIVIEW的装饰器  视图依旧可以向下继承 相当于功能越多  还可以继承 modelviewsite
渲染器

django-contenttype django组件








