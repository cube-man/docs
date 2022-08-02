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

## 视图

## 存储过程
