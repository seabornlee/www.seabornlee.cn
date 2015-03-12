Title: sqlldr用法
Url: /2009/02/11/sqlldr-usage
Category: 技术

`sqlldr user/pwd@serviceName control=load.ctl`

load.ctl内容如下：
```sql
load data
infile 'C:\Documents and Settings\Administrator\桌面\数据文件.txt'
append into table tableName //append 或者 replace
fields terminated by ';' //分隔符
(
	EMAIL CHAR,
	GENDER CHAR,
	AGE CHAR
)
```