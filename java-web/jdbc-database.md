# JDBC操作数据库

- 概念：`Java DataBase Connectivity` Java 数据库连接，可以使用Java语言操作数据库；
- JDBC本质：其实是官方(sun公司)定义的一套操作所有关系型数据库的规范，即接口。各个数据库厂商去实现这套接口，提供数据库驱动jar包。我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动jar包中的实现类。

## jdbc基本使用流程

1. 导入驱动jar包；

2. 注册驱动；

   `Class.forName("com.mysql.jdbc.Driver");`

3. 获取数据库连接对象；

   `Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db", "root", "root");`

4. 定义sql；

   `String sql = "select * from user";`

5. 获取执行sql语句的对象 Statement；

   `Statement stmt = conn.createStatement();`

6. 执行sql，接受返回结果；

   `ResultSet result = stmt.executeQuery(sql);`

7. 处理结果；

   `System.out.println(result);`

8. 释放资源

   `stmt.close();`
   `conn.close();`

## Jdbc驱动对象详解

1. DriverManager：驱动管理对象

   - **功能**：

   1. 注册驱动：告诉程序使用哪种数据库驱动jar	

      方法：`static void registerDriver(Driver driver)`

      问题：写代码时为什么使用`Class.forName("com.mysql.jdbc.Driver");`来注册驱动呢？

      解释：在`com.mysql.jdbc.Driver`类中存在如下静态代码块，而静态代码块会随着类的加载而同步加载到内存中，从而达到了使用DriverManager来注册驱动的目的。

      ```java
      static {
          try {
              java.sql.DriverManager.registerDriver(new Driver());
          } catch (SQLException E) {
              throw new RuntimeException("Can't register driver!");
          }
      }
      ```

      注意：mysql5之后的驱动jar包，可以省略注册驱动的步骤。

   2. 获取数据库连接：

      方法：`static Connection getConnection(String url, String user, String password) `

      参数：

      - url：`jdbc:mysql://ip地址(域名):端口号/数据库名称`；
      - user：数据库用户名；
      - password：数据库用户密码；

      如果连接的是本机mysql服务器，并且mysql服务默认端口是3306，则url可以简写为：jdbc:mysql:///数据库名称。

2. Connection：数据库连接接口

   - 功能

   1. 获取执行sql的对象

      `Statement createStatement()`

      `PreparedStatement prepareStatement(String sql) `

   2. 管理事务

      - 开启事务：setAutoCommit(boolean autoCommit) 调用该方法设置参数为false，即开启事务;
      - 提交事务：commit() ;
      - 回滚事务：rollback();

3. Statement：执行SQL的接口

   - `boolean execute(String sql) `：可以执行任意的sql；
   - `int executeUpdate(String sql)`:执行DML（insert、update、delete）语句、DDL(create，alter、drop)语句；
     - 返回值：影响的行数，可以通过这个影响的行数判断DML语句是否执行成功 返回值>0的则执行成功，反之，则失败。
   - `ResultSet executeQuery(String sql) ` ：执行DQL（select)语句；

   ```java
   Statement stmt = null;
   Connection conn = null;
   try {
       //1.注册驱动
       Class.forName("com.mysql.jdbc.Driver");
       //2.创建连接
       conn = DriverManager.getConnection("jdbc:mysql:///db1", "root", "root");
       //3.sql
       String sql = "insert into stu2 values(null,'Tom',18)";
       //4.创建Statement执行sql对象
       stmt = conn.createStatement();
       //5.执行sql
       int result = stmt.executeUpdate(sql);
       //6.处理结果
       System.out.println(result);
       if (result > 0) {
           System.out.println("添加成功");
       } else {
           System.out.println("添加失败");
       }
   } catch (ClassNotFoundException e) {
       e.printStackTrace();
   } catch (SQLException e) {
       e.printStackTrace();
   } finally {
       //7.释放资源
       if (stmt != null) {
           try {
               stmt.close();
           } catch (SQLException e) {
               e.printStackTrace();
           }
       }
       if (conn != null) {
           try {
               conn.close();
           } catch (SQLException e) {
               eprintStackTrace();
           }
       }
   }
   ```

4. ResultSet：结果集接口

   - `boolean next()`: 游标向下移动一行，判断当前行是否是最后一行末尾(是否有数据)，如果是，则返回false，如果不是则返回true；

   - `getXxx(参数)`:获取数据

     * Xxx：代表数据类型   如： `int getInt()` ,	`String getString()`；

     * 参数：int：代表列的编号,从1开始   如： `getString(1)`；

       		   String：代表列名称。 如： `getDouble("name")`；

   ```java
   //提升对象作用域，方便释放资源
   Connection conn = null;
   Statement stmt = null;
   ResultSet rs = null;
   try {
       //注册驱动
       Class.forName("com.mysql.jdbc.Driver");
       //创建数据库连接对象
       conn = DriverManager.getConnection("jdbc:mysql:///db1", "root", "root");
       //定制sql
       String sql = "select * from stu2";
       //创建执行sql的对象Statement
       stmt = conn.createStatement();
       //执行sql
       rs = stmt.executeQuery(sql);
       //处理结果
       while (rs.next()) {
           int id = rs.getInt(1);//传索引
           String name = rs.getString("name");//传列名
           int age = rs.getInt("age");
           System.out.println(id + "**" + name + "**" + age);
       }
   } catch (ClassNotFoundException e) {
       e.printStackTrace();
   } catch (SQLException e) {
       e.printStackTrace();
   } finally {
       //释放资源
       if (rs != null) {
           try {
               rs.close();
           } catch (SQLException e) {
               e.printStackTrace();
           }
       }
       if (stmt != null) {
           try {
               stmt.close();
           } catch (SQLException e) {
               e.printStackTrace();
           }
       }
       if (conn != null) {
           try {
               conn.close();
           } catch (SQLException e) {
               e.printStackTrace();
           }
       }
   }
   ```

5. PrepareStatement：执行SQL的接口，Statement的子接口

   1. SQL注入问题：在拼接sql时，有一些sql的特殊关键字参与字符串的拼接。会造成安全性问题
   				- 输入用户随便，输入密码：a' or 'a' = 'a
   	
   	- sql：`select * from user where username = 'fhds' and password = 'a' or 'a' = 'a' `

   2. 解决sql注入问题：使用PreparedStatement对象来解决；

   3. 预编译的SQL：参数使用?作为占位符；

   4. 步骤：

      - 注册驱动
      - 获取数据库连接对象Connection

      - 定义SQL时：sql的参数使用？作为占位符。

         如：`select * from user where username = ? and password = ?`

      - 获取执行sql语句的对象 PreparedStatement  

        `Connection.prepareStatement(String sql) `

      - 给?赋值：

        方法： setXxx(参数1,参数2)

        * 参数1：?的位置编号 从1 开始
        * 参数2：?的值

      - 执行sql，接受返回结果，不需要传递sql语句

## Jdbc数据库连接池

- 概念：祈使句就是一个存放数据库连接的容器（集合）；当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，访问完后，将连接对象归还给容器。

- 优点：节约资源、用户访问高效；

- 使用：

  标准接口：DataSource，javax.sql包；

  方法：

  - 获取连接：getConnection()

  - 归还连接：close() 这里不同于普通使用方法的关闭连接释放资源，而是归还连接对象给连接池；

- 实现：由提供数据库驱动jar的数据库厂商来实现；

- 常用的数据库连接池：

  - C3P0：
  - Druid：ali