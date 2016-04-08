---
layout: post
section-type: post
title: Django中对MySQL数据库建表改表操作及注意事项
category: tech
tags: [ 'django','mysql' ]
toc: show
---

_environment:Django 1.8, Ubuntu 14.04LTS, Mysql 5.6_

### Upgrading-from-South

Django版本升级之后不再使用south，迁移已经存在的表的方法如下:

[In official document ...](https://docs.djangoproject.com/en/1.9/topics/migrations/#upgrading-from-south)

> If you already have pre-existing migrations created with South, then the upgrade process to use django.db.migrations is quite simple:

> + Ensure all installs are fully up-to-date with their migrations.

> + Remove `'south'` from `INSTALLED_APPS`.

> + Delete all your (numbered) migration files, but not the directory or `__init__.py` - make sure you remove the .pyc files too.

> + Run `python manage.py makemigrations`. Django should see the empty migration directories and make new initial migrations in the new format.

> + Run `python manage.py migrate --fake-initial`. Django will see that the tables for the initial migrations already exist and mark them as applied without running them. (Django won’t check that the table schema match your models, just that the right table names exist).

其实主要就是删除工程中migrations文件夹里面除了`__init__.py`之外的文件，linux终端删除时可以参考以下两行代码删除之前使用`south`遗留的文件

```
find .env34 -type d -name "__pycache__" -exec rm -rfv {} \;
find .env34 -type f -name "000*" -delete
```

`python manage.py makemigrations`主要是为了生成一遍包含所有建表信息的`0001_initial.py`文件

`python manage.py migrate --fake-initial`则是为了伪执行一遍`0001_initial.py`文件中的建表信息，这样就会在表格`django_migrations`里面插入不用再执行的`migrations`文件记录

同理以上操作可以适用于重新整理`migrations`文件夹中内容并生成`0001_initial.py`

### Make-Migrations

目前建表改表流程如下：

1.develop分支拉取最新版本代码，切换到固定改表分支去merge一下develop的内容，确保改表分支的内容为最新

2.本地修改model内容，保存，终端运行`./manage.py makemigrations`生成`migrations`内新文件，新文件记得添加到git

3.终端运行`./manage.py migrate [app_label]`对数据表进行真正修改，并且会同时在表中写入记录，注意：建议加上app_label指名运行model有改动的app（尤其是工程中出现了使用多个数据库时，这里就要谨慎使用，特别是在线上改表的时候），不过这个字段括起来表示可选

4.本地改表无误，代码部署到测试机。此时测试机只运行`./manage.py migrate [app_label]`即可，一定**不要**运行`./manage.py makemigrations`

5.如果一切无误则可以合并代码。可以查看表结构是否无误，如在数据库中执行：

    ```show create table table_name```

### Tips-for-Migrations

+ 如果是删除某个表格的多余列并且删除和这些列相关的索引时务必分两部操作

  + 改model表时先删掉索引，运行一下`./manage.py makemigrations`

  + 之后再删列，运行一下`./manage.py makemigrations`

  + 最后执行`./manage.py migrate [app_label]`可以保证无误

### Migrate-Table-Manually

手动改表有两种情况，其一是某些app的表不在主数据库而在其他数据库里，需要手动建表等操作，其二是有时候会出现不必要的冲突或者改错表的情况，单纯借助Django自带的改表操作不太合适。

第一种情况一般操作定式，第二种情况需要极为慎重，都注意不要触动已有表格数据。

情况一（例如统计日志app和其他业务性表不在一个数据库中）：

+ log表结构提前备份`mysqldump -uroot -p[password] -d bugua > ~/create_table.sql`

+ 一定不要在线上使用`./manage.py migrate`，注意按照后面的提示操作

+ 针对log表的改动

  + 很可能本地不用分离数据库建表，那么本地直接照常运行建表即可

  + 用`select * from django_migrations order by id desc limit 3;`查看本地记录

  + 将新一条记录信息原样插入线上数据库，语句如```insert into django_migrations values(23,'log','0004_auto_20160331_1409','2016-03-31 06:09:54.000000');```

  + 用`show create table log_table_name`查看建表信息并且在相应线上log数据库里面手动建表

  + 线上主数据库完全不做其他变更

  + 记得添加新的建表语句到`create_table.sql`备份

+ 针对其他表的改动

  + 不要和log表一起操作

  + 按照正常建表改表流程进行，`./manage.py migrate app_label`务必记得加`app_label`

情况二（误操作需要手动回退等问题）：

+ 手动改表原则就是为了恢复现有`migrations`文件夹中已有条目的表结构状况，总之要操作对应表格，`migrations`文件夹中的文件需要删除的也可以删除（此时也要看看表`django_migrations`中如果也有记录需要一并还原），最终要保持两者一致

+ 总之一般就是列和索引的改动稍微麻烦点，要是整表改动的直接drop table然后删记录(`delete from django_migrations where id=X`)就行了

+ 直接用sql语句更改，常用MySQL语句如下（注意有些列名或者表名需要加反引号）：

  + 增加列：`ALTER TABLE TestTable ADD iValues int(11) NOT NULL;` (TestTable表名，iValues列名）

  + 删除列：`ALTER TABLE TestTable DROP COLUMN iValues1;`

  + 增加普通索引：`ALTER TABLE table_name ADD INDEX index_name ( column )

  + 增加多列索引：`ALTER TABLE table_name ADD INDEX index_name ( column1, column2, column3 )`

  + 删除普通索引：`ALTER TABLE table_name DROP INDEX index_name` （提前查index_name）

+ 注意手动改表有可能会导致其他程序员需要重新导入一下线上表结构

---







