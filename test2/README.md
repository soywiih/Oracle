# oracle
## 第1步：以system登录到pdborcl，创建角色new_user_shy和用户new_user_soy，并授权和分配空间：
CREATE ROLE new_user_shy;   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/1.png)    
GRANT connect,resource,CREATE VIEW TO new_user_shy;   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/2.png)    
CREATE USER new_user_soy IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/3.png)    
ALTER USER new_user_soy QUOTA 50M ON users;   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/4.png)    
GRANT new_user_shy TO new_user_soy;   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/5.png)    
exit   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/6.png)    
## 第2步：新用户new_user_soy连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户
CREATE TABLE mytable (id number,name varchar(50));   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/2-1.png)    
INSERT INTO mytable(id,name)VALUES(1,'zhang');   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/2-2.png)    
INSERT INTO mytable(id,name)VALUES (2,'wang');   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/2-3.png)    
CREATE VIEW myview AS SELECT name FROM mytable;   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/2-4.png)    
SELECT * FROM myview;   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/2-5.png)    
GRANT SELECT ON myview TO hr;   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/2-6.png)    
## 第3步：用户hr连接到pdborcl，查询new_user_soy授予它的视图myview
SELECT * FROM new_user_soy.myview;   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/7.png)     
exit   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/8.png)    
## 查看数据库使用情况
SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/9.png)    
SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",   
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",   
 Round(( total - free )/ total,4)* 100 "使用率%"   
 from (SELECT tablespace_name,Sum(bytes)free   
        FROM   dba_free_space group  BY tablespace_name)a,   
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files   
        group  BY tablespace_name)b   
 where  a.tablespace_name = b.tablespace_name;   
![ABC](https://github.com/soywiih/oracle/blob/master/test2/10.png)    
