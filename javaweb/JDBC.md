# JDBC
## 



## JDBC连接六步
1. 注册驱动
   1. 方法一:
```
    Driver driver=new com.mysql.cj.jdbc.Driver();
	DriverManager.registerDriver(new com.mysql.cj.jdbc.Driver());
```
   2. 方法二(常用)
```
   Class.forName("com.mysql.cj.jdbc.Driver");
   
   mysql的Driver文件中有一个static语句块，已经写好了注册驱动，
   只需要进行类加载就可以使用(static语句块中的代码再类加载时自动调用)
		使用反射机制进行类加载，
			不需要返回值
   ```
2. 连接数据库
```
   Connection connection=DriverManager.getConnection(url,user,password);
```
3. 获取预编译的数据库操作对象
   防止sql注入:（PreparedStatement,执行效率高）  常用
```
   String sql="";
   先写sql语句的框架，如："select * from emp where dname=? and sal=?", 
   ?表示一个占位符，一个？将来接受一个“值",占位符不能使用引号括起来)
    
   //获取预编译的数据库操作对象,这里将sql语句框子给DBMS,DBMS进行sql语句的预先编译
   PreparedStatement ps=connection.prepareStatement(sql);  
	
   //给占位符？传值（第一个？下标是1，第二个？下标是2，JDBC中所有的下标从1开始）
	ps.setString(1,value1);
	ps.setString(2,value2);
	
    【推送多条数据】
        需要在新的sql语句生成后，将SQL语句作为一个整体添加到ps中
	ps.addBatch()
        //然后一次性将ps中的所有数据都推送到msql中
	ps.executeBatch()
	
	如:
	    String sql="insert into dept values(?,?,?)
	    for(int i=0;i<100;i++){
	        ps.setInt(1,i);
	        ps.setString(2,"dept_"+i;)
	        ps.setString(3,"上海");
	        ps.addBatch();    
	    }
	    ps.executeBatch();

```
4. 执行sql语句
```
    1.非查询语句:ps.excuteUpdate(),返回改变的行数
    
    2. 查询语句
    ResultSet resultSet=ps.excuteQuery()
    
```
5. 操作sql(如果有查询语句才有第5步)
```aidl
    resultset.next();
    使用next()方法判断是否有数据(True/False)，
	如果有数据，使用getString("列名/列号")拿出数据，还有(getInt(),getDouble()等)

	例：
	resultset.next();
	String str=resultset.getString("列名");如果列名设置了别名，这里填别名
			
	【循环读取】使用while(resultset.next()){xxxx}读取多条数据
```
6.释放资源  
对象.close



	-Statement存在sql注入问题，PreparedStatement解决了sql注入的问题
	-Statement是编译一次执行一次;PreparedStatement是编译一次，可以执行N多次。PreparedStatement的效率要高一些
	-PreparedStatement会在编译阶段做类型的安全检查

	PreparedStatement使用较多，只有少数情况下使用Statement
使用Statement的情况：
-业务方面要求必须支持SQL注入的时候
-业务方面要求是需要进行SQL语句拼接的，必须使用Statement

## 事务提交
JDBC中的事务是自动提交的  
只要执行任意一条DML语句，则自动提交一次。这是JDBC默认 的事务行为  
但是在实际的业务中，通常都是N条DML语句共同联合才能完成的必须同时成功或同时失败  



+ 关闭自动提交，将自动提交改为手动提交  
Connection connection=DriverManager.getConnection(url,user,password);
connection.setAutoCommit(false);关闭自动提交

    * 提交事务
connection.commit();

    * 回滚事务；当发生错误时
connection.rollback();

## 事务管理
JDBC中只要执行任意一条DML语句都会自动提交一次  


+ 关闭自动提交，改为手动提交  
    connection.setAutoCommit(false);

+ 回滚事务  
    connection.rollback();  
    当sql发生异常时回滚(catch(SQLException s){connection.rollback();})  
  
+ 提交事务  
    connection.commit()





## 行级锁（悲观锁）
select语句的最后面添加 for update
在当前事务没有结束的时候，被查询的结果被锁定，其他事务不能对指定事务进行修改
别的事务只能等待当前事务执行完毕才能对这条语句所查询出来的内容进行操作


悲观锁：事务必须排队执行。数据锁住了，不允许并发。
乐观锁：支持并发，事务也不需要排队，只不过需要一个版本号。


## DAO封装
(将数据库操作封装在一个类中，使用时直接调用方法传参数)
### 介绍  
1. DAO=DateBase Access Object;数据库访问对象
2. 作用:数据库访问对象在开发时提供针对某张表的操作细节【增删改查】
3. 优点:
    + 在管理系统开发时，通过数据库访问对象可以避免反复的SQL命令书写
    + 在关系系统开发时，通过数据库访问对象可以避免反复的JDBC开发步骤
4. DAO类：提供数据库访问对象的类
### DAO类开发规则
1. 一个DAO类中封装的是一张表操作细节
2. DAO类名规则:表名+Dao,如:empDao
3. DAOL类所在包命名规则：公司网站域名.dao, 如:com.公司.dao

## 实体类
1. 一个实体类用于描述一张表的结构
2. 实体类的类名应该与关联的表名保持一致，但是可以忽略大小写  
   【dept.frm------>public class Dept{}】
3. 实体类的属性应该与关联的表文件中的字段保持一致
```
Dept.frm --------------->public class Dept{
deptno int,                 private Integet deptNo;
dname varchar(20)           private String ename;
loc   varchar(20)           private String loc;
                            }
```
4. 实体类的一个实例对象用于在内存中存储对应的表文件中一个数据行
```
        Dept.frm --------------------> JVM内存
DeptNo  dname     loc                           
10      金融      上海                  Dept dept1=new Dept(10,"金融","上海");
20      科研      北京                  Dept dept1=new Dept(20,"科研","北京");

```
    

