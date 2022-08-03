# mysql

-

```sql
select a1.*[ ,a2.xxx, count(1) sum,min(a.xxx), a1.xxx] from xxx1 a1
join xxx2 a2 on a1.xxx = a2.xxx
group by a2.xxx[,a2.xxx,a1.xxx]
where a1.xxx between 'xxx' and 'xxx'
order by a1.xxx[,a1.xxx,a2.xxx] desc
having
a1.xxx >= a1.xxx and a2.xxx = a1.xxx
```

## 单表查询

```sql
select * from xxx
select distinct column1,colum2 from table1 as ss
where ( column1 = xxx and column2 = xxx ) or ( column3==xx )
order by ss.column desc;
```

- `select`： 表示是查询语句
- `from`： 从那张表查出来
- `where`： 限定条件，遍历结果如果条件满足，就会返回至最终结果集
  - ( ) `and` ( )： 左表达式和右表达式都要满足
  - ( ) `or` ( )： 左表达式满足或右表达式满足任一满足即可
  - `not` ( )： 取反
  - `[not] in`： \[不\]在右边的结果集内
  - cloumn `between` xxx `and` xxx： 在 xxx 和 xxx 内
  - `like` :
    - like %xx%：包含 xx
    - like xx%：以 xx 开头
    - like %xx：以 xx 结尾
  - 正则表达式： ^:开头 $:结尾 | 逻辑或
  - null 查询：
    - column is null
    - column is not null
- `order by`： 根据哪些字段排序,
  - `desc`：倒序，就近原则
- `as`： 给表或字段取别名，可以不显示的写出，在要起别名的字段或表加空格再起也可以
- `limit` \[offset,\] size：查询从 offset 开始，size 长度

## 多表查询

1. 内连接
   ```sql
    select xxx from table1 t1 [inner] join/, table2 t2 on t1.xxx>t2.xxx;
   ```
   - `inner join`：符合条件的才返回给结果集(显式)
   - t1,t2：隐式
2. 自连接：自己 join 自己
3. 联合查询：联合 id 在两张表，需在一起才可标示独一性，pk1 或 pk2 可重复，pk1,pk2 的组合不可重复
4. 外连接：
   1. `left join`:左边无论符合与否都会显示
   2. `right join`: 右
5. using 从句 `using`(mysql8.0+)：using (xxx) 同 on t1.xxx = t2.xxx
6. 自然连接：`natural join`：数据库自动匹配相同的列作为连接条件
7. 交叉连接：`cross join`笛卡尔积
8. 联合查询：() union ()：两语句返回的列数要一样，同列区分需要同别名

## 增删改

### 列属性

- Column：列名称 word1_word2
- DataType: 数据类型
- PK (primary key)：主键，每张表都要有
- NN (not null): 非空
- AI (auto increment)：自增
- Default：默认值

### insert

```sql
insert into table1 values (c1v,c2v,...)[,(c1v,c2v,...)];
insert into table1(c2,c1,..) values(c1v,c1v,...)
create table t2 as select xx,xx ss from t1;
```

1. 不限定句段需写全,可以插入多条
2. 限定需写有的，不可以多或少,可乱序但需要对应
3. 复制表，并替换语句
4. 一般加 where 防止误操作整张表

### update

```sql
 update t1 set (c1=c1v,c2=c2v,...) where c1=xxx;
```

1. 一般加 where 防止误操作整张表

### delete

```sql
  delete from t1 where c1=xx
```

1.  一般加 where 防止误操作整张表

## 统计查询

### 聚合函数

- 接收一些参数并将其合并为一个结果

```sql
min(xx) max(xxx) avg(xxx) sum(xxx) count(xxx)
```

1. min()：返回结果集中的最小
2. max()：最大
3. avg()：平均
4. sum()：求和
5. count()：计数

### 分组查询

```sql
select c2,count(1) from t1 where c1 > xx group by c2,c4 having c3 < xx
```

1. 分组返回的结果集行数需与分组情况一致
2. 可多个，范围应从大到小
3. `having`：将前面得到的结果集处理再返回

## 高级查询

### 子查询和连接查询

1. 连接查询性能更高

### 关联子查询

```sql
 select * from table1 t1 where c1 >
  ( select c1 from table1 t2 where t2.c2 = t1.c2  )
```

1. 非关联子查询:子查询独立，先子查询，然后主查询，子查询只进行一次
2. 关联子查询：子查询关联外部记录，先主查询，再子查询，子查询需查询多次，性能较慢

### all & any(some)

1.any(some)：mysql8.0+

### exists

```sql
  select * from t1 where  exists
  ( select c1 from t2 where t1.c1 = t2.c1 )
```

1. 如果 in 后面是一个较大的结果集，exists 的性能相对较好，exists 不会将结果集返回给外部查询，只检验真假

## 函数

1. 数字函数

   ```sql
   -- 四舍五入 num：数字 def：精确度
   round(num,def) round(5.67,1) -- = 5.6
   -- 截断
   truncate(num,def)
   -- 向上取整
   ceiling(num)
   -- 向下取整
   floor(num)
   -- 绝对值
   abs(num)
   -- 随机数0~1
   rand()
   ```

2. 字符串函数

   ```sql
   -- 获取长度
   select length('abc');
   -- 大小写
   select upper('abc');
   select lower('ABC');
   -- 去空格 ltrim trim rtrim
   -- 左右截取字符串
   select left('abcd',2);
   -- 任意位置截取字符串
   select substring('abcdefg',2,3);
   -- 返回第一次出现字符的位置
   select locate('abcdea');
   -- 替换
   select replace('asdfg','a','x');
   -- 合并字符串
   select concat('sda','open');
   ```

   3. 时间函数

   ```sql
   -- 获取当前时间
   select now(),curdate(),curtime();
   -- 获取日期年月日 year month day hour minute second dayname monthname
   select year(now());
   select extract(year from now());

   -- 日期格式化
   select date_format(now(),'%M %d %Y')
   select time_format(now(),'%H:%i %p')

   -- 日期加减计算
   select date_add(now(),interval 1 day);
   select date_sub(now(),interval 1 day);
   -- 日期间隔
   select datediff('2020-01-01','2020-02-01'); -- 天数
   select time_to_sec('09:00'); -- 0点到某时间点的秒数

   ```

3. 其他

   ```sql
   -- 转换null
   select order_id,
     ifnull(shipper_id,'没有记录'),
     coalesce(shipper_id,comments,'没有记录') -- 返回这一串值中第一个不为null的值
     from orders;
   -- 条件函数
   select order_id,order_date,
     if(
       year(order_date)=year(now()), -- 测试条件
       'active', -- true
       'archived' -- false
     )
     from orders;
   -- 多条件
   select order_id,order_date,
     case
       when year(order_date)=year(now()) then 'active'
       when year(order_date)=year(now())-1 then 'last year'
       when year(order_date)<year(now())-1 then 'archived'
       else 'future'
     end as category
     from orders;
   ```

## 视图

```sql
-- vname：视图名称
create view vname as select c1,c2 from t1;
drop view vname;
create or replace view vname as ...;
-- 当视图不包含以下内容时，可以更新
distinct
聚合函数
group by
having
union

insert update delete 同一般的表

-- with option check
```

## 存储过程

1. 是再数据库中保存代码块的对象
2. 大多数数据库有优化，会比普通 sql 快
3. 其中可以加入安全检测

```sql
delimiter $$
create procedure get_clients()
begin
	select * from clients;
end $$
delimiter ;

-- 调用
call get_clients();
-- drop procedure if exists get_clients;
```

## 触发器与事件

## 事务与并发

1. 事务的特性`ACID`：

   - `Atomicity`原子性，事务就像原子一样，各个操作合为一体，不可拆分
   - `Consistency`一致性，我们的数据永远保持一致，例如我们不会保存没有商品的订单信息。
   - `Isolation`隔离性，事务之间是相互隔离的，特别是它们要修改同一个数据的时候，它们之间互相不受影响。如果多个事务要修改同一份数据，这个数据就会被锁定，每次只能有一个事务有权修改。
   - `Durability`持久性，一旦一个事务被提交，它的修改就是永久性的，任何特殊情况比如停电，都不会影响这种数据修改。

2. 并发带来的问题：

   - `Lost Updates`丢失修改：当 2 个事务修改同一份数据而不使用锁时，会发生这种情况，后一个事务的修改会把前一次事务的修改覆盖掉。
   - `Dirty Reads`脏读：事务读取还没有提交的数据时，事务 A 对数据发生了修改，而事务 B 在 A 提交之前读取了 A 修改的数据，但是最后 A 回滚了，那么 B 读取的数据就是无效的。
   - `Non-repeating Reads`不可重复读：在一次事务 A 中 2 次读取同一份数据，但是事务 B 在 A 的 2 次读取之间对这份数据进行了修改和提交，那么事务 A 的 2 次读取结果将会不一样。简言之，一个事务读取同一条记录，会出现两个不同的状态值，又称为 ABA 事件。
   - `Phanton Reads`幻读：事务 B 对数据进行了修改，在 B 提交之前，事务 A 根据条件查询出若干条记录，但是在 B 提交之后，使得某些数据之前不符合 A 的要求，但是 B 修改提交后符合要求了。

3. 事务隔离级别(默认 Repeatable Read)

|                   | Lost Updates | Dirty Reads | Non-repeating Reads | Phanton Reads |
| :---------------: | :----------: | :---------: | :-----------------: | :-----------: |
| Read Uncommitted  |              |             |                     |               |
|  Read Committed   |              |      Y      |                     |               |
| Repeatable Read\* |      Y       |      Y      |          Y          |               |
|   Serializable    |      Y       |      Y      |          Y          |       Y       |

4. 死锁(deadlock)：死锁发生在所有事务都无法完成的情况下，因为每一个事务都持有另一个事务所需要的锁。所有事务都让对方等待，不会释放自己的锁。
   - mysql 遇到 deadlock,会自动回滚两个事务

## 数据类型

## 数据库设计

## DDL

1. 数据库语句类型
   1. DML：数据库操作命令
   2. DDL：数据库 xx 命令
   3. DQL：数据库查询命令

## 索引

## 权限管理
