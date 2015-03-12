Date: 2013-11-07 12:34:42
title: sqlite数据库迁移的几种办法

### 需求
把数据库中除指定的表外的其它表数据清掉，但要保留表结构。

### 手工
50多个表，一个一执行`delete from table_name`，作为一个ThoughtWorker，这样做且不说会被同事鄙视，连自己那关都过不了。

### 文本处理 + shell
通过`.tables`命令得到所有表名，*手工去掉要保留的表名*，
再用vim或其它文本编辑器把表名处理成`"tbl1" "tbl2" "tbl3"`这样的形式，再写一个shell脚本，用for循环来执行。
```
#!/bin/bash
for tableName in "tbl1" "tbl2" "tbl3"
do
    sqlite3 services.db "delete from ${tableName};"
done
```
这个属于半自动，也是我一开始想到的方式。

### shell only
在查找shell资料的过程中发现，可以在shell脚本中得到一个命令的执行结果作为变量，这样就可以不用手工处理表名了，代码如下：
```
#!/bin/bash
tables=`sqlite3 services.db ".tables"`
for tableName in $tables
do
  if [[ "$tableName" != "tableWhichShouldReserve" ]]; then
    sqlite3 services.db "delete from ${tableName};"
  fi
done
```
注意，第一条命令的等号两边都不能有空格
```
tables=`sqlite3 services.db ".tables"`
```

---
如果不需要保留其它表的结构的话，还有两种方式：
### attach as
```
$> sqlite3 1.db

sqlite> attach '2.db' as db2;
```
需要注意'2.db'一定要有引号，这时你可以通过.databases命令查看到：
```
sqlite> .databases
seq  name             file
---  ---------------  ----------------------------------------------------------
0    main             /Users/twer/Desktop/1.db
2    db2              /Users/twer/Desktop/2.db
```
然后执行如下命令：
```
sqlite> create table db2.table_name as select * from main.table_name;
```
*table_name是你要迁移的表名*

### dump + read
```
sqlite3 1.db '.dump table_name' > 2.sql
touch 2.db
sqlite3 2.db '.read 2.sql'
```

### 总结
有句话说：如果你手里只有一把锤子，你会看什么都像钉子。
如果时间允许的话，找到尽量多的解决方案并记录下来，下次再处理类似问题的时候就会轻松很多。