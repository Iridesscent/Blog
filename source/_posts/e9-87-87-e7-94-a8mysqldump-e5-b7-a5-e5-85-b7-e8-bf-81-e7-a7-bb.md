---
title: 采用MySQLdump工具迁移
id: 233
categories:
  - Linux
date: 2016-05-17 15:52:55
tags:
  - Database
---

转自[采用MySQLdump工具迁移](https://help.aliyun.com/knowledge_detail/5974579.html?spm=5176.788314899.2.1.RZdalS#采用MySQLdump工具迁移)

该方案优点是简单，容易上手；缺点是停机时间较长。 因此它适用于数据量不大，或者允许停机的时间较长，并且在这个时间范围内能够完成。

由于RDS提供的关系数据库服务与原生的数据库服务是完全兼容的，所以对于用户来说，从原有数据库迁移到RDS数据的过程，与从一个mysql服务器迁移到另外一台mysql服务器的过程基本类似。具体的迁移流程如下：

![](https://bbs.aliyun.com/attachment/thumb/Mon_1206/27_124170_8ce2a3fdf17e24e.jpg?20)

1、 开通云服务器服务。

2、 开通RDS服务，如果开通成功，会返回给用户连接地址和端口，比如cloudcc.mysql.rds.aliyuncs.com：3306。

3、 先停止业务，使用MySQLdump的数据导出工具，将您线下原有数据库数据导出为数据文件（(本步骤仅仅导出数据，不包括存储过程，函数，触发器)。具体命令如下：

mysqldump -h local_ip -u user_name -p --opt --default-character-set=utf8   --hex-blob db_name --skip-triggers  &gt; /tmp/db_name.sql

&nbsp;

其中

local_ip 以您真实的线下MySQL服务器IP地址代替

user_name 以您真实的数据库用户代替

db_name   以您真实的数据库名代替

/tmp/db_name.sql是文件名，由您自己随意填写。

&nbsp;

4、备份您线下原有数据库的存储过程、触发器和函数(若您数据库中没有使用存储过程、触发器和函数，则跳过这步)，在导出存储过程、触发器和函数时，需要将definer去掉，以兼容RDS。具体命令如下：

mysqldump -h local_ip -u user_name -p --opt --default-character-set=utf8   --hex-blob db_name<span style="font-size: 10pt; font-family: 微软雅黑, sans-serif; color: #222222; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"> –triggers -d -t</span>  -R | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/'  &gt; /tmp/trigger_procedure.sql

其中

local_ip 以您真实的线下MySQL服务器IP地址代替

user_name 以您真实的数据库用户代替

db_name   以您真实的数据库名代替

/tmp/trigger_procedure.sql是文件名，由您自己随意填写。

&nbsp;

5、将数据文件和存储过程文件上传到已经购买的云服务器中。

6、远程登录到云服务器，将刚才上传的数据文件导入到已购买的RDS实例中。具体命令如下：

mysql -hexample.mysql.rds.aliyuncs.com –uuserName -p dbName &lt; /tmp/dbName.sql

其中

example.mysql.rds.aliyuncs.com：RDS实例连接地址；

user_name 以您真实的RDS数据库用户代替

db_name   以您真实的RDS数据库名代替

/tmp/db_name.sql是刚才前面您导出的数据文件名称

&nbsp;

7、远程登录到云服务器，将刚才上传的存储过程文件（包含存储过程、触发器和函数，若没有则请跳过此步）导入到已购买的RDS实例中。具体命令如下：

mysql -h example.mysql.rds.aliyuncs.com -u userName -p dbName &lt; /tmp/triggerProcedure.sql

其中

example.mysql.rds.aliyuncs.com：RDS实例连接地址；

user_name 以您真实的RDS数据库用户代替

db_name   以您真实的RDS数据库名代替

/tmp/trigger_procedure.sql是刚才前面您导出的存储过程文件名称

至此数据迁移完毕，可以对RDS正常使用。用户可使用数据库客户端工具（如MySQL-Front）或第三方数据库管理工具(如phpmyadmin)以及程序的方式，通过域名和端口号连接数据库，在输入正确的用户名和密码后即可登陆DB进行操作和开发。