#                   Oracle Data Pump
##     Oracle Data Pump概述

          Oracle Data Pump技术可以将数据和元数据从一个数据库高速移动到另一个数据库。
### 数据泵组件
* 命令行客户端，expdp以及impdp
* 该DBMS_DATAPUMPPL / SQL程序包（也称为数据泵API）
* 该DBMS_METADATAPL / SQL程序包（也称为元数据API）

在expdp和impdp客户机使用规定的程序DBMS_DATAPUMPPL / SQL包来执行导出和导入命令，使用在命令行中输入的参数。这些参数支持导出和导入完整数据库或数据库子集的数据和元数据。
### 数据泵导出和导入操作所需的角色

许多数据泵导出和导入操作要求用户具有DATAPUMP_EXP_FULL_DATABASE角色和/或DATAPUMP_IMP_FULL_DATABASE角色。运行作为数据库创建一部分的标准脚本时，将自动为Oracle数据库定义这些角色。（请注意，虽然这些角色的名称包含单词FULL，但实际上所有导出和导入模式都需要这些角色，而不仅仅是完整模式。）

这个DATAPUMP_EXP_FULL_DATABASE角色仅影响导出。这个DATAPUMP_IMP_FULL_DATABASE角色影响使用Import SQLFILE参数的导入操作和操作。


## 数据泵导出
Data Pump Export（以下简称为Export）是一个实用程序，用于将数据和元数据卸载到一组称为转储文件集的操作系统文件中。转储文件集
只能由Data Pump Import实用程序导入。转储文件集可以在同一系统上导入，也可以移动到另一个系统并加载到那里。
## 数据泵导入
数据泵导入（以下简称为“导入”）是一种用于将导出转储文件集加载到目标系统的实用程序。转储文件集由一个或多个包含表数据，数据库对象元数据和控制信息的磁盘文件组成。文件以专有的二进制格式编写。在导入操作期间，数据泵导入实用程序使用这些文件来查找转储文件
集中的每个数据库对象。
## 导入导出表
```
sqlplus system/123456 
create or replace directory dump_dir as 'D:\app';--创建directory 对象目录 转储文件只能存放在directory 对象目录下
 
create table LSH
(DEPTNO number(4) primary key,
DNAME varchar2(14),
LOC varchar2(13)
);
insert all  
     into LSH values(10,'ACCOUNTING','LONDON')
     into LSH values (20,'RESEARCH','PERSTON')
     into LSH values (30,'SALES','LIVERPOOL')
     SELECT 1 FROM DUAL;
  
grant connect,resource,create database,table  to scott;
grant read,write on directory dump_dir to scott;
grant exp_full_database to scott;
grant imp_full_database to scott;

expdp scott/123456 DIRECTORY=dump_dir dumpfile=tab.dmp tables=scott.LSH--将新建的表导出
drop table LSh;--删除新建的表
impdp scott/123456 DIRECTORY=dump_dir dumpfile=tab.dmp tables=LSH --将导出的表导入
select * from  lsh;       --查看导入的表是否导入
```

## 导入导出模式
```
expdp system/123456 DIRECTORY=dump_dir dumpfile=schema.dmp schemas=scott--将scott用户的模式导出
 drop user scott cascade;                       --级联删除scott用户
impdp system/123456 DIRECTORY=dump_dir dumpfile=schema.dmp schemas=scott--将scott用户的模式导入
conn scott/123456         --查看是否导入成功
 ```

## 导入导出表空间
```
create tablespace ts_test datafile 'D:\piccoa.dbf' size 200m;--创建表空间
create user user_test identified by 123456 default tablespace ts_test temporary tablespace temp profile default;--新建用户 使其默认表空间为ts_test
grant dba,connect,resource,imp_full_database,exp_full_database to user_test;授予权限

conn user_test/123456

create table LSH_1--创建新的表1
(DEPTNO number(4) primary key,
DNAME varchar2(14),
LOC varchar2(13)
);
create table LSH_2
(DEPTNO number(4) primary key,
DNAME varchar2(14),
LOC varchar2(13)
);
insert all  
     into LSH_1 values(10,'ACCOUNTING','LONDON')
     into LSH_1 values (20,'RESEARCH','PERSTON')
     into LSH_1 values (30,'SALES','LIVERPOOL')
     SELECT 1 FROM DUAL;


expdp system/123456 DIRECTORY=dump_dir dumpfile=tablespace_ts_test.dmp tablespaces=ts_test
 drop table LSh_1;//删除表空间中的表
 drop table Lsh_2;
impdp system/123456 DIRECTORY=dump_dir dumpfile=tablespace_ts_test.dmp tablespaces=ts_test--导入表空间
select * from ts_test.lsh_1;--查看是否导入成功
```
## 导入导出数据库
(以一个表查看数据库是否导入成功)

```
select * from lsh_1;--查看表是否存在

expdp system/123456@orcl DIRECTORY=dump_dir dumpfile=full_orcl.dmp full=y--导出数据库
drop table lsh_1;--删除数据库中的表lsh_1;
select * from lsh_1;
impdp system/123456@orcl DIRECTORY=dump_dir dumpfile=full_orcl.dmp full=y--导入数据库
select * from lsh_1;查看数据是否导入成功








-
