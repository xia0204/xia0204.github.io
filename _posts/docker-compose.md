Docker Compose项目简介

Compose 定位是“defining and running complex applications with Docker”，前身是 Fig，兼容 Fig 的模板文件。
Dockerfile 可以让用户管理一个单独的应用容器；而 Compose 则允许用户在一个模板（YAML 格式）中定义一组相关联的应用容器（被称为一个  project  ，即项目）

该项目由 Python 编写，实际上调用了 Docker 提供的 API 来实现。

安装 （环境 Centos7）


yum install docker-compose -y
术语

服务（service）：一个应用容器，实际上可以运行多个相同镜像的实例。

项目(project)：由一组关联的应用容器组成的一个完整业务单元。

一个项目可以由多个服务（容器）关联而成，Compose 面向项目进行管理。

YAML 模板文件详解

默认的模板文件是  docker-compose.yml  ，其中定义的每个服务都必须通过  image  指令指定镜像或build  指令（需要 Dockerfile）来自动构建。

如果使用  build  指令，在  Dockerfile  中设置的选项(例如： CMD  ,  EXPOSE  ,  VOLUME  ,  ENV  等) 将会自动被获取，无需在  docker-compose.yml  中再次设置。

YAML 语法格式，猛击这里
```yaml
image

指定为镜像名称或镜像 ID。如果镜像在本地不存在， Compose  将会尝试拉去这个镜像。


#实用的例子
image: ubuntu
image: bushaoxun/centos
```
```yaml
build

指定  Dockerfile  所在文件夹的路径。  Compose  将会利用它自动构建这个镜像，然后使用这个镜像。


#实用例子
build: .
build: /path/to/build/dir
```
```yaml
links

链接到其它服务中的容器。使用服务名称（同时作为别名）或服务名称：服务别名  （SERVICE:ALIAS）  格式都可以。

链接容器后，将会在防火墙中创建对应的路由规则，同时会自动在容器中 /etc/hosts 文件中创建对应的主机名和 IP 地址记录。


#语法举例
links:
    - db
    - db:database
    - redis
```
```yaml  
external_links

链接到 docker-compose.yml 外部的容器，甚至 并非  Compose  管理的容器。参数格式跟  links  类似。


#语法举例
external_links:
    - redis_1
    - project_db_1:mysql
    - project_db_1:postgresql
```
```yaml
ports

暴露端口信息。

使用宿主：容器  （HOST:CONTAINER）  格式或者仅仅指定容器的端口（宿主将会随机选择端口）都可以。


ports:
    - "3000"
    - "8000:8000"
    - "49100:22"
    - "127.0.0.1:8001:8001"
 注意：当使用  HOST:CONTAINER  格式来映射端口时，如果你使用的容器端口小于 60 你可能会得到错误得结果，因为  YAML  将会解析  xx:yy  这种数字格式为 60 进制。所以建议采用字符串格式。
```
```yaml
expose

暴露端口，但不映射到宿主机，只被连接的服务访问。

expose:
    - "3000"
    - "8000"
```
```yaml
volumes

卷挂载路径设置。可以设置宿主机路径 （ HOST:CONTAINER  ） 或加上访问模式（ HOST:CONTAINER:ro  ）


volumes:
    - /var/lib/mysql
    - cache/:/tmp/cache
    - ~/configs:/etc/configs/:ro
```
```yaml
volumes_from

从另一个服务或容器挂载它的所有卷。

volumes_from:
     - service_name
     - container_name
```
```yaml
environment

设置环境变量。你可以使用数组或字典两种格式。


environment:
    RACK_ENV: development
    SESSION_SECRET:
environment:
    - RACK_ENV=development
    - SESSION_SECRET
```
```yaml
env_file

从文件中获取环境变量，可以为单独的文件路径或列表。

如果通过  docker-compose -f FILE  指定了模板文件，则  env_file  中路径会基于模板文件路径。

如果有变量名称与  environment  指令冲突，则以后者为准。


env_file: .env
env_file:
    - ./common.env
    - ./apps/web.env
    - /opt/secrets.env
环境变量文件中每一行必须符合格式，支持  #  开头的注释行。


RACK_ENV=development
```
```yaml
extends

基于已有的服务进行扩展。例如我们已经有了一个 webapp 服务，模板文件为  common.yml  。
```
```yaml

# common.yml
webapp:
    build: ./webapp
    environment:
        - DEBUG=false
        - SEND_EMAILS=false
编写一个新的  development.yml  文件，使用  common.yml  中的 webapp 服务进行扩展。
```
```yaml

# development.yml
web:
    extends:
        file: common.yml
    service: webapp
    ports:
        - "8000:8000"
    links:
        - db
    environment:
        - DEBUG=true
db:
    image: postgres
后者会自动继承 common.yml 中的 webapp 服务及相关环节变量。
```
```yaml
net

设置网络模式。使用和  docker client  的  --net  参数一样的值。


net: "bridge"
net: "none"
net: "container:[name or id]"
net: "host"
```
```yaml
pid

跟主机系统共享进程命名空间。打开该选项的容器可以相互通过进程 ID 来访问和操作。


pid: "host"
```
```yaml
dns

配置 DNS 服务器。可以是一个值，也可以是一个列表。

dns: 8.8.8.8
dns:
    - 8.8.8.8
    - 114.114.114.114
```
```yaml
dns_search

配置 DNS 搜索域。可以是一个值，也可以是一个列表。


dns_search: example.com
dns_search:
    - domain1.example.com
    - domain2.example.com
```
# Docker-compose 命令详解

## build

命令用来创建或重新创建服务使用的镜像，后面指定的是服务的名称，创建之后的镜像名为project_service,

即项目名后跟服务名。比如项目名称为composeset，其中的一个服务名称为web，则docker-compose build web创建的镜像的名称为composeset_web。

## help

获得一个命令的帮助。

## kill

通过发送  SIGKILL  信号来强制停止服务容器。支持通过参数来指定发送的信号

docker-compose kill -s SIGINT
## logs

查看服务的输出。

## port

打印绑定的公共端口

## pull

拉取服务镜像

## rm

删除停止的服务容器

## run

在一个服务上执行一个命令


#命令实例，将会启动一个 ubuntu 服务，执行  ping docker.com  命令。
#默认情况下，所有关联的服务将会自动被启动，除非这些服务已经在运行中。
docker-compose run ubuntu ping docker.com
## scale

设置同一个服务运行的容器个数，通过  service=num  的参数来设置数量。


docker-compose scale web=2 worker=3
## start

启动一个已经存在的服务容器。

## stop

停止一个已经运行的容器，但不删除它。通过  docker-compose start  可以再次启动这些容器。

## up

构建，（重新）创建，启动，链接一个服务相关的容器。链接的服务都将会启动，除非他们已经运行。

# 环境变量

## COMPOSE_PROJECT_NAME

设置通过 Compose 启动的每一个容器前添加的项目名称，默认是当前工作目录的名字。

## COMPOSE_FILE

设置要使用的  docker-compose.yml  的路径。默认路径是当前工作目录。

## DOCKER_HOST

设置 Docker daemon 的地址。默认使用  unix:///var/run/docker.sock  ，与 Docker 客户端采用的默认值一致。

## DOCKER_TLS_VERIFY

如果设置不为空，则与 Docker daemon 交互通过 TLS 进行。

## DOCKER_CERT_PATH

配置 TLS 通信所需要的验证（ ca.pem  、 cert.pem  和  key.pem  ）文件的路径，默认是  ~/.docker

案例

简单的 wordpress 运行案例


# 创建目录
mkdir /wordpress
#编写 docker-compose.yaml 文件
vim docker-compose.yaml
```yaml
wordpress:
    image: wordpress
    links:
        - db:mariadb
    ports:
        - 90:80
db:
    image: mariadb
    environment:
        MYSQL_ROOT_PASSWORD: test
```
# 生成应用
docker-compose up -d  
**测试：(perfect)**