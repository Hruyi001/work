### SQL201 查找入职员工时间升序排名的情况下的倒数第三的员工所有信息
```sql
select * from employees 
    where hire_date = (select distinct hire_date 
    from employees order 
    by hire_date desc 
    limit 1 offset 2)
```
### SQL204 查找所有员工的last_name和first_name以及对应部门编号dept_no
```sql
select last_name, first_name, dept_no
    from employees e left join dept_emp d
    on e.emp_no = d.emp_no
```
### SQL206 查找薪水记录超过15条的员工号emp_no以及其对应的记录次数t
```sql
select emp_no, count(emp_no) as t
        from salaries 
        group by emp_no
        having t > 15
```

### SQL210 获取所有员工当前的manager
```sql
select de.emp_no, dm.emp_no
    from dept_emp de, dept_manager dm
    where de.dept_no = dm.dept_no and de.emp_no != dm.emp_no
```

### SQL214 查找employees表emp_no与last_name的员工信息
```sql
select * 
    from employees
    where emp_no % 2 = 1 and last_name != 'Mary'
    order by hire_date desc
```
### SQL216 获取当前薪水第二多的员工的emp_no以及其对应的薪水salary
```sql
select emp_no, salary
    from salaries
    where salary =
    (select salary
    from salaries
    order by salary desc
    limit 1 offset 1)
```
### SQL218 查找所有员工的last_name和first_name以及对应的dept_name
```sql
select last_name, first_name, dept_name
from employees e left join
(select emp_no, d.dept_no, dept_name
    from departments d left join dept_emp e
    on d.dept_no = e.dept_no) as t
on e.emp_no = t.emp_no
```

### SQL221 统计各个部门的工资记录数
```sql
select dept_no, dept_name, count(*)
    from salaries s left join
(select emp_no, d.dept_no, dept_name
    from departments d left join dept_emp e
    on d.dept_no = e.dept_no) as t
    on s.emp_no = t.emp_no
    group by dept_no
    order by dept_no
```
### SQL222 对所有员工的薪水按照salary降序进行1-N的排名
```sql
select s1.emp_no, s1.salary, 
    (select count(distinct s2.salary) + 1
    from salaries s2
    where s2.salary > s1.salary) as t_rank
    from salaries s1
    order by s1.salary desc,s1.emp_no
```
### SQL223 获取所有非manager员工当前的薪水情况
```sql
select b.dept_no, a.emp_no, salary
    from employees a, dept_emp b, dept_manager c, salaries d
    where a.emp_no = d.emp_no and b.dept_no = c.dept_no
    and a.emp_no = b.emp_no
    and b.emp_no != c.emp_no
```

### LeetCode 1045. 买下所有产品的客户 - 力扣（LeetCode）
```sql
 
select customer_id
    from Customer
    group by customer_id
    having count(distinct product_key)
    = (select count(*) from Product)
```