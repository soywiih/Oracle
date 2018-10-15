# oracle
SELECT d.department_name，count(e.job_id)as "部门总人数"，   
avg(e.salary)as "平均工资"   
from hr.departments d，hr.employees e   
where d.department_id = e.department_id   
and d.department_name in ('IT'，'Sales')   
GROUP BY department_name;    

![ABC](https://github.com/soywiih/oracle/blob/master/test1/1.png)   
![ABC](https://github.com/soywiih/oracle/blob/master/test1/2.png)   
![ABC](https://github.com/soywiih/oracle/blob/master/test1/3.png)   
SELECT d.department_name，count(e.job_id)as "部门总人数"，   
avg(e.salary)as "平均工资"   
FROM hr.departments d，hr.employees e   
WHERE d.department_id = e.department_id   
GROUP BY department_name   
HAVING d.department_name in ('IT'，'Sales');   
![ABC](https://github.com/soywiih/oracle/blob/master/test1/4.png)   
![ABC](https://github.com/soywiih/oracle/blob/master/test1/5.png)   
![ABC](https://github.com/soywiih/oracle/blob/master/test1/6.png)
