# **Oracle第三次实验**    
## **使用system用户给自己的账号分配上述分区的使用权限**      
```
ALTER USER new_user_soy QUOTA 50M ON USERS;       
ALTER USER new_user_soy QUOTA 50M ON USERS02;       
ALTER USER new_user_soy QUOTA 50M ON USERS03;      
```
![ABC](https://github.com/soywiih/oracle/blob/master/test3/1.png)          
## **使用new_user_soy创建订单表(orders)与订单详表(order_details)**     
### 创建订单表（orders）    
```
 CREATE TABLE "ORDERS"       
   (	"ORDER_ID" NUMBER(10,0) NOT NULL ENABLE,        
	"CUSTOMER_NAME" VARCHAR2(40 BYTE) NOT NULL ENABLE,     
	"CUSTOMER_TEL" VARCHAR2(40 BYTE) NOT NULL ENABLE,  
	"ORDER_DATE" DATE NOT NULL ENABLE,     
	"EMPLOYEE_ID" NUMBER(6,0) NOT NULL ENABLE,  
	"DISCOUNT" NUMBER(8,2) DEFAULT 0,     
	"TRADE_RECEIVABLE" NUMBER(8,2) DEFAULT 0,     
	 CONSTRAINT "ORDERS_PK" PRIMARY KEY ("ORDER_ID")     
  USING INDEX PCTFREE 10 INITRANS 2 MAXTRANS 255 COMPUTE STATISTICS     
  TABLESPACE "USERS"  ENABLE    
   ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255     
 NOCOMPRESS    
  STORAGE(    
  BUFFER_POOL DEFAULT FLASH_CACHE DEFAULT CELL_FLASH_CACHE DEFAULT)   
  TABLESPACE "USERS"     
  PARTITION BY RANGE ("ORDER_DATE")    
 (PARTITION "PARTITION_BEFORE_2016"  VALUES LESS THAN (TO_DATE(' 2016-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS',        'NLS_CALENDAR=GREGORIAN'))   NO INMEMORY SEGMENT CREATION DEFERRED     
  PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255     
 NOCOMPRESS NOLOGGING     
  STORAGE( INITIAL 8388608 NEXT 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645    
  BUFFER_POOL DEFAULT FLASH_CACHE DEFAULT CELL_FLASH_CACHE DEFAULT)    
  TABLESPACE "USERS" ,     
 PARTITION "PARTITION_BEFORE_2017"  VALUES LESS THAN (TO_DATE(' 2017-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS',         'NLS_CALENDAR=GREGORIAN')) SEGMENT CREATION DEFERRED       
  PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255    
 NOCOMPRESS NOLOGGING       
  STORAGE(    
  BUFFER_POOL DEFAULT FLASH_CACHE DEFAULT CELL_FLASH_CACHE DEFAULT)      
  TABLESPACE "USERS02"，    
     
 PARTITION "PARTITION_BEFORE_2018"  VALUES LESS THAN (TO_DATE(' 2018-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS',     'NLS_CALENDAR=GREGORIAN')) SEGMENT CREATION DEFERRED    
  PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255    
 NOCOMPRESS NOLOGGING    
  STORAGE(   
  BUFFER_POOL DEFAULT FLASH_CACHE DEFAULT CELL_FLASH_CACHE DEFAULT)   
  TABLESPACE "USERS03"  ) ;    
  ```
  ![ABC](https://github.com/soywiih/oracle/blob/master/test3/2.png)      
  ### 创建订单详表(order_details)    
  ```
  CREATE TABLE order_details 
(
id NUMBER(10, 0) NOT NULL 
, order_id NUMBER(10, 0) NOT NULL
, product_id VARCHAR2(40 BYTE) NOT NULL 
, product_num NUMBER(8, 2) NOT NULL 
, product_price NUMBER(8, 2) NOT NULL 
, CONSTRAINT order_details_fk1 FOREIGN KEY  (order_id)
REFERENCES orders  (  order_id   )
ENABLE 
) 
TABLESPACE USERS 
PCTFREE 10 INITRANS 1 
STORAGE (   BUFFER_POOL DEFAULT ) 
NOCOMPRESS NOPARALLEL
PARTITION BY REFERENCE (order_details_fk1)
(
PARTITION PARTITION_BEFORE_2016 
NOLOGGING 
TABLESPACE USERS
PCTFREE 10 INITRANS 1 
STORAGE 
( 
 INITIAL 8388608 
 NEXT 1048576 
 MINEXTENTS 1 
 MAXEXTENTS UNLIMITED 
 BUFFER_POOL DEFAULT 
) 
NOCOMPRESS NO INMEMORY, 
PARTITION PARTITION_BEFORE_2017 
NOLOGGING 
TABLESPACE USERS02
PCTFREE 10 INITRANS 1 
STORAGE 
( 
 INITIAL 8388608 
 NEXT 1048576 
 MINEXTENTS 1 
 MAXEXTENTS UNLIMITED 
 BUFFER_POOL DEFAULT 
) 
NOCOMPRESS NO INMEMORY, 
PARTITION PARTITION_BEFORE_2018 
NOLOGGING 
TABLESPACE USERS03
);
```
![ABC](https://github.com/soywiih/oracle/blob/master/test3/3.png)    
## **插入数据**   
```
declare
  maxnumber constant int:=10000;
  i int :=1;
  
begin

  for i in 1..maxnumber loop
  
    insert into orders(order_id, customer_name, customer_tel, order_date, employee_id, discount, trade_receivable)
    values(i, '1', '1', '1', '1', '1', '1');
    
    insert into order_details (id, order_id, product_id, product_num, product_price)
    values(i, '1', '1', '1', '1');
    
  end loop;
  commit;
  
end; 
```   
## **用system查看数据库的使用情况**
```
SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';
```
![ABC](https://github.com/soywiih/oracle/blob/master/test3/4.png)    
```
SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
 Round(( total - free )/ total,4)* 100 "使用率%"
 from (SELECT tablespace_name,Sum(bytes)free
        FROM   dba_free_space group  BY tablespace_name)a,
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
        group  BY tablespace_name)b
 where  a.tablespace_name = b.tablespace_name;
```
![ABC](https://github.com/soywiih/oracle/blob/master/test3/5.png)     
