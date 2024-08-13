---
title: JavaWeb
categories:
  - 开发框架
tags:
  - JavaWeb
toc_number: true
date: 2024-04-18
---

# P1 JSP环境搭建及入门

- 静态/动态
    
    动态网页需要使用到 服务端脚本语言JSP
    
- 架构
    
    CS: Client Server
    
    BS: Broswer Server
    
    客户端可以通过浏览器直接访问服务端
    
- tomcat目录：
    - bin: 可执行文件
    - conf: 配置文件
    - lib: tomcat依赖的jar文件
    - log
    - temp
    - webapps：可执行的项目（将我们开发的项目放入该文件）
    - work：存放由jsp翻译成的java，以及编辑成的class文件 【jsp→java→work】
- 配置tomcat
    - 配置jdk
    - 配置CATALINA_HOME
    
    双击bin/startup.bat启动tomcat
    
    常见错误：与其他服务的端口号冲突
    
    默认端口号8080，建议修改为8888(conf/server.xml)
    
- 访问tomcat
    - localhost:8888
    - 常见状态码：200：一切正常； 404：资源不存在； 403：权限不足； 300/301： 页面重定向； 500：服务器内部错误（代码有误）
- Jsp
    - jsp: 在html中嵌套的java代码
    - 在WEB-INF/web.xml中设置 默认的 初始页面
    
    ```xml
    <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
    ```
    

# P2 虚拟路径和虚拟主机

- 虚拟路径
    - 将web项目配置到webapps以外的目录
    - 方式一： conf/server.xml中配置， host标签中(需要重新启动)
    
    ```xml
    <content docBase="D:\Mydoc\JspProject", path="/JspProject"/>
    ```
    
    - 方式二：在conf/Catalina/localhost中新建“项目名.xml”新增上面的一行代码
- 虚拟主机
    - 通过www.test.com访问本机
    1. conf/server.xml
        
        ```xml
        <Engine name="Catalina" defaultHost="www.test.com">
        		<Host appBase="D:\Mydoc\JsaProject" path="/">
        				<Context docBase="D:\Mydoc\JsaProject" path="/"/>
        		</Host>
        </Engine>
        ```
        
    2. C:\Windows\System32\drives\etc\host 增加 127.0.0.1    www.test.com
    - 流程：
        - [www.test.com](http://www.test.com) → host找映射关系 → server.xml找Engine的defaultHost → 通过“/”映射到"D:\Mydoc\JsaProject"
- JSP执行流程
    - 第一次访问：将jsp翻译成java,再将java编译成class文件 （速度慢）
        - jsp - java(Servelet文件) - class
        - D:\ProgramFiles\apache-tomcat-8.5.99\work\Catalina\localhost\JspProject\org\apache\jsp
    - 第二次访问：直接访问class文件
    - 如果服务端代码修改，再访问时将会重新翻译编译
    - Jsp和Servelet可以相互转换

# P4 使用eclipse开发

- 在eclipse中创建的web项目：
    - 浏览器可以直接访问WebContent/（src/main/wenapp）中的文件
    - 但是WEB-INF中的文件无法通过客户端（浏览器）直接访问，只能通过 请求转发 来访问
- 配置tomcat运行时环境
- 部署 tomcat
    - 在servers面板 新建一个tomcat示例， 再在该实例中 部署项目（右键→Add）， 之后运行
- 统一字符集编码
    - 编码分类：
        - 设置jsp文件的编码（jsp文件中的pageEncoding属性）:  jsp → java
        - 设置浏览器读取jsp文件的编码（jsp文件中meta charset属性）
        - 文本编码：
            - 将整个eclipse中的文件 统一设置 （window→referecr→jsp）*
            - 设置某一个项目
            - 设置某一个文件
- JSP的页面元素:HTML, JAVA代码（scriptlet脚本）， 注释， 指令
    - 脚本Scriptlet
        
        ```jsx
        	<%!
        		全局变量、定义方法
        	}
        	%>
        	<%
        		局部变量、Java代码
        	%>
        	<%=输出表达式 %>
        ```
        
    
    ps: 一般修改web.xml、配置文件、Java需要重启tomcat服务； 但修改Jsp/html/css/js不用
    
    out.println不能回车，回车要写"<br/>”， out.println和输出表达式可以直接解析html代码
    
- page指令

```jsx
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" import="java.util.Date" %>
```

      page指定的属性：

- language：jsp页面使用的脚本语言
- import： 导入类
- pageEncoding： jsp文件自身编码 jsp→java
- contentType：浏览器解析jsp的编码
- 注释
    - html <!—注释—!>
    - java：//     /*注释*/
    - jsp： <%—注释—>
- 九大JSP内置对象（自带的，不需要new也能使用）
    - out: 输出对象，向客户端输出内容
    - request： 请求对象， 存储“客户端向服务端发送的请求信息”
        - 常见方法
            - String getParameter(String name)：根据请求的字段名, 返回字段值value
            - String[] getParameterValues(String name)：根据请求的字段名，返回多个字段值value（checkbox）
            - void setCharacterEncoding(”编码格式utf-8”)： 设置请求编码 （tomcat7以前默认iso-8859-1, 8以后utf-8）
            - getRequestDispatcher(”b.jsp”).forward(requset， response): **请求转发**的方式跳转页面A→B
            - getServerContext(): 获取项目的ServerContext对象
        - 示例：register.jsp, show.jsp
    - response： 响应信息
        - 常见方法：
            - void addCookie(Cookie cookie); 服务端像客户端增加cookie对象
            - void sendRedirect(String location) throws IOException; 页面跳转的一种方式 ，**重定向**
            - void setContentType(String type): 设置服务端响应的编码
        - 示例：login.jsp, check.jsp, success.jsp
    - pageContent
    - session(服务端)
        - Cookie(客户端， 不是内置对象)： Cookie是由服务端生成的，再发给客户端保存。
            - 相当于 本地缓存 的作用
            - 作用: 提高访问服务端的效率， 但是安全性较差
            - key = value
            - javax.servlet.http.Cookie
                - public Cookie(String key, String name);
                - String getName();
                - String getValue();
                - String setMaxAge(int expiry); 设置最大有效期（秒）
            - 服务端准备给客户端：**response**.addCookie(Cookie cookie) → 页面跳转 → 客户端获取cookie： **request**.getCookie**s**();
                - 自动产生一个name为JSESSIONID的cookie
        - session：会话
            - 客户端**第一次**请求服务端时，服务端会产生一个session信息（用于保存该客户的信息）；并且每个session对象都会有一个sessionId(用于区分其他session)；服务端会产生一个cookie，并且该cookie的key=JSEESIONID, value=服务端sessionid的值；然后，服务端在响应客户端的同时，将该cookie发送给客户端，至此，客户端就有了一个cookie（JSESSIONID）。因此客户端的cookie和服务端的session一一对应。
                
                **第二/n次请求**服务端时：服务端会先用客户端cookie中的JSESSIONID去服务端的SESSION中匹配sessionid。
                
            - 总结：
                - session存储在服务器
                - session在同一个用户（客户）请求时共享
                - 实现机制：第一次客户请求时，产生一个sessionid并复制给cookie的jsessionid然后发给客户端。
            - 方法:
                - String getId(): 获取sessionid
                - boolean isNew(): 判断是否是新用户（第一次访问）
                - void invalidate(): 使session失效（退出登录、注销）
                - setAttribute()
                - getSttribute()
                - void setMaxInactiveInterval（秒）：设置最大有效非活动时间
                - void getMaxInactiveInterval()
                - 示例
        - cookie vs session
            
            
            |  | session | cookie |
            | --- | --- | --- |
            | 保存位置 | 服务端 | 客户端 |
            | 安全性 | 较安全 | 不安全 |
            | 保存的内容 | (key=String, value=Object) | (String, String) |
        - 客户端在第一次请求服务端时，如果服务端发现此请求没有JSESSIONID，则会创建一个name=JESESSIONID的cookie，并返回给客户端
    - application 全局对象
        - String getContextPath() 虚拟路径
        - String getRealPath(String ) 虚拟路径相对的绝对路径
    - config 配置对象（服务器配置信息）
    - page 当前jsp页面对象（相当于java中的this）
    - exception 异常对象
- 统一请求的编码 request
    - GET方式：如果出现乱码，解决：
        - 统一每一个变量的编码 new String(旧编码， 新编码)； 例如：name = new String(name.getBytes(“iso-8859-1”), “utf-8”);
        - 修改server.xml一次性的更改tomcat默认get提交方式的编码(utf-8)
        - method=”get”和 地址栏、超链接<a href=”xx”>请求方式 默认都属于get提交方式
    - POST
    - get vs post
        - get在地址栏显示请求信息（但地址栏容量有限4-5kb）； post不显示
        - 文件上传操作，必须是post
        - 推荐使用post
- 请求转发 vs 重定向
    
    
    |  | 请求转发 | 重定向 |
    | --- | --- | --- |
    | 地址栏是否改变 | 不变（check.jsp） | 变(success.jsp) |
    | 是否保留第一次请求时候的数据 | 保留 | 不保留 |
    | 请求的次数 | 1 | 2 |
    | 跳转发生的位置 | 服务端 | 客户端发出的第二次跳转 |
    
    ![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/Untitled.png)
    
    ![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/Untitled%201.png)
    
- 四种范围对象(小→大)
    - pageContext  JSP页面容器   当前页面有效（页面跳转后无效）
    - request         请求对象        同一次请求有效； 其他请求无效（请求转发后有效，重定向后无效）
    - session         会话对象        同一次会话有效（关闭/切换浏览器后无效； 从登录→退出都有效）
    - application   全局对象        全局有效，整个项目运行期间都有效（切换浏览器仍然有效； 关闭服务，其他项目无效）
    
    → 多个项目共享，重启后仍然有效：JNDI
    
    以上四个方法共有的方法：
    
    - Object getAttribute(String name) 根据属性名，或者属性值
    - Object setAttribute(String name, Object obj)
    - Object setAttribute(”a”, “b”)
    - void removeAttribute(String name)
    
    以上的4个范围对象，通过setAttribute赋值，getAttribute取值
    
    以上的4个范围对象，尽量使用范围最小的，对象的范围越大，造成的性能损耗越大
    

# P13 JDBC: Java DataBase Connectivity

![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/Untitled%202.png)

[https://www.notion.so](https://www.notion.so)

- JSBC: Java DataBase Connectivity可以为多种关系型数据库提供统一的访问方式，用Java来操作数据
- JDBC API主要功能。主要是通过以下的类/接口实现：
    - DriveManager: 管理jdbc驱动
    - Connection: 连接
    - Statement(PreparedStatement)：增删改查
    - CallableStatement: 调用数据库中的 存储过程/存储函数
    - Result: 返回的结果集
        
        ![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/Untitled%203.png)
        
- JDBC访问数据库的具体步骤
    - 导入驱动，加载具体的驱动类
    - 与数据库建立连接
    - 发送sql，执行
    - 处理结果集（查询）
- 数据库驱动

|  | 驱动jar | 具体驱动类 | 连接字符串 |
| --- | --- | --- | --- |
| Oracle | ojdbc-x.jar | oracle.jdbc.OracleDriver | jdbc:oracle:thin:@localhost:1521:ORCL |
| MySQL | mysql-connector-java-x.jar | com.mysql.jdbc.Driver | jdbc:mysql://localhost:3306/数据库实例名 |
| SqlServer | sqljdbc-x.jar | come.microsoft.sqlserver.jdbc.SQLServerDriver | jdbc:microdoft:sqlserver:localhost:1433;databasename=数据库实例名 |
- 使用jdbc，如果更换了数据库，只需要替换：驱动，具体驱动类、连接字符串、用户名、密码
- Connection产生操作数据库的对象
    - 产生Statement对象：createStatement();
    - 产生PreparedStatement对象：prepareStatement();
    - 产生CallableStatement对象：prepareCall();
- Statement操作数据库
    - 增删改：excuteUpdate()
    - 查询：excuteQuery()
    
    ```java
    stmt = connection.createStatement();	
    //String sql = "insert into student values(1, 'zs', 23, 's1')";
    //String sql = "insert into student values(2, 'ls', 22, 's2')";
    //String sql = "update student set stuname='ww' where stuno=2";
    String sql = "delete from student where stuno=2";
    int count = stmt.executeUpdate(sql);
    ```
    
- PreparedStatement操作数据库
    - public interface PreparedStatement extends Statement
        - 增删改：excuteUpdate()
        - 查询：excuteQuery()
    - 赋值操作setXxx()
    - 推荐使用，cause：编码简单； 预编译提高性能；安全（防止sql注入）
        - statement sql注入：用户名：任意值 ‘ or 1=1 —  密码：任意值
            
            select count(*) from login where uname=’任意值’ or 1=1’ and upwd=’任意值’
            
    
    ```java
    String sql = "insert into student values(?,?,?,?)";
    pstmt = connection.prepareStatement(sql); //预编译
    pstmt.setInt(1, 333);
    pstmt.setString(2, "hh");
    pstmt.setInt(3, 30);
    pstmt.setString(4, "s4");
    int count = pstmt.executeUpdate();
    ```
    
- Result: 保存结果集
    - next(): 判断是否有下一条数据
    - previous()
    - getXxx(字段名/位置)
- DriveManager: 管理jdbc驱动
- CallableStatement: 调用数据库中的 存储过程/存储函数

---

- jdbc总结（模板、八股文）：

```java
try{
a.导入驱动包、加载具体驱动类Class.forName("具体驱动类");
b.与数据库建立连接connection = DriverManager.getConnection(...);
c.通过connection，获取操作数据库的对象（Statement\preparedStatement\callablestatement）
stmt = connection.createStatement();
d.(查询)处理结果集rs = pstmt.executeQuery()
while(rs.next()){ rs.getXxx(..) ;}
}catch(ClassNotFoundException e  )
{ ...}
catch(SQLException e)
{...
}
catch(Exception e)
{...
}
finally
{
//打开顺序，与关闭顺序相反
if(rs!=null)rs.close()
if(stmt!=null) stmt.close();
if(connection!=null)connection.close();
}
```

     ps：jdbc中，除了Class.forName() 抛出ClassNotFoundException，其余方法全部抛SQLException

- CallableStatement:调用 存储过程、存储函数
connection.prepareCall(参数：存储过程或存储函数名)
参数格式：
    - 存储过程（无返回值return，用out参数替代）：
    { call  存储过程名(参数列表) }
    - 存储函数（有返回值return）：
    { ? = call  存储函数名(参数列表) }
- 存储过程：

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `AddNumbers`(IN a INT, IN b INT, OUT sum INT)
BEGIN
    SET sum = a + b;
END
```

JDBC调用存储过程的步骤：(oracle)
a.产生 调用存储过程的对象（CallableStatement） cstmt = 	connection.prepareCall(   "..." ) ;
b.通过setXxx()处理 输出参数值 cstmt.setInt(1, 30);
c.通过 registerOutParameter(...)处理输出参数类型
d.cstmt.execute()执行
e.接受 输出值（返回值）getXxx()

- 调存储函数：

```sql
CREATE DEFINER=`root`@`localhost` FUNCTION `AddNumbers`(a INT, b INT) RETURNS int
    READS SQL DATA
    DETERMINISTIC
BEGIN
    DECLARE result INT;
    SET result = a + b;
    RETURN result;
END
```

- JDBC调用存储函数：与调存储过程的区别：
   在调用时，注意参数："{? =  call addTwoNumfunction	(?,?) }"

---

- 处理CLOB/BLOB类型
处理稍大型数据：
    1. 存储路径	E:\JDK_API_zh_CN.CHM
    通过JDBC存储文件路径，然后 根据IO操作处理
    例如：JDBC将 E:\JDK_API_zh_CN.CHM 文件 以字符串形式“E:\JDK_API_zh_CN.CHM”存储到数据库中
    获取：1.获取该路径“E:\JDK_API_zh_CN.CHM”  [2.IO](http://2.io/)
    2. CLOB：大文本数据 （小说->数据）
    BLOB：二进制
- clob:大文本数据   字符流 Reader Writer
    - 存：
    1.先通过pstmt 的? 代替小说内容 （占位符）
    2.再通过pstmt.setCharacterStream(2, reader,  (int)file.length());  将上一步的？替换为 小说流， 注意第三个参数需要是Int类型
    - 取：
    1.通过Reader reader = rs.getCharacterStream("NOVEL") ; 将cloc类型的数据  保存到Reader对象中
    2. 将Reader通过Writer输出即可。
- blob:二进制  字节流 InputStream OutputStream
与CLOB步骤基本一致，区别：setBinaryStream(...)  getBinaryStream(...)

# P18 JSP访问数据库

JSP就是在html中嵌套的java代码，因此 java代码可以写在jsp中（<%  ... %>）

- 导包操作：
    - java项目 ：1 Jar复制到工程中 2.右键该Jar :build path ->add to build Path
    - Web项目：jar复制到WEB-INF/lib
- 核心：就是将 java中的JDBC代码，复制到 JSP中的<% ... %>
- 注意：如果jsp出现错误：The import Xxx cannot be resolved， 尝试解决步骤：
a.(可能是Jdk、tomcat版本问题) 右键项目->build path，将其中 报错的 libary或Lib 删除后 重新导入
b.清空各种缓存：右键项目->Clean tomcat... clean  （Project -clean或者 进tomcat目录 删除里面work的子目录）
c.删除之前的tomcat，重新解压缩、配置tomcat，重启计算机
d.如果类之前没有包，则将该类加入包中

---

- JavaBean
将 jsp中 登录操作的代码  转移到了LoginDao.java；其中LoginDao类 就称之为JavaBean
    - JavaBean的作用：
        - 减轻的jsp复杂度
        - 提高代码复用（以后任何地方的 登录操作，都可以通过调用LoginDao实现）
    - JavaBean（就是一个Java类）的定义：满足一下2点 ，就可以称为JavaBean
    a. public 修饰的类  ,public 无参构造
    b. 所有属性(如果有) 都是private，并且提供set/get   (如果boolean 则get 可以替换成is)
    - 使用层面, 分为2大类：
        - a.封装业务逻辑的JavaBean (LoginDao.java封装了登录逻辑)			逻辑
        可以将jsp中的JDBC代码，封装到Login.java类中 （Login.java）
        - b.封装数据的JavaBean   （实体类，Student.java  Person.java  ）		数据
        对应于数据库中的一张表
        Login login = new Login(uname,upwd) ;//即用Login对象 封装了2个数据（用户名 和密码）
    - 封装数据的JavaBean 对应于数据库中的一张表   (Login(name,pwd))
    封装业务逻辑的JavaBean 用于操作 一个封装数据的JavaBean
    - JavaBean可以简化 代码(jsp->jsp+java)、提供代码复用(LoginDao.java)

# P20 MVC设计模式和Servelet执行流程

![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/Untitled%204.png)

- MVC设计模式：
    
    
    | M | Model | 模型：一个功能 | 用JavaBean实现 |
    | --- | --- | --- | --- |
    | V | View | 视图： 用于展示、以及与用户交互 | 使用html  js  css jsp jquery等前端技术实现 |
    | C | Controller | 控制器 ：接受请求，将请求跳转到模型进行处理；模型处理完毕后，再将处理的结果返回给 请求处  | 可以用jsp实现，  但是一般建议使用 Servlet实现控制器 |

Jsp->Java(Servlet)->JSP

- Servlet: Java类必须符合一定的 规范：
a. 必须继承  javax.servlet.http.HttpServlet
b. 重写其中的 doGet()或doPost()方法
- doGet()： 接受 并处 所有get提交方式的请求
doPost()：接受 并处 所有post提交方式的请求
- Servlet要想使用，必须配置
Serlvet2.5：web.xml
Servle3.0： @WebServlet
- Serlvet2.5：web.xml:
    
    项目的根目录：WebContent 、src
    
    <a href="WelcomeServlet">所在的jsp是在 WebContent目录中，因此 发出的请求WelcomeServlet  是去请求项目的根目录。
    
    - Servlet流程：
    请求 -><url-pattern> -> 根据<servlet-mapping>中的<servlet-name> 去匹配  <servlet> 中的<servlet-name>，然后寻找到<servlet-class>，求中将请求交由该<servlet-class>执行。

2个/:
jsp:/  localhost:8888
web.xml: /   [http://localhost:8888/项目名/](http://localhost:8888/%E9%A1%B9%E7%9B%AE%E5%90%8D/)

# P21 Servelet25和Servelet30的使用

- 回顾 纯手工方法 创建第一个Servlet，步骤：
编写一个类，继承HttpServlet
重写doGet()、doPost()方法
编写web.xml 中的servlet映射关系
- 借助于Eclipse快速生成Servlet
直接新建Servlet即可！（继承、重写、web.xml  可以借助Eclipse自动生成）
- Servlet3.0，与Servlet2.5的区别：
Servlet3.0不需要在web.xml中配置，但 需要在 Servlet类的定义处之上编写 注解@WebServlet("url-pattern的值")
    - 匹配流程：  请求地址 与@WebServlet中的值 进行匹配，如果匹配成功 ，则说明 请求的就是该注解所对应的类
- 项目根目录：WebContent、src（即 所有的构建路径）
    - 例如：
    WebContent中有一个文件index.jsp
    src中有一个Servlet.java
    - 如果: index.jsp中请求 <a href="abc">...</a> ，则 寻找范围：既会在src根目录中找  也会在WebContent根目录中找
    - 如果：index.jsp中请求<a href="a/abc"></a>，寻找范围：先在src或WebContent中找a目录，然后再在a目录中找abc
- /
    - web.xml中的 /:  代表项目根路径，例如：[http://localhost:8888/Servlet25Project/](http://localhost:8888/Servlet25Project/)
    - jsp中的/: 服务器根路径，[http://localhost:8888/](http://localhost:8888/)（/a = [http://localhost:8888/](http://localhost:8888/)a; a= [http://localhost:8888/](http://localhost:8888/)[Servlet25Project/](http://localhost:8888/Servlet25Project/)webcontent/a）
    - 构建路径、WebContent都属于根目录

- Servlet生命周期：**5**个阶段
加载
初始化： init()  ，该方法会在 Servlet被加载并实例化的以后 执行
服务  ：service() ->doGet()  doPost
销毁  ：destroy()，  Servlet被系统回收时执行
卸载

![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/Untitled%205.png)

- init():
a. 默认第一次访问 Servlet时会被执行 （只执行这一次）
b. 可以修改为 Tomcat启动时自动执行
    - Servlet2.5：  web.xml
    
    ```xml
    <servlet>
    ...
    <load-on-startup>1</load-on-startup>
    </servlet>
    其中的“1”代表第一个。
    ```
    
    - Servlet3.0
    
    ```java
    @WebServlet(value="/WelcomeServlet", loadOnStartup=1  )
    ```
    
- service() ->doGet()  doPost ：调用几次，则执行几次
- destroy()：关闭tomcat服务时，执行一次。

# P22 Servlet API详解与源码分析

- Servlet API：由两个软件包组成： 对应于HTTP协议的软件包、对应于除了HTTP协议以外的其他软件包 （即Servlet  API可以适用于 任何 通信协议）
我们学习的Servlet,是位于javax.servlet.http包中的类和接口，是基础HTTP协议。
- Servlet继承关系
    
    ![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/Untitled%206.png)
    
- ServletConfig:接口
    - ServletContext getServletContext():获取Servlet上下文对象   application
    - String  getInitParameter(String name):在**当前Servlet**范围内，获取名为name的参数值（初始化参数）
- ServletContext中的常见方法(application)：
    - getContextPath():相对路径
    - getRealPath()：绝对路径
    setAttribute() 、getAttribute()
    - String getInitParameter(String name);在**当前Web容器**范围内，获取名为name的参数值（初始化参数）

Servlet3.0方式 给当前Servlet设置初始值：
@WebServlet( .... , initParams= {@WebInitParam(name="serveltparaname30",value="servletparavalue30")   }   )
注意，此注解只 隶属于某一个具体的Servlet ，因此无法为 整个web容器设置初始化参数 （如果要通过3.0方式设置 web容器的初始化参数，仍然需要在web.xml中设置）

![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/Untitled%207.png)

- HttpServletRequest中的方法：(同request)，例如setAttrite()、getCookies()、getMethod()
HttpServletResponse中的方法：同response
- Servlet使用层面：
    
    Eclipse中在src创建一个Servlet，然后重写doGet()  doPost()就可以  （doGet() doPost()只需要编写一个）。
    

# P23 MVC案例

![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/Untitled%208.png)

# P24 三层架构

与MVC设计模式的目标一致：都是为了解耦合、提高代码复用；

区别：两者对项目理解的角度不同

- 三层组成
    - 表示层（USL, User Show layer; 视图层）
        - 前台：对应MVC中的V， 用于和用户交互、界面显示
            - jsp, js, html, css, jquery等前端web技术
            - 代码位置：webcontent
        - 后台：对应MVC中的C，用于 控制跳转、调用
            - Servlet(Spring MVC Strusts2)
            - 代码位置：xxx.servlet包
    - 业务逻辑层（BLL，Business Logic Layer; Service层）
        - 接收表示层的请求 调用
        - 组装数据访问层，逻辑性操作（增删改查， 删：查+删）
        - 位于xxx.service包
    - 数据访问层（DAL，Data Access Layer; Dao层）
        - 直接访问数据库的操作，原子性操作（增删改查）
        - 位于xxx.dao包
- 三层的关系：
    - 上层将请求传递给下层，下层处理后返回给上层
    - 上层依赖于下层

```
	/**
	 * jsp常见的内置对象都可以在servlet里面拿到
	 * out: PrintWriter writer = response.getWriter()
	 * session: request.getSession()
	 * application: request.getServletContext()
	 */
```

- 示例

![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/Untitled%209.png)

- 一个Servlet对应一个功能

# P29 优化三层（接口和DBUtil）

- 加入接口
建议面向接口开发：先接口-再实现类
--service、dao加入接口
--接口与实现类的命名规范
接口：interface，	起名   I实体类Service		IStudentService
IStudentDao	
实现类：implements	起名   实体类ServiceImpl		StudentServiceImpl
StudentDaoImpl
接口：	I实体类层所在包名	IStudentService、IStudentDao	
接口所在的包：  xxx.service		xx.dao

```
	实现类：	 实体类层所在包名Impl	StudentServiceImpl、StudentDaoImpl
		实现类所在的包：xxx.service.impl		xx.dao.impl

以后使用接口/实现类时，推荐写法：
接口 x = new 实现类();
IStudentDao studentDao = new StudentDaoImpl();

```

- DBUtil 通用的数据库帮助类，可以简化Dao层的代码量
    
    帮助类 一般建议写在  xxx.util包
    
- 方法重构：  将多个方法 的共同代码 提炼出来，单独写在一个方法中，然后引入该方法即可

# P30 Web调试：

与java代码的调试 区别：启动方式不同

index.jsp ->index_jsp.java ->index_jsp.class

jsp->java->class
jsp翻译成的Java 以及编译后的class文件 存在于tomcat中的work目录中

10000

# P21 分页SQL

- 分页
要实现分页，必须知道  某一页的 数据 从哪里开始 到哪里结束
- 页面大小：每页显示的数据量

假设每页显示10条数据

- mysql分页：
    - mysql:从0开始计数
    0		0		9
    1		10		19
    2		20		29
    n		n*10	      (n+1)*10-1
        
        结论：
        分页：
        第n页的数据：  第(n-1)*10+1条  -- 第n*10条
        
    - MYSQL实现分页的sql：
    limit  开始,多少条
    第0页
    select * from student limit 0,10 ;
    第1页
    select * from student limit 10,10 ;
    第2页
    select * from student limit  20,10 ;
    第n页
    select * from student limit n*10,10
    - mysql的分页语句：
    
    ```sql
    select * from student limit 页数*页面大小,页面大小
    ```
    
- oracle分页：
    
    
- SQLServer分页：  3种分页sql

dao和DBUtil的区别：
dao 是处理特定 类的 数据库操作类：
DBUtil是通用  数据库操作类

# P34 文件上传

- 上传文件
    - 引入2个jar
    apache: commons-fileupload.jar组件
    commons-fileupload.jar依赖 commons-io.jar
    - 代码：
        - 前台jsp：
        <input type="file"  name="spicture"/>
        表单提交方式必须为post
        在表单中必须增加一个属性 entype="multipart/form-data"
        - 后台servlet：
    - 注意的问题：
    上传的目录  upload ：
    1. 如果修改代码，则在tomcat重新启动时 会被删除
    原因：当修改代码的时候,tomcat会重新编译一份class 并且重新部署（重新创建各种目录）
    
          2.如果不修改代码，则不会删除
    	原因： 没有修改代码，class仍然是之前的class
    
    因此，为了防止 上传目录丢失： a.虚拟路径	b.直接更换上传目录 到非tomcat目录
    
- 限制上传：
类型、
大小
注意 对文件的限制条件 写再parseRequest之前
- 下载：不需要依赖任何jar	
a.请求（地址a  form），请求Servlet	
b.Servlet通过文件的地址  将文件转为输入流 读到Servlet中
c.通过输出流 将 刚才已经转为输入流的文件  输出给用户
注意：下载文件 需要设置2个 响应头：
response.addHeader("content-Type","application/octet-stream" );//MIME类型:二进制文件（任意文件）
response.addHeader("content-Disposition","attachement;filename="+fileName );//fileName包含了文件后缀：abc.txt
- 下载时 ，文件名乱码问题：
    - edge：
        
        URLEncoder.encode(fileName,"UTF-8")
        
    - firefox：
    给文件名 加：
    前缀   =?UTF-8?B?
        
        String构造方法
        Base64.encode
        
        后缀   ?=
        示例：
        response.addHeader("content-Disposition","attachment;filename==?UTF-8?B?"+   new String(  Base64.encodeBase64(fileName.getBytes("UTF-8"))  ) +"?=" );//fileName包含了文件后缀：abc.txt
        

# P38 EL语法表达式，点操作符和中括号操作符

EL ：为了消除jsp中的Java代码

- 语法：
${EL表达式}
a.EL不需要导包
b.在el中调用属性，其实是调用的getXxx()方法
- ${范围.对象.属性.属性的属性 }
- 操作符：操作：属性，不是对象
. : 使用方便
[] : 如果是常量属性，需要使用双引号/单引号 引起来;比点操作符更加强大
- []强大之处:
    - 可以容纳一些 特殊符号 （.  ?   -）
    - []可以容纳 变量属性 （可以动态赋值）
    String x = "a";
    ${requestScope.a}等价于${requestScope["a"]}等价于${${requestScope[x]}
    - 可以处理数组
    ${requestScope.arr[0] }
    - 普通对象、map中的变量
- 关系运算符，逻辑运算符
- empty运算符：判断一个值null/不存在→true
- EL表达式的隐式对象（不需要new就能使用的对象，自带的对象）
    - 作用域访问的对象（EL域对象）: pageScope,  requestScope, sessionScope, applicationScope
        - 如果不指定域对象，则默认 会根据从小到大的顺序 依此取值
    - 参数访问对象： 获取表单数据 （request.getParameter()/超链接中传的值/地址栏中的值→${param}, request.getParameterValues()→${paramValues}）
    - JSP隐式对象：pageContext
        - 在jsp中可以通过pageContext获取jsp的其他隐式对象；因此如果要在el中使用jsp隐式对象，可以通过pageContext获取
        - 例如：${pageContext.request}; pageContext.getSession→${pageContext.session}hia
        - 还可以获取级联对象：${pageContext.request.serverPort}

# P40 JSTL基础及set, out, remove

- JSTL：比EL更加强大
    - 需要引入2个jar ：jstl.jar   standard.jar
    - 引入tablib：<%@ taglib uri="[http://java.sun.com/jsp/jstl/core](http://java.sun.com/jsp/jstl/core)" prefix="c" %>   (其中prefix="c" :前缀)
    - 核心标签库：  通用标签库、条件标签库 、迭代标签库
- 通用标签库
<c:set>赋值，作用：
    - 在某个作用域之中（4个范围对象），给**某个变量赋值**
        
        ```java
        <c:set var="变量名"    value="变量值"   scope="4个范围对象的作用域"/>
        例如：
        <c:set var="name"    value="zhangsan"   scope="request"/>
        等价于：
        request.setAttribute("name", "zhangsan") ;
        ```
        
    - 给**某个对象的属性**赋值: 在某个作用域之中（4个范围对象），给**某个对象的属性复制** （此种写法，不能指定scope属性）
        - 给普通对象赋值:
        
        ```java
        <c:set target="对象" property="对象的属性"  value="赋值" />
        <c:set target="${requestScope.student}" property="sname"  value="zxs" />
        ```
        
        - 给map对象赋值:
        
        ```java
        <c:set target="对象" property="对象的属性"  value="赋值" />
        <c:set target="${requestScope.countries}" property="cn"  value="中国" />    
        ```
        
    - 注意 <c:set>可以给**不存在的变量**赋值 （但不能给不存在的对象赋值）
- <c:out>  ：显示
    
    ```java
    true:<c:out value='<a href="[https://www.baidu.com](https://www.baidu.com/)">百度</a>' default="当value为空的，显示的默认值" escapeXml="true" />
    false：	<c:out value='<a href="[https://www.baidu.com](https://www.baidu.com/)">百度</a>' escapeXml="false" />
    ```
    
- <c:remove >：删除属性

```java
<c:remove var="a" scope="request"/>
```

- 选择：
    - if(boolean): 单重选择
    <c:if test="" >
    - if else if... esle if... else  /switch
        
        ```java
        <c:choose>
        	<c:when test="...">   </c:when>
        	<c:when test="...">   </c:when>
        	<c:when test="...">   </c:when>
        	<c:otherwise>   </c:otherwise>
        </c:choose>
        ```
        
    - 在使用 test="" 一定要注意后面是否有空格
    例如：test="${10>2 }"   true
    test="${10>2 } "  非true
- 循环（迭代标签库）

```java
	for(int i=0;i<=5;i++)
	<c:forEach  begin="0" end="5" step="1" varStatus="status">
		${status.index} 
		test...<br>
	</c:forEach>
```

```java
for(String str:names)
<c:forEach  var="student" items="${requestScope.students }" >
${student.sname }-${student.sno }
</c:forEach>
```

# P42 过滤器

![Untitled](JavaWeb%20a0049edb9a814733869816542ecba636/d1517cf2-51f7-48e8-bad8-825bb781ae57.png)

- 过滤器：
实现一个Filter接口
init()、destroy() 原理、执行时机 同Servlet
配置过滤器，类似servlet
通过doFilter()处理拦截，并且通过chain.doFilter(request, response);放行
- filter映射
    - 只拦截 访问MyServlet的请求
    <url-pattern>/MyServlet</url-pattern>
    - 拦截一切请求（每一次访问 都会被拦截）
    <url-pattern>/*</url-pattern>
- 通配符
    - dispatcher请求方式：
        - REQUEST：拦截HTTP请求 get post
        - FORWARD：只拦截 通过 请求转发方式 的请求
        - INCLUDE: 只拦截通过 request.getRequestDispatcher("").include()  、通过<jsp:include page="..." />此种方式发出的请求
        - ERROR：只拦截<error-page>发出的请求
- 过滤器中doFilter方法参数：ServletRequest
在Servlet中的方法参数：HttpServletRequest
- 过滤器链
可以配置多个过滤器，过滤器的先后顺序 是由 <filter-mapping>的位置 决定

# P43 监听器

- 监听器
    - 步骤：
        - 编写监听器，实现接口
        - 配置web.xml
    - 监听对象：request session application
        - request：ServerRequestListener
        - session: HttpSessionListener
        - application: ServletContextListener;
        - 每个监听器各自两个方法，监听开始和结束
        - ServletContext在servlet容器启动时自动创建
    - 监听属性的变更
        - request：ServerRequestAttributeListener
        - session: HttpSessionAttributeListener
        - application: ServletContextAttributeListener;
    

# P44 session绑定解绑、钝化活化

- 钝化：内存→硬盘
- 活化：硬盘→内存
- session对象的四种状态
    - session.setAttribute(”a”, xxx) 将对象**绑定**到session中
    - session.removeAttribute(”a”) 将对象从session中**解绑**
    - 钝化
    - 活化
- 监听：
    - 监听session对象的绑定和解绑：HttpSessionBindingListener  不需要配置web.xml
    - 监听session对象的钝化活化：HttpSessionActivationListener  不需要配置web.xml
- 如何钝化、活化：配置tomcat安装目录/conf/context.xml
- 钝化、活化的本质 就是序列化、反序列化，需要实现Serializable接口
- 总结：钝化、活化 实际执行 是通过context.xml中进行配置而进行；
    
    HttpSessionActivationListener只是负责在 session钝化 和 活化 时予以监听。
    

# P45 Ajax原理及js方式实现

- Ajax： 异步js 和 xml
    - 异步刷新可以使得：只刷新需要修改的地方，页面中其他部分保持不变
        
        例如:视频点赞
        
    - 实现：
        - js： XMLHttpRequest对象
            - XMLHttpRequest对象的方法
                - open(方法名（提交方式get/post）, 服务器地址, true)：(与服务段端建立链接)
                - send():
                    - get: send(null)
                    - post: send(参数值)
                - setRequestHeader(header, value)
                    - get不需要设置此值
                    - post
                        - 如果请求元素中包含了文件上传：setRequestHeader(”Content-Tyoe”, “multipart/form-data”)
                        - 不包含：setRequestHeader(”Content-Tyoe”, “application/x-www-form-urlencoded”)
            - XMLHttpRequest对象的属性
                - readyState:请求状态。 4：请求完毕
                - state:响应状态。 200：响应正常
                - onreadystatechange: 回调函数
                - responseText: 响应格式为text
                - responseXML
        - jquery（推荐）
            
            ```jsx
            $.ajax({
            url: 服务器地址，
            type：get | post,
            data: 请求数据，
            success: function(result, testStatus) {},
            error: function(xhr, errorMessage, e) {},
            })
            ```
            
            ```jsx
            $.get(
            服务器地址,
            请求数据,
            function(result, testStatus) {
            },
            预期返回值类型（String\xml),
            );
            $.post(
            服务器地址,
            请求数据,
            function(result, testStatus) {
            },
            预期返回值类型（String\xml),
            );
            ```
            
            ```jsx
            $(xxx).load(
            服务器地址,
            请求数据,
            );
            ```
            
            load: 将服务端的返回值，直接加载到$(xxx)所选择的元素
            
            ```jsx
            $.getJSON(
            服务器地址,
            json格式的请求数据,
            function(result) {
            },
            );
            ```