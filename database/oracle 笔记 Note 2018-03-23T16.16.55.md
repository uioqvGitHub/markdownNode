[TOC]

oracle 笔记 Note 2018-03-23T16.16.55
========================

## dml
### 格式
	select 字段名，表达式
	from 表名/数据集
	join 表二 on(连接条件)
	where 条件
	group by 分组
	having 分级函数做条件
	order by 排序
### 执行次序
	1.from
	2.join on
	3.如果有多个join则继续join
	4.where
	5.group by
	6.having
	7.select
	8.order by

### 字段类型

1.	数值

		number

#### 字符串
	Char
char数据类型存储固定长度的字符值。一个CHAR数据类型可以包括1到2000个字符。如果对CHAR没有明确地说明长度，
它的默认长度则设置为1。 如果对某个CHAR类型变量赋值，其长度小于规定的长度，那么Oracle自动用空格填充。
注意：如果给了一个比固定长度更长的值，其尾部带有空格，则空格会被删除到固定的长度。如果值太大Oracle会返回一个错误，
	varchar2

3. 时间

   date

### 子句

#### where 
    between A and B     ----》在A和B之间
    in(A,B,C)           ----》在A,B,C之中
    like ''             ----》模糊查询
        %               ----》代表0-n个字符
        _               ----》代表1个任意字符
            escape '\'      ----》指定转义字符为'\'
        ----》查询员工名字包含ar的 lower(ename) like lower('%ar%')
    is null
    is not null
    any
    all
    and
    or
    not

#### order by
	order by 字段名 升/降序
	asc     ----》升序
	desc    ----》降序

#### group by    
分组子句 应该放在where后面order by前面

    select deptno, avg(sal) from emp group by deptno;
    ----》group by时select 中的字段必需确保唯一，即使用分组函数或者为分组的条件。
    select e.deptno, avg(e.sal), d.dname from emp e, dept d where e.deptno = d.deptno group by e.deptno, d.dname;
    select e.deptno, avg(e.sal), d.dname from emp e join dept d on(e.deptno = d.deptno) group by e.deptno, d.dname;
    select count(*), s.losal, s.hisal
        from emp e join salgrade s on(e.sal between s.losal and hisal)
        group by s.grade, s.losal, s.hisal order by s.losal;

#### having      
允许使用分组函数做条件

            ----》与where很相似，最大区别是是否分组函数做条件，分组函数做条件必需使用having
            ----》只有使用groupby时才能用
    select deptno, avg(sal) from emp having avg(sal)>2000 group by deptno;

### distinct 
过滤重复数据

### dual表 
一个虚表主要测试函数使用

### 函数


#### 单行函数

1.	字符函数

		 str upper(str)
    str lower(str)
		 str initcap(str)
		 number length(str)
		 str substr(str,number1,number2)     ----》参数为原始字符串，起始位置（从1开始），长度
2. 数值函数

    number round(number1, number2)    ----》四舍五入，参数一为源，参数二为舍入后的位数
    number trunc(number1, number2)    ----》截取
3. 日期函数

    sysdate     ----》系统当前时间
    减法操作，以天数为基准
4. 转换函数

    number to_number(str1, str2)    ----》原始字符串，转换格式，0则前导强制为0,9则不影响
    str to_char(number, str2)       ----》$代表美元符号 ,代表千位符
    date to_date(str1, str2)        ----》原始字符串，转换格式，yyyy mm dd hh24/hh mi ss
    str to_char(date, str2)
        ----》查询当前入职时间条件to_char(sysdate,'mm-dd')=to_char(hiredate,'mm-dd')
5. 其他函数

    obj nvl(obj, default)           ----》设置字段为空时的默认值
#### 多行函数 
分组函数会过滤空值，若希望空值参与进来应用nvl函数进行处理，不能用不在where子句中
    
		 count()     ----》统计总记录数
	sum()       ----》求和，只能用于数值类型
	avg()       ----》求平均值，只能用于数值类型
	max()       ----》最大值
	min()       ----》最小值发

### 多表连接 
### 多表连接 
等值连接   ----》非等值连接  ----》外连接    ----》内连接

#### 关键字
1. join on     ----》多表连接
2. cross join  ----》迪卡尔积
3. (+)         ----》外连接放在where 后面条件的后面
4. ----》这是老写法，不支持全外连接即 ----》只能加在一个条件的一端
5. left outer join
6. right outer join
7. full outer join     ----》左右全，外连接    ----》outer可以省略

#### 格式
        select 字段名 from 表名1, 表名2 where 连接条件
        select 字段名 from 表名1 join 表名2  on 连接条件

#### 示例
    ----》等值连接
        ----》where写法
    select e.empno, e.ename, d.dname, e.sal, e.deptno from emp e, dept d where e.deptno = d.deptno;
        ----》join on写法  ----》on(条件) 不加括号也可以，但加上格式清晰减少错误
    select e.empno, e.ename, d.dname, e.sal, d.dname from emp e join dept d on(d.deptno=e.deptno);
        ----》迪卡尔积需要用cross join
    select e.empno, e.ename, d.dname, e.sal, d.dname from emp e cross join dept d
    ----》非等值连接
    select e.empno, e.ename, e.sal, s.grade
        from emp e join salgrade s on(e.sal between s.losal and s.hisal);
    ----》外连接    ----》显示出没有匹配到的空部门
        ----》老写法
    select e.empno, e.ename, e.sal, d.deptno, d.dname from emp e, dept d where e.deptno(+) = d.deptno;
        ----》新写法
    select e.empno, e.ename, e.sal, d.deptno, d.dname
        from emp e left outer join dept d on (e.deptno = d.deptno);
    select e.empno, e.ename, e.sal, d.deptno, d.dname
        from emp e right outer join dept d on (e.deptno = d.deptno);
    select e.empno, e.ename, e.sal, d.deptno, d.dname
        from emp e full outer join dept d on (e.deptno = d.deptno);
    ----》内连接
    ----》自连接
    --select e.empno, e.ename, e.sal, m.ename manager
      --  from emp e, emp m where e.mgr(+) = m.empno;
      ----》错误写法，会查询出不是管理者的员工
    select e.empno, e.ename, e.sal, m.ename manager
        from emp e, emp m where e.mgr = m.empno(+);
    select e.empno, e.ename, e.sal, m.ename manager
        from emp e left outer join emp m on(e.mgr = m.empno);
    ----》三表
    select e.ename, d.dname, e.sal, s.grade from emp e
        join dept d on(e.deptno = d.deptno)
        join salgrade s on(e.sal between s.losal and s.hisal);

### union
合并结果集，   要求字段一样，  会把重复的过滤掉, union all会显示重复

    select empno, deptno from emp where deptno = 20
        union select empno, deptno from emp where deptno = 30;
### 子查询
()中的查询叫做子查询，外面的叫做主查询，子查询先于主查询运行。子查询也可以用于建表

    select empno, ename, sal from emp where sal=(select max(sal) from emp);
    select empno, ename, sal from emp where sal = any (select max(sal) from emp group by deptno);
        =any    ----》等于任意一个，相当于in
        >any    ----》大于任意一个，相当于大于最小的即可
        >all    ----》大于所有，相当于大于最大的
    select empno, ename, sal, deptno from emp where deptno=(select deptno from emp where ename='WARD');
    ----》查询每个组中最高工资的人的员工信息
    select e.empno, e.ename, e.sal, e.deptno from emp e
        right outer join (select max(sal) msal,deptno from emp group by deptno) t
        on(e.deptno=t.deptno and e.sal=t.msal);

### top n 与 分页
#### top n
	select * from (select empno, ename, sal from emp  e order by e.sal desc) where rownum<5;

#### 分页
#### 分页
   		-----》3.选择查询第几条记录根据rownum别名
		select * from
		(   -----》2.将rownum别名加入查询字段
		select rownum row_num, empno, ename, sal from
		(    -----》1.排序
		select empno, ename, sal from emp  e order by e.sal desc
		)
		)
		where row_num between 3 and 7;



### 新增数据
    insert into
    ------格式
    insert into 表名(字段名1, ..., 字段名n) values(字段值1, ..., 字段值n);
    ------示例
    insert into student(id, name) values (1, 'zhangfei');
    insert into student values(2, 'guanyu', null);
    commit;
### 删除数据
    delete from
    ------格式
    delete from 表名 where 条件;
        ----》where条件可以不出现，后果就是全部删除
    ------示例
    delete from student where id = 1;
    ------截断表
    truncate table 表名;
### 修改数据
    update set
    update 表名 set 字段1 = 值1, ..., 字段n=值n where 条件;
        ----》where不出现，就是全表修改
    update student set name = 'zhugeliang' where id = 3;

### 事务处理
DML执行完毕后，并不能立即生效，需要使用事务处理语句生效。

    commit      ----》提交，就是让DML的结果写入数据库
    rollback    ----》回滚，就是撤销刚才的DML操作。
## ddl
### 建表
    create table 表名 (
        字段名1 字段类型 default 值 字段级约束,
        ...
        表级约束1,
        ...
    );
    defalut可以省略,写了的效果默认值不在是null而是指定值。
    约束可以不写，但正规的建表者是带约束的。
### 约束
    1 非空约束 not null         ---》字段不能为null
    2 唯一约束 unique           ---》字段不能重复，但可以为null
    3 主键约束 primary key      ---》是数据的唯一标识
    4 外键约束 foreign key()    references  ---》表间的关系，表的外键 指定来自哪个表
    5 检查约束 check            ----》可以检查任意的条件，可以跟条件表达式
    6 constraint               ----》为约束起一个自定义名字
    主键约束最重要，绝大多数的表者有主键约束。其次是 非空约束和外键约束， 唯一约束也常用，check约束很少使用
    约束可以保证数据的有效性，但也会影响部分性能。因此约束要适度使用。

#### 外键约束
    drop table class;
    create table class(
        id number(7) constraint cla_pk primary key,
        name varchar2(20) not null
    );
    drop table student;
    create table student (
        id number(7) constraint stu_pk primary key,
        name varchar2(20) not null,
        mobile varchar2(20) unique,
        deptno number(7),
        constraint stu_cla_fk foreign key (deptno) references class(id)
        --on delete cascade
        on delete set null
    );
    
    insert into class values (1, 'java16');
    insert into class values (2, 'java17');
    insert into class values (3, 'php199');
    insert into student values (1, 'zhangfei', '123', 1);
    insert into student values (2, 'zhangfei2', '124', 2);
    insert into student values (3, 'zhangfei1', '121', 3);
    insert into student values (4, 'guanyu', '120', null);
    	--    insert into student values (3, 'liubei', '121', 4);
#### 多对多关系
	drop table student;
	create table student (
	    id number(10) constraint stu_pk primary key,
	    name varchar2(20) not null,
	    birthday date
	);
	
	drop table lession;
	create table lession (
	    id number(10) constraint les_pk primary key,
	    name varchar2(20) not null
	);
	
	drop table stu_les_relation;
	create table stu_les_relation(
	    s_id number(10),
	    l_id number(10),
	    constraint stu_rel_fk foreign key(s_id) references student(id) on delete cascade,
	    constraint les_rel_fk foreign key(l_id) references lession(id) on delete cascade
	);
### sequence
创建序列解决主键自动增长问题

    create sequence seq_student start with 100;
    
    
    insert into student values (seq_student.nextval, 'zhangfei', '123', 1);
    insert into student values (seq_student.nextval, 'liubei', '124', 2);
    insert into student values (seq_student.nextval, 'zhaoyun', '121', 3);
    insert into student values (seq_student.nextval, 'guanyu', '120', null);

### plsql
### plsql
### plsql
#### plsql的基本格式
1.	declare
--申明区
2. begin
--执行区
3. exception
--异常处理函数
4. end;

#### constant
----代表常量

#### 数据类型
1. 标量类型
  数字   binary_integer  number
  字符串 char(n)  varchar2(n)
  布尔   boolean ---- true  false  null
  日期   date

2. 组合类型
  record(记录)   table(表)   cursor(游标)

3. 引用类型
  ref cursor （参考类型）
4. 大类型
  blob
  clob
  bfile

#### into
将查询的一条结果放到变量中

	declare
	    id    number;
	    name  varchar2(10);
	begin
	    select empno,ename into id,name -- into 就是把结果放入变量中
	    from emp where empno=7369;
	    dbms_output.put_line(id||':'||name);
	end;
#### %type 
可以获取一个表中的基本类型来创建变量
	
	declare
	    id    emp.empno%type;
	    name  emp.ename%type;
	begin
	    select empno,ename into id,name
	    from emp where empno=7369;
	    dbms_output.put_line(id||':'||name);
	end;

#### RECORD
结构体类型，将一些类型组合到一个整体
	
	declare
	    /*定义一个记录类型 叫emprectype*/
	    TYPE  emprectype  IS RECORD(
	        id     emp.empno%type,
		name   emp.ename%type,
		salary emp.sal%type
	    );
	    /*用上面的类型定义一个变量*/
	    var_emp   emprectype;
	begin
	    select empno,ename,sal  into var_emp -- 次序不可混乱
	    from emp where empno=7369;
	    dbms_output.put_line(var_emp.id||':'||
	    var_emp.name||':'||var_emp.salary);
	end;

#### %rowtype  
可以获得一个记录类型

	emp%rowtype   dept%rowtype
	
	一个表中的所有字段对应的一个记录类型
	字段排放顺序 和desc 表名得到的顺序相同并且字段名和record的成员名也一致。
	
	declare
	   /*用记录类型定义变量*/
	   var_emp  emp%rowtype;
	begin
	   select * into var_emp  from emp where empno=7369;
	   dbms_output.put_line(var_emp.empno||':'
	   ||var_emp.ename);
	end;

#### table
和数组差不多

	declare
	    /*定义一个table类型 放整数类型*/
	    TYPE  tablenumbertype  IS TABLE  OF
	    emp.empno%type index by  binary_integer;
	    /*定义一个table 类型的变量*/
	    var_nums  tablenumbertype;
	begin
	    var_nums(0):=100;
	    var_nums(3):=200;
	    var_nums(4):=400;
	    dbms_output.put_line('var_nums(4):'
	    ||var_nums(4));
	end;

### 控制语句
#### if语句
条件语句

	if   表达式   then
	    满足条件
	elsif  表达式 then
	
	elsif  表达式 then
	
	else
	
	end if;
	-------------exit   when   退出条件;
	exit   when   退出条件;

#### loop
	loop
	end  loop;
#### while
	while  条件  loop
	
	end  loop;
#### for
	for i in 1..10 loop
	    dbms_output.put_line(i);
	end loop;
#### 退出多重循环
	begin
	<<outer>>
	for  a in reverse 1..10 loop
	    dbms_output.put_line(a);
	    for b in 1..10 loop
	        if b=5 then
		   exit outer;
		end if;
	        dbms_output.put_line(b);
	    end loop;
	end  loop;
	end;

### 动态执行sql语句
	sqlstr:='create table testddl(id  number,name varchar2(30))';
	execute immediate sqlstr;    -- 执行动态SQL
#### 参数绑定
	sqlstr:='insert into testddl values(:b0,:b1)';
	execute  immediate  sqlstr using var_id,var_name;
	commit;
### cursor 游标
游标------用于提取多行数据集

#### 游标的使用
1. 声明游标
2. 打开游标
3. 提取数据
4. 关闭游标

			declare
			     cursor  empcursor is select ename  -- 1 声明游标
			     from emp;
			     var_name  emp.ename%type;
			begin
			     open empcursor;  -- 2 打开游标
			     fetch  empcursor into var_name; -- 3 提取数据(需要循环)
			     dbms_output.put_line(var_name);
			     fetch  empcursor into var_name;
			     dbms_output.put_line(var_name);
			     close  empcursor;  -- 4 关闭游标
			end;

#### 遍历游标所有的数据
使用游标的属性？
--------》found 和 notfound前要先fetch
1. %found   当游标提取数据时  如果提取到了新数据

		 则返回true  否则返回false
			 前提是游标被打开了
		 如果没有fetch  则返回NULL
2. %notfound  前提游标被打开

     当游标提取不到新数据时返回true
	   		否则返回false
     如果没有fetch  则返回NULL

3. %isopen    游标是否已经打开

    打开就返回真
	   否则是false

4. %rowcount   游标指针偏移量

##### loop游标
loop  游标提取不到数据退出循环
##### while 游标可以提取到数据则继续循环 要先fetch

##### for循环 遍历游标
##### 智能循环
  for循环能自动打开游标，读取数据，关闭游标。

			declare
			        cursor empcursor is select * from emp order by empno;
			        var_emp emp%rowtype;
			begin
			/*
			        open empcursor;
			        loop
			        fetch empcursor into var_emp;
			        exit when empcursor%notfound;
			        dbms_output.put_line(var_emp.empno || ':' || var_emp.ename);
			        end loop;
			        close empcursor;
			*/
			
			        open empcursor;
			        fetch empcursor into var_emp;
			        while empcursor%found loop
			        dbms_output.put_line(var_emp.empno || ':' || var_emp.ename);
			        fetch empcursor into var_emp;
			        end loop;
			        close empcursor;
			/*
			        for var_emp1 in empcursor loop
			        dbms_output.put_line(var_emp1.empno || ':' || var_emp1.ename);
			        end loop;
			*/
			end;

##### 游标传参
##### 游标传参
##### 游标传参
##### 游标传参
##### 游标传参
			declare
			        cursor empcursor(var_sal number) is
			                select * from emp where sal>var_sal order by empno;
			        var_emp emp%rowtype;
			begin
			        open empcursor(1000);
			        fetch empcursor into var_emp;
			
			        while(empcursor%found) loop
			        dbms_output.put_line(var_emp.empno || ':'
			                        || var_emp.ename || ':' || var_emp.sal);
			        fetch empcursor into var_emp;
			        end loop;
			        close empcursor;
			
			end;

##### 参考游标

				--------------exception
				raise -----》抛出
				
				declare
				    var_name emp.ename%type;
				begin
				    select ename into var_name from emp where empno > 1;
				    dbms_output.put_line(var_name);
				exception
				    when no_data_found then
				        dbms_output.put_line('no data found');
				    when too_many_rows then
				        dbms_output.put_line('many rows');
				    when others then
				        dbms_output.put_line('other');
				end;
	
	
	
				create or replace procedure getmax
				is
				begin
				end;
				
				create or replace procedure getmax
				is
				    var_i number:=1;
				    var_j number:=100;
				begin
				    if var_i > var_j then
				        dbms_output.put_line(var_i);
				    else
				        dbms_output.put_line(var_j);
				    end if;
				
				end;

----》带参数存储过程XU
-------》覆盖，不是重载
create or replace procedure getmax(var_i number, var_j number)
is
begin
    if var_i > var_j then
        dbms_output.put_line(var_i);
    else
        dbms_output.put_line(var_j);
    end if;

end;
-----》out
create or replace procedure zhuanzhang(acc_id_from number, acc_id_to number, money out number)
----》in default
create or replace procedure getmax(var_i number default -1, var_j in number default -1)
is
begin
    if var_i > var_j then
        dbms_output.put_line(var_i);
    else
        dbms_output.put_line(var_j);
    end if;

end;
#### 示例
---------》转帐

					create table account (
					    id number(10) constraint acc_pk primary key,
					    name varchar2(20) not null,
					    money number(10, 2)
					);
					create or replace procedure zhuanzhang(acc_id_from number, acc_id_to number, money number)
					is
					    from_money number;
					    sqlstr varchar2(500);
					begin
					    select money into from_money from account where id=acc_id_from;
					    dbms_output.put_line('余额：'|| from_money);
					    if money <= 0 then
					        dbms_output.put_line('转帐金额必需大于零');
					        dbms_output.put_line('转帐金额：'|| money);
					    elsif from_money < money then
					        dbms_output.put_line('余额不足');
					        dbms_output.put_line('余额：'|| from_money);
					        dbms_output.put_line('转帐金额：'|| money);
					    else
					        sqlstr := 'update account set money=money+:mn1 where id=:id1';
					        execute  immediate  sqlstr using -money, acc_id_from;
					        execute  immediate  sqlstr using money, acc_id_to;
					        commit;
					        dbms_output.put_line('转帐成功');
					        dbms_output.put_line('余额：'|| from_money);
					        dbms_output.put_line('转帐金额：'|| money);
					    end if;
					exception
					    when no_data_found then
					        dbms_output.put_line('帐号不存在,请核对');
					        dbms_output.put_line('转帐帐号：' || acc_id_from);
					        dbms_output.put_line('接收帐号：' || acc_id_to);
					        rollback;
					    when others then
					        rollback;
					end;

#### function
		create or replace function getmax(x number, y number)
		return number
		is
		begin
		    if x>y then
		        return x;
		    end if;
		    return y;
		end;

### 示例
### 示例
### 示例
### 示例

----》子查询优于主查询



    drop table student;
    create table student (
        id number(7) ,
        name varchar2(20) not null,
        --name varchar2(20) constraint abc not null,
        --测试语法可以过，但触发null异常时不提示abc
        mobile varchar2(20) unique
        --mobile varchar2(20) constraint stu_uni unique
            --唯一约束 constraint 约束名 unique
    );
    insert into student values(1, 'zhangfei', '123');
    insert into student values(2, 'guanyu', '123');
    insert into student values(3, null, '120');
    commit;

-	商城网站×作业
		商城网站×作业
		商城网站×作业
		
		drop table commodity;
		create table commodity (
		    id number(10) constraint com_pk primary key,
		    name varchar2(50) not null,
		    price number(12,2) not null
		);
		drop table userinfo;
		create table userinfo (
		    id number(10) constraint user_pk primary key,
		    username varchar2(20) unique not null,
		    password varchar2(40) not null,
		    address varchar2(100),
		    phone varchar2(20) not null
		);
		
		drop table orderinfo;
		create table orderinfo (
		    id number(10) constraint order_pk primary key,
		    user_id number(10),
		    create_date date default sysdate,
		    constraint user_order_fk foreign key(user_id) references userinfo(id)
		);
		drop table item;
		create table item (
		    order_id number(10),
		    com_id number(10),
		    quantity number(5) default 1,
		    constraint order_item_fk foreign key(order_id) references orderinfo(id)
		        on delete cascade,
		    constraint com_item_fk foreign key(com_id) references commodity(id)
		        on delete cascade
		);
		
		drop sequence seq_user;
		drop sequence seq_commod;
		drop sequence seq_order;
		create sequence seq_user;
		create sequence seq_commod;
		create sequence seq_order;
		
		insert into commodity values(seq_commod.nextval, 'HuoTuiChang', 5.5);
		insert into commodity values(seq_commod.nextval, 'FangBianMian', 1.4);
		insert into commodity values(seq_commod.nextval, 'ChunNiuNai', 7.2);
		insert into commodity values(seq_commod.nextval, 'WeiHua', 4.3);
		insert into commodity values(seq_commod.nextval, 'QiaoKeLi', 8.7);
		
		insert into userinfo values(seq_user.nextval, 'ZhaoYun', '123456', 'HuiLongGuan', '18723452384');
		insert into userinfo values(seq_user.nextval, 'HuangZhong', '123456', 'TianAnMen', '18723452384');
		insert into userinfo values(seq_user.nextval, 'GuanYu', '123456', 'HeiLongJiang', '18723452384');
		insert into userinfo values(seq_user.nextval, 'ZhangFei', '123456', 'DongHai', '18723452384');
		insert into userinfo values(seq_user.nextval, 'MaChao', '123456', 'WuZhiShan', '18723452384');
		
		commit;
		
		insert into orderinfo(id,user_id) values(seq_order.nextval, 2);
		insert into orderinfo(id,user_id) values(seq_order.nextval, 1);
		insert into orderinfo(id,user_id) values(seq_order.nextval, 3);
		insert into orderinfo(id,user_id) values(seq_order.nextval, 5);
		insert into orderinfo(id,user_id) values(seq_order.nextval, 4);
		insert into orderinfo(id,user_id) values(seq_order.nextval, 3);
		insert into orderinfo(id,user_id) values(seq_order.nextval, 5);
		insert into orderinfo(id,user_id) values(seq_order.nextval, 2);
		insert into orderinfo(id,user_id) values(seq_order.nextval, 2);
		insert into orderinfo(id,user_id) values(seq_order.nextval, 4);
		insert into orderinfo values(seq_order.nextval, 4, to_date('14:24', 'hh24:mi'));
		
		commit;
		
		insert into item values(11, 1, 3);
		insert into item values(12, 2, 5);
		insert into item values(13, 3, 5);
		insert into item values(14, 4, 3);
		insert into item values(15, 5, 5);
		insert into item values(16, 3, 5);
		insert into item values(17, 1, 3);
		insert into item values(18, 2, 5);
		insert into item values(19, 3, 5);
		insert into item values(20, 4, 3);
		
		
		insert into item values(11, 1, 2);
		insert into item values(12, 2, 4);
		insert into item values(13, 3, 3);
		insert into item values(14, 4, 2);
		insert into item values(15, 5, 34);
		insert into item values(16, 3, 4);
		insert into item values(17, 1, 2);
		insert into item values(18, 2, 4);
		insert into item values(19, 3, 1);
		insert into item values(20, 4, 2);
		
		
		insert into item values(11, 1, 3);
		insert into item values(12, 2, 3);
		insert into item values(13, 3, 6);
		insert into item values(14, 5, 2);
		insert into item values(15, 3, 3);
		insert into item values(16, 2, 4);
		insert into item values(17, 1, 5);
		insert into item values(18, 2, 6);
		insert into item values(19, 2, 7);
		insert into item values(20, 1, 3);
		insert into item values(21, 2, 5);
		insert into item values(21, 2, 7);
		insert into item values(21, 5, 121);
		insert into item values(21, 2, 4);
		insert into item values(21, 4, 53);
		
		commit;
		
		select o.id, i.quantity * c.price, c.name, i.quantity, u.username, u.address, u.phone from userinfo u
		left outer join orderinfo o on(u.id=o.user_id)
		left outer join item i on(o.id = i.order_id)
		left outer join commodity c on(i.com_id=c.id);
		
		select o.id,to_char(o.create_date, 'yyyy-mm-dd hh24:mi:ss'),
		c.id comid, i.quantity, i.quantity * c.price, u.id userid from userinfo u
		left outer join orderinfo o on(u.id=o.user_id)
		left outer join item i on(o.id = i.order_id)
		left outer join commodity c on(i.com_id=c.id)
		where to_char(o.create_date, 'hh24') between 14 and 15;
