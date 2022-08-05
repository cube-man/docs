# jdbc

## 概念

1. Java Database Contectivity
1. 是 java 连接连接数据库的一套规范

## 基本步骤

1. 注册驱动（jdbc 驱动 4+可以省略，会自动注册）
2. 获取连接
3. sql
4. 编译
5. 执行
6. 获取结果集
7. 关闭连接，释放资源

```java
//未加try catch
// 注册驱动
Class.forName("com.mysql.cj.jdbc.Driver");
// 获取连接
Connection con = DriverManager.getConnection(url, "root", "root");
// sql
String select_statement = "select ? from xx where id = ?";
// 编译动态sql语句(可以解决sql注入问题) （createStatement() 编译静态）
 PreparedStatement ps = con.prepareStatement(sql);
 //
 ps.setXx(1,xx);
 ps.setObject(2,xx)
//运行&获取结果
ResultSet rs = statement.executeQuery(select_statement);
if(rs.next()){
  rs.getInt("cname1");
  rs.getString("cname2");
}
//关闭连接
rs.close();
ps.close();
con.close();
```

## OO 化 jdbc 优化封装成框架（ORM）Object Relation Mapping

### 封装内容

1. DAO
2. DTO

### 封装细节

- 模板方法化 baseDao,RowMapper

- 将连接数据库的要素抽离出来，封装到工具类（DbHelper）中,并在此接口中提供 getConnection(), close() 的默认实现，避免重复获取连接与释放资源
- 提供一个函数接口 RowMapper\<T\>, 可以继承接口后，实现 mapping 方法，完成 javabean 与表字段的映射
- 将 dao 的几种常用功能（CRUD findById(RowMapper mapper,int id),findAll,update,save,delete）抽取出来,封装在一个 BaseDao\<T\> 提供实现,使用需返回固定类型的方法时，依靠方法传入的具体映射类，获取并返回具体类型，既保证了其在 sql 操作是的通用性，又不至于过于宽泛（Object）
