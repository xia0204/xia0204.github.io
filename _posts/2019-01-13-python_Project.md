---
layout: post
title: For project online
---
# 项目部署文档
---
## 启动配置最简

			uwsgi安装   编写uwsgi.ini文件   使用uwsgi --ini uwsgi.ini启动服务
			[uwsgi]
			http=127.0.0.1:8888
			wsgi-file=/home/ubuntu/flask_test/app.py
			callable=app
			touch-reload=/home/ubuntu/flask_test/　touch-reload：动态监控文件变化，然后重载服务，
			1 $ uwsgi --emperor  /path/to/vassals/


			注意了，这里--emperor后面的参数是一个文件夹的路径，这个文件夹里面放置了你所有需要监控的应用的配置文件，
			一旦这些配置文件发生变更，uwsgi就会动态加载，所以虽然对上面的配置文件放置位置没什么特殊要求，但是最好将你的配置文件用软链接的方式放到这个配置文件中：

			1 $ ln -s /file/path/of/conf.ini   /path/to/vassals/
					最后，配置完成之后，要放在后台持续运行，可以使用nohup命令：

			1 $ nohup uwsgi --emperor  /path/to/vassals/  &
---
## nginx  一般部署
				server{
				　　listen  80;
				　　server_name  localhost;
				　　location /xxx/yyy/zzz{
				　　　　proxy_pass  http://127.0.0.1:8888;
				　　}
				}
---
## nginx特殊部署

				server{
					listen  80;
					server_name  localhost;
					location /xxx/yyy/zzz{
						include uwsgi_params;
						uwsgi_pass  unix://path/to/uwsgi.sock
					}
				}

				注意，如果使用uwsgi协议进行通信，则在uWSGI的配置文件中应该使用socket配置项而不是http或者http-socket，也就是类似于下面这种形式：

 				uwsgi编写
				[uwsgi]
				socket=/path/to/uwsgi.sock
				wsgi-file=/home/ubuntu/flask_test/app.py
				callable=app
				touch-reload=/home/ubuntu/flask_test/



## 将不重要的业务部署到无session状态的反向代理下
		利用负载均衡降低服务器压力
		利用celery 将耗时任务分布到其他服务器执行



# 阿里云密码  数据库密码相同
	TestDecops123.0


mysql8用户密码的加密方法发生了变化如果在连接时出现 2059的报错则需要修改加密方式，
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
wordpress 密码   "WordPress123.0"


复制代码
-- 使用mysql 数据库
USE mysql
-- 为mysql创建用户：case_dev 密码为：pass123
CREATE USER case_dev IDENTIFIED BY 'pass123';
-- 查看下用户case_dev的权限
SELECT *  FROM USER WHERE USER='case_dev' ;
SHOW GRANTS FOR case_dev;
-- 给用户case_dev在数据库名为auto_dev上赋EXECUTE(执行存储过程),INSERT,SELECT,UPDATE权限，@'%'表示从任意ip都可以访问到这个数据库
GRANT EXECUTE,INSERT,SELECT,UPDATE ON auto_dev.* TO 'case_dev'@'%';
-- 生效
FLUSH PRIVILEGES;
-- 再次查询 下权限
SELECT *  FROM USER WHERE USER='case_dev' ;
SHOW GRANTS FOR case_dev;
复制代码
