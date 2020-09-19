# 0910 JDBC MVC

## 0 客户端与服务器
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2145106/1599642055135-b0773677-fcc8-466b-a37c-3bc6d692f75e.png#align=left&display=inline&height=433&margin=%5Bobject%20Object%5D&name=image.png&originHeight=866&originWidth=1542&size=233646&status=done&style=none&width=771)

## 
# 一、JDBC概述
JDBC:java database connectivity SUN公司提供的**一套操作数据库的标准规范**。
JDBC与数据库驱动的关系：接口与实现的关系。
 
JDBC规范（掌握四个核心对象）：
DriverManager:用于注册驱动
Connection: 表示与数据库创建的连接
Statement: 操作数据库sql语句的对象
ResultSet: 结果集或一张虚拟表
 
开发一个JDBC程序的准备工作：
**> JDBC规范在哪里：**
** JDK中：**
java.sql.*;
javax.sql.*;
**> 数据库厂商提供的驱动：jar文件**
  *.jar
![](https://cdn.nlark.com/yuque/0/2020/png/2145106/1599640868006-6c621c20-5e78-46be-81a7-50a55fc0df8c.png#align=left&display=inline&height=89&margin=%5Bobject%20Object%5D&originHeight=119&originWidth=296&status=done&style=none&width=222)


#### jdbc工作原理图解
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2145106/1599707604689-9e1d4ca4-bf42-4bb1-851f-1445076d82b3.png#align=left&display=inline&height=295&margin=%5Bobject%20Object%5D&name=image.png&originHeight=590&originWidth=1100&size=119705&status=done&style=none&width=550)
 


#### jdbc简单代码
```java
package com.qf.test;

//提供使用Java TM编程语言访问和处理存储在数据源（通常是关系数据库）中的数据的API
import java.sql.*;

public class Demo1 {
    public static void main(String[] args) throws SQLException {
        //1注册驱动-Driver
        DriverManager.registerDriver(new com.mysql.jdbc.Driver());

        //2获取连接-connection,尝试建立与给定数据库URL的连接。
        //选择公共包里的接口，扩大使用范围
        //SQLException - 如果发生数据库访问错误或url是 null
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/db2", "root", "123456");

        //2 创建小车并绑定sql语句-statement, 创建一个Statement对象，用于将SQL语句发送到数据库
        Statement statement = connection.createStatement();
        //编写sql语句
        String sql = "select empno,ename,job from emp";

        //4卸货-resultSet
        //执行给定的SQL语句，返回单个ResultSet对象。
        //参数 sql - 要发送到数据库的SQL语句，通常为静态SQL SELECT语句
        ResultSet set = statement.executeQuery(sql);

        //返回true如果新的当前行有效; false如果没有更多的行
        while (set.next()) {
            //此方法将以Java对象的形式返回给定列的值
            int empno = set.getInt("empno");
            String name = set.getString("ename");
            String job = set.getString("ename");
            System.out.println(empno + " " + name + " " + job);
        }
        //5关闭资源
        //Connection释放此Connection对象的数据库和JDBC资源，而不是等待它们自动释放。
        connection.close();
        statement.close();
        set.close();
    }
}
```


#### 模型封装
创建Emp模型
```sql
package com.qf.javabean;

/*
模型必须具备以下四个方面
1 属性
2 get set方法
3 构造方法
4 toString方法
 */
public class Emp {//类名最好和表明一致
    //属性名称最好和表中一致
    private int empno;
    private String ename;
    private String job;

    public Emp() {
    }

    public Emp(int empno, String ename, String job) {
        this.empno = empno;
        this.ename = ename;
        this.job = job;
    }


    @Override
    public String toString() {
        return "Emp{" +
                "empno=" + empno +
                ", ename='" + ename + '\'' +
                ", job='" + job + '\'' +
                '}';
    }

    public int getEmpno() {
        return empno;
    }

    public String getEname() {
        return ename;
    }

    public String getJob() {
        return job;
    }

    public void setEmpno(int empno) {
        this.empno = empno;
    }

    public void setEname(String ename) {
        this.ename = ename;
    }

    public void setJob(String job) {
        this.job = job;
    }
}

```
**使用模型**
```sql
package com.qf.test;
//模型封装
import java.sql.*;
import java.util.*;
import java.util.Properties;
import com.qf.javabean.*;

public class Demo3 {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {
        Class.forName("com.mysql.jdbc.Driver");
        Properties p =new Properties();
        p.setProperty("user","root");
        p.setProperty("password","123456");
        //尝试建立与给定数据库URL的连接
        Connection connection=DriverManager.getConnection("jdbc:mysql://localhost:3306/db2",p);
        //创建一个Statement对象，用于将SQL语句发送到数据库
        Statement statement = connection.createStatement();
        String sql = "select empno,ename,job from emp";
        //执行给定的SQL语句，返回单个ResultSet对象。
        ResultSet set = statement.executeQuery(sql);
        //封装
        List<Emp> list=new ArrayList<>();
        while (set.next()) {
            Emp emp=new Emp(set.getInt("empno"),set.getString("ename"),set.getString("ename"));
            list.add(emp);
        }
        System.out.println(list);//[Emp{empno=7369, ename='SMITH', job='SMITH'}, Emp{empno=7499, ename='ALLEN', job='ALLEN'}, Emp{empno=7521, ename='WARD', job='WARD'}, Emp{empno=7566, ename
        connection.close();
        statement.close();
        set.close();
    }
}
```


#### 代码优化
config，放在src文件下
```
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/db2
user=root
pwd=123456
```
创建工具类，可以复用
```java
package com.qf.utils;

import java.sql.*;
import java.util.*;

public class JDBCUtils {
    static String mydriver;
    static String myurl;
    static String mypwd;
    static String myuser;

    static {
        //一般来说，ResourceBundle类通常是用于针对不同的语言来使用的属性文件。
        //而如果你的应用程序中的属性文件只是一些配置，并不是针对多国语言的目的。那么使用Properties类就可以了。
        //使用指定的基本名称，默认语言环境和调用者的类加载器获取资源包。
        //参数baseName - 资源包的基本名称，一个完全限定的类名
        ResourceBundle resourceBundle = ResourceBundle.getBundle("config");
        mydriver = resourceBundle.getString("driver");
        myurl = resourceBundle.getString("url");
        myuser = resourceBundle.getString("user");
        mypwd = resourceBundle.getString("pwd");

        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    //返回一个连接对象
    public static Connection getConnection() throws SQLException {

        //尝试建立与给定数据库URL的连接
        return DriverManager.getConnection(myurl, myuser, mypwd);
    }

    //定义一个通用的关闭方法
    public static void closeALl(Connection connection, Statement statement, ResultSet set) {
        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
```
主类
```java
package com.qf.test;
//jdbc代码优化
import java.sql.*;
import java.util.*;
import com.qf.javabean.*;
import com.qf.utils.JDBCUtils;
public class Demo5 {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {

        Connection connection=JDBCUtils.getConnection();
        //创建一个Statement对象，用于将SQL语句发送到数据库
        Statement statement = connection.createStatement();
        String sql = "select empno,ename,job from emp";
        //执行给定的SQL语句，返回单个ResultSet对象。
        ResultSet set = statement.executeQuery(sql);
        //封装
        List<Emp> list=new ArrayList<>();
        while (set.next()) {
            Emp emp=new Emp(set.getInt("empno"),set.getString("ename"),set.getString("ename"));
            list.add(emp);
        }
        System.out.println(list);
        JDBCUtils.closeALl(connection,statement,set);
    }
}
```


## MVC

- **Model（模型）** - 模型代表一个存取数据的对象或 JAVA POJO。它也可以带有逻辑，在数据变化时更新控制器。
- **View（视图）** - 视图代表模型包含的数据的可视化。
- **Controller（控制器）** - 控制器作用于模型和视图上。它控制数据流向模型对象，并在数据变化时更新视图。它使视图与模型分离开。



![image.png](https://cdn.nlark.com/yuque/0/2020/png/2145106/1599724059067-dabe942e-40d6-4264-9338-5721b2d5893d.png#align=left&display=inline&height=367&margin=%5Bobject%20Object%5D&name=image.png&originHeight=733&originWidth=1546&size=206154&status=done&style=none&width=773)






### 模拟登录
待补充
# 三、JDBC常用的类和接口详解
## 1、java.sql.Drivermanager类 : 创建连接 

### a、注册驱动
DriverManager.registerDriver(new com.mysql.jdbc.Driver());不建议使用
原因有2个：
> 导致驱动被注册2次。
> 强烈依赖数据库的驱动jar
解决办法：
```java
		//方法一
        //注册给定的驱动程序DriverManager
        DriverManager.registerDriver(new com.mysql.jdbc.Driver());

        //方法2
        //在驱动类的代码中,我们可以看到有一个静态代码块。 静态代码块中已经做了注册驱动的事情。 \
        // 所以我们只需要加载驱动类，就相当于调用了 registDriver 方法。
        //如果调用registDriver 方法, 那么相当于创建了两个Driver对象,浪费资源.
        //使用forname的方式. 因为驱动类的名称是以字符串的形式填写,那么我们把该名称放到配置文件中,每次从配置文件中读取.那么切换驱动类就非常方便.
        // 也就意味着切换数据库方便
        Class.forName("com.mysql.jdbc.Driver");
```
### b、与数据库建立连接
```java
Properties p =new Properties();
        p.setProperty("user","root");
        p.setProperty("password","123456");
        //尝试建立与给定数据库URL的连接
        Connection connection=DriverManager.getConnection("jdbc:mysql://localhost:3306/db2",p);
```
## 2、java.sql.Connection接口：一个连接
接口的实现在数据库驱动中。所有与数据库交互都是基于连接对象的。
```java
常用方法：
createStatement()：创建向数据库发送sql的statement对象。
prepareStatement(sql) ：创建向数据库发送预编译sql的PrepareSatement对象。
prepareCall(sql)：创建执行存储过程的callableStatement对象。
setAutoCommit(boolean autoCommit)：设置事务是否自动提交。
commit() ：在链接上提交事务。
rollback() ：在此链接上回滚事务
```
 
```java
 		//创建一个Statement对象，用于将SQL语句发送到数据库
        Statement statement = connection.createStatement();
        String sql = "select empno,ename,job from emp";

```
 
## 3、java.sql.Statement接口: 
操作sql语句，并返回相应结果的对象（小货车）

```java
常用Statement方法：
execute(String sql):运行语句，返回是否有结果集
executeQuery(String sql)：运行select语句，返回ResultSet结果集。
executeUpdate(String sql)：运行insert/update/delete操作，返回更新的行数。
addBatch(String sql) ：把多条sql语句放到一个批处理中。
executeBatch()：向数据库发送一批sql语句执行。
```
```java
		//参数 sql - 要发送到数据库的SQL语句，通常为静态SQL SELECT语句
        ResultSet set = statement.executeQuery(sql);
```
## 4、java.sql.ResultSet接口: 结果集（客户端存表数据的对象）


```java
ResultSet提供检索不同类型字段的方法，常用的有：

getString(int index)、getString(String columnName)：获得在数据库里是varchar、char等类型的数据对象。
getFloat(int index)、getFloat(String columnName)：获得在数据库里是Float类型的数据对象。
getDate(int index)、getDate(String columnName)：获得在数据库里是Date类型的数据。
getBoolean(int index)、getBoolean(String columnName)：获得在数据库里是Boolean类型的数据。
getObject(int index)、getObject(String columnName)：获取在数据库里任意类型的数据。

ResultSet还提供了对结果集进行滚动的方法：

next()：移动到下一行
Previous()：移动到前一行
absolute(int row)：移动到指定行
beforeFirst()：移动resultSet的最前面。
afterLast() ：移动到resultSet的最后面。
使用后依次关闭对象及连接：ResultSet → Statement → Connection
```
  
```java
 //返回true如果新的当前行有效; false如果没有更多的行
        while (set.next()) {
            //此方法将以Java对象的形式返回给定列的值
            int empno = set.getInt("empno");
            String name = set.getString("ename");
            String job = set.getString("ename");
            System.out.println(empno + " " + name + " " + job);
        }
```
  
 
 
# 四、使用JDBC实现CRUD(增删改查)操作
 
```java
package com.qf.test;
//增删查改
import java.sql.*;
import java.util.*;
public class Demo2 {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {
        //1驱动注册 - 好处，可以不用new，节省空间
        Class.forName("com.mysql.jdbc.Driver");
        //2 获取连接
        //两个参数的写法
        Properties p = new Properties();
        p.setProperty("user", "root");
        p.setProperty("password", "123456");
        //参数url - 形式为 jdbc:subprotocol:subname的数据库网址
        //参数info - 作为连接参数的任意字符串标签/值对的列表; 通常至少应包含“用户”和“密码”属性
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/db2", p);
        //一个参数的方式
        //Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/db2?user=root&password=123456");
        Statement statement = connection.createStatement();
        //增加删除修改
        String sql = "insert into emp(empno,ename,job) values(1000,'bing','qiantai')";
        
        //执行给定的SQL语句，这可能是INSERT ， UPDATE ，或DELETE语句，或者不返回任何内容，如SQL DDL语句的SQL语句。
        //结果（1）SQL数据操作语言（DML）语句的行计数或（2）0不返回的SQL语句
        //num大于0 成功，=0失败
        int num = statement.executeUpdate(sql);
        if (num > 0) {
            System.out.println("成功");
        }
        
        connection.close();
        statement.close();
    }
}
```




# 五、实现一个用户登录的功能
![](https://cdn.nlark.com/yuque/0/2020/png/2145106/1599640869100-87e764f8-3230-4a4b-b8b9-842c87545bb2.png#align=left&display=inline&height=176&margin=%5Bobject%20Object%5D&originHeight=348&originWidth=648&status=done&style=none&width=328)
具体实现
config
```java
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/db2
myuser=root
mypwd=123456
```
 工具类
```java
package com.qf.utils;

import java.sql.*;
import java.util.ResourceBundle;

public class JDBCUtils {
    static String mydriver;
    static String myurl;
    static String myuser;
    static String mypwd;

    static {
        //读取DBConfig文件的内容
        //默认识别的路径是当前的工程
        //使用指定的基本名称，默认语言环境和调用者的类加载器获取资源包
        ResourceBundle resourceBundle = ResourceBundle.getBundle("config");
        mydriver = resourceBundle.getString("driver");
        myurl = resourceBundle.getString("url");
        myuser = resourceBundle.getString("myuser");
        mypwd = resourceBundle.getString("mypwd");

        //1.先注册驱动---Driver
        try {
            Class.forName(mydriver);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    public static Connection getConnection() throws SQLException {
        //2.获取连接---Connection
        return DriverManager.getConnection(myurl, myuser, mypwd);
    }

    public static void closeAll(Connection connection, Statement statement, ResultSet set) {
        //5.关闭资源
        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (statement != null) {
            try {
                statement.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (set != null) {
            try {
                set.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}

```
模型
```java
package com.qf.javabean;

import java.util.Date;

public class User {
	private int id;
	private String name;
	private String password;
	private String sex;
	private Date birthday;
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public User(int id, String name, String password, String sex, Date birthday) {
		super();
		this.id = id;
		this.name = name;
		this.password = password;
		this.sex = sex;
		this.birthday = birthday;
	}
	public User() {
		super();
	}
	@Override
	public String toString() {
		return "User [id=" + id + ", name=" + name + ", password=" + password + ", sex=" + sex + ", birthday="
				+ birthday + "]";
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	public Date getBirthday() {
		return birthday;
	}
	public void setBirthday(Date birthday) {
		this.birthday = birthday;
	}
}

```
ServerDemo
```java
package com.qf.server;
import com.qf.javabean.User;
import com.qf.utils.JDBCUtils;
import java.sql.*;
//模拟的服务器端
/*
 * 同户名:chen
 * 密码:' or 1='1
 * sql语句:select * from user where name='chen' and password='' or 1='1'"
 *
 * 当前的错误称为sql注入.
 */
public class ServerDemo {
    public User findUser(String name, String pwd) {
        Connection connection = null;
        Statement statement =  null;
        ResultSet set = null;
        User user = null;
        try {
            connection = JDBCUtils.getConnection();
            statement = connection.createStatement();
            String sql = "select * from user where name='"+name+"' and password='"+pwd+"'";
            //执行给定的SQL语句，返回单个ResultSet对象。
            set = statement.executeQuery(sql);
            //遍历
            if (set.next()) {
                user = new User();
                user.setId(set.getInt("id"));
                user.setName(set.getString("name"));
                user.setPassword(set.getString("password"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeAll(connection, statement, set);
        }
        return user;
    }
    //解决sql注入问题
    public User findUser1(String name, String pwd) {
        Connection connection = null;
        PreparedStatement ps =  null;
        ResultSet set = null;
        User user = null;
        try {
            connection = JDBCUtils.getConnection();
            String sql="select * from user where name=? and password=?";
            //创建一个PreparedStatement对象，用于将参数化的SQL语句发送到数据库
            ps=connection.prepareStatement(sql);
            //将指定的参数设置为给定的Java String值。 驱动程序将其转换为SQL VARCHAR或LONGVARCHAR值（
            ps.setString(1,name);
            ps.setString(2,pwd);
            set=ps.executeQuery();

            //遍历
            if (set.next()) {
                user = new User();
                user.setId(set.getInt("id"));
                user.setName(set.getString("name"));
                user.setPassword(set.getString("password"));
            }
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } finally {
            JDBCUtils.closeAll(connection, ps, set);
        }
        return user;
    }
}
```
clientDEmo
```java
package com.qf.client;

import com.qf.javabean.User;
import com.qf.server.ServerDemo;

import java.sql.SQLException;
import java.util.Scanner;

public class ClientDemo {
    public static void main(String[] args) throws SQLException {
        //模拟客户端
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入用户名:");
        String name = scanner.nextLine();
        System.out.println("请输入密码:");
        String pwd = scanner.nextLine();

        //进行本地验证---省略(验证格式)
        //进行网络验证(验证的是正确性)


        ServerDemo servletDemo = new ServerDemo();
        User user = servletDemo.findUser(name,pwd);
        if (user != null) {
            System.out.println("恭喜"+user.getName()+"登录成功");
        }else {
            System.out.println("登录失败,请重新登录");
        }

        scanner.close();
    }
}

```
# 六、SQL注入问题：preparedStatement
preparedStatement：预编译对象， 是Statement对象的子类。
特点：

- 性能要高
- 会把sql语句先编译
- sql语句中的参数会发生变化，过滤掉用户输入的关键字。


/
```java
package com.qf.test;
import com.qf.javabean.Emp;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;
import java.util.Properties;

public class Demo1 {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {
        Class.forName("com.mysql.jdbc.Driver");

        Properties p = new Properties();
        p.setProperty("user", "root");
        p.setProperty("password", "123456");
        //url - 形式为 jdbc:subprotocol:subname的数据库网址
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/db2", p);
        String sql="SELECT * FROM emp WHERE ename=?";

        //一.代码的可读性和可维护性.
        //虽然用PreparedStatement来代替Statement会使代码多出几行,但这样的代码无论从可读性还是可维护性上来说.都比直接用Statement的代码高很多档次:
        //每一种数据库都会尽最大努力对预编译语句提供最大的性能优化.
        // 因为预编译语句有可能被重复调用.所以语句在被DB的编译器编译后的执行代码被缓存下来,那么下次调用时只要是相同的预编译语句就不需要编译,只要将参数直接传入编译过的语句执行代码中(相当于一个涵数)就会得到执行
        //PreparedStatement 对象的开销比Statement大，对于一次性操作并不会带来额外的好处。
        //statement每次执行sql语句，相关数据库都要执行sql语句的编译，preparedstatement是预编译得,preparedstatement支持批处理
        PreparedStatement ps=connection.prepareStatement(sql);
        ps.setString(1,"SMITH");
        System.out.println( ps.toString());

        //ps的话此处无参数。
        ResultSet set = ps.executeQuery();

        List<Emp> list = new ArrayList<>();
        while (set.next()) {
            list.add(new Emp(set.getInt("empno"), set.getString("ename"), set.getString("job")));
        }
        System.out.println(list);
    }
}
```


# 七  连接池
我们在讲多线程的时候说过，**创建线程是一个昂贵的操作**，如果有大量的小任务需要执行，并且频繁地创建和销毁线程，实际上会消耗大量的系统资源，往往创建和消耗线程所耗费的时间比执行任务的时间还长，所以，为了提高效率，可以用线程池。
类似的，在执行JDBC的增删改查的操作时，如果每一次操作都来一次打开连接，操作，关闭连接，那么创建和销毁JDBC连接的开销就太大了。为了**避免频繁地创建和销毁JDBC连接，我们可以通过连接池（Connection Pool）复用已经创建好的连接**。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2145106/1599728086323-260bf5c8-fcab-45cd-8ed8-b38720757ea2.png#align=left&display=inline&height=241&margin=%5Bobject%20Object%5D&name=image.png&originHeight=481&originWidth=842&size=113815&status=done&style=none&width=421)
