### grafana和influxdb管理方法

**服务部署在10.134.101.126（GIT服务器）上**

1. influxdb

    - 使用rpm文件直接安装，安装文件在/usr/local/influxdb目录下

    - 使用systemctl start influxdb命令启动服务、使用systemctl stop influxdb停止服务

    - 配置文件为/etc/influxdb/influxdb.conf，目前基本都是使用的默认配置

    - 使用influx命令进入influxdb的命令行状态，常用命令有

         show databases（显示所有数据库）

         use XXX（XXX为数据库名称，使用指定数据库）

         show measurements（显示当前指定数据库下所有的表）

         select * from XXX LIMIT 10 （XXX为表名，显示XXX表内10条数据）

         exit（退出命令行状态）

    - influxdb的基础语法基本与mysql一致，mysql中表的名词为table，influxdb中表的名词为measurement

    - 若有其它问题，可以百度查看官网或者其它博客内容

2. grafana

    - 使用rpm文件直接安装，安装文件在/usr/local/grafana目录下

    - 使用systemctl start grafana-server命令启动服务、使用systemctl stop grafana-server命令停止服务

    - 配置文件为/etc/grafana/grafana.ini，目前基本都是使用的默认配置

    - 启动服务后，默认端口为3000，通过浏览器访问即可打开grafana的页面，管理员账号密码：admin/Butel2020

    - 常用功能有

         增加data source

         增加dashboard

         编辑panel