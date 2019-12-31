# FLask

## 相关
+ 模板在templates文件夹
- 静态文件在 static文件夹中
+ /static/...为静态文件访问路劲
- <img src="{{url_for{'static',filename='img/a.jpg'}}"

## 路由
## 模板

+ 继承
        父模板
        {% block name %}{% endblock %} 定义子模板汇中允许被修改的内容部分
        子模板
        {% extens blockname %} {% endblock %}  继承
        {% block name %} {% endblock %} 覆盖父模板
        使用{{super（）}}调用父模板内容
+ 修改templates static文件路径
        修改配置
        在创建falsk实例的实例是顺便指定（APP= flask。Flask（__name__,template_folder='/s/',static_url_path=''））
        self, import_name, static_path=None, static_url_path=None,访问路径
                 static_folder='static', template_folder='templates',
                 instance_path=None, instance_relative_config=False,
                 root_path=None

# 蓝图   
类似Django中的app的定义，用于团队开发，多个flask实例中可以复用相同蓝图，仅需注册即可工作，搭配分布式session可以完成分布式集群。代码需完成主要包括 
1. 蓝图编写
蓝图编写包括两个模块
+ 实际蓝图部分
```python
from flask import Blueprint,session

bluep = Blueprint('bluePrintFile',__name__,url_prefix='/bluep')

@bluep.route("/t")
def tbluep():
    session["name"] = "testqi"
    return 'test success'
@bluep.route("/ta") 
```
2. 蓝图注册
- 蓝图注册部分
```python
from flask import Flask,session
from bluePrintFile import bluep

app = Flask(__name__)
app.config["SECRET_KEY"] = "current session"
app.register_blueprint(bluep)

@app.route("/test")
def tnormal():
    if "name" in session.keys():
        return session["name"]


if __name__ == "__main__":
    app.run(debug=True, host="0.0.0.0",port=8899)
```



# 大型复杂的flask项目 
简单分为 manager view  model 三个模块 
一个包表示一个应用  单独存放管理文件

app中   

        \__init__.py  #负责对整个应用进行初始化
        models.py  

        main 中主要业务逻辑  另若干文件夹程序包  来处理不同种类业务逻辑    

                \__init__.py  
                views.py
        例：  
        users处理用户逻辑
         
**templates 中存放模板**

static  中存放今天文件  js css  img

1. 编写app/__init__.py 
        构建Flask的应用以及各种配置
        构建SQLAlchemy的应用
