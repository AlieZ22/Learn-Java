# Java小学期笔记

## Java vs C++

##### 1, 数据类型

​	*基本数据类型：byte(8bits)   short(16bits)   int(43bits)   long(64bits)   float(32bits)   double(64bits)  char(16bits)   boolean

​	char string 用unicode编码格式(双字节编码) -可表示字母 汉字 日文等  

```java
char c = '\u0065';
// 禁止 大数据类型给小数据类型赋值-syntax error
float f = 3.14F;   // (数值类型默认double)
int i = 011;    // 八进制
```

​	*引用数据类型： Object （every thing is Object ）

##### 2, 参数传递

​	只有传值一种方式 

##### 3, 对象存储与访问

​	除非用new创建对象，否则，实际上并未获得任何对象

​	对象全部存储在堆中，栈中没有对象，  访问对象的唯一方法是 ‘引用’（引用的大小：8byte）

##### 4, 对象回收

​	没有引用的对象 是垃圾对象，JVM的垃圾回收机制将自动回收，但不知道何时进行。（System.gc()仅是发一个回收请求，并不立刻进行）

## Java中的运算

##### 1,  算术运算

+；  -； *； /； %； ++； --；>> (右移，保证符号位不变);  **>>>** (无符号右移，最高位只补零。注意：负数移位会变成正数);  & (与);  | (或)；^ (异或); ~ (按位取反);

```java
int i = -1;
i = i >> 99;
// -1的补码是全1，右移保证符号，故i移位后仍为-1
int j = 17;
j = j << 33;    // int只占32位，编译器会自动给33取32的模，于是等价于 j<<1
/*若是byte,short,char类型（是int型兼容的）进行算术运算，会自动进行类型提升，至int*/
// 故 byte，short，char的移位运算，移的位数仍然模32，而不是8，16
short s = 5;
s = (short) (s + (byte)9f);
int i = 1000;
byte b = (byte)i;    // 输出-24  （截断后最高为为1，则为负数）
//两个byte相与，类型为int，还需转换类型
```

特别地，对于 = (赋值运算)，基本类型的赋值运算就是普通的内容复制；而对象的赋值实际上是将“引用”从一个地方复制到另一个地方，操纵的是“引用”

##### 2,  关系运算

<;  >;  <=;  >=;  ==;  !=;  instanceof; 

instanceof用来在运行时指出对象是否是特定类(自己定义的)的一个实例。通过返回一个布尔值来指出，这个对象是否是这个*特定类或者是它的子类*的一个实例。（任何 对象 instanceof Object 结果为true，除了null）

```java
Unit u = new Unit();
if(u instanceof Object) {
System.out.println("True");
}else {
System.out.println("False");
}
// 输出 True
```

##### 3,  逻辑运算

&&（逻辑与）;  ||（短路）;  & (与);  | (或)；^ (异或);

(思考：位与运算&和逻辑与&&的区别， 位或运算|和短路运算||的区别)

```java
int i = 1;
false & i++ > 10
false && i++ > 10
//两者的区别是 最终的i值不同
//常见的安全代码-利用||处理空指针异常
String username;
if(username == null || username.length()==0){//发出警告}
```

*桶排序   进制问题

## static关键字

当声明一个事物是static时，就意味着这个域或方法不会与包含它的那个类的任何对象实例关联在一起。

具体体现是：

- 不用创建类就可以直接通过"类名. "来访问
- static的域是共享的，而与包含它的对象的实例个数无关（static字段对每个类来说只有一份存储空间，而非static字段则是对每个对象有一个存储空间）
- static方法不能直接调用其他非static的域或方法

## 包

用于进行类的组织，处理命名冲突。**包的命名须全小写（规范**），不能以系统包名开头。

如果有包名，那么javac编译必须要加 -d 表示建立 package a.b; 中的 a.b 对应的文件夹 a\b
. (点) 表示 在当前路径下

java命令中给出 -classpath 选项，将在指定目录下去找class文件运行

jar 命令用于归档打包

*mainfest文件， 清单

## 继承，多态，接口

java中的继承是单一继承，若不显示指示则继承于Object

super 特指继承下来的父类（可多级），super() 仅能用于子类构造函数中，而super. 方法可以在子类其他函数中使用

```Java
class A {
	int j;
	A() { this.init(); }    // 2，此时调用的不是A的init()函数，而是B重写的init()
	public void init() {    
	    System.out.println("q");
		j = 0;
	}
}
class B extends A {
   int j;
   B(int j) { this.j = j; }   // 1，子类构造函数在基类构造之后，其实j赋值之前 默认有super()
   public void init() {     // 3，此时调用该函数时，j并没有被赋值，默认初值为0
	   System.out.println("p");
	   System.out.println(j);
	   j = 4;
   }
   public static void main(String[] a) {
      System.out.println(new B(5).j);
   }
}
// 最终的输出是 p 0 5
```

多态分为静态多态和动态多态。静态多态也叫重载，即同名函数，参数不同；Override 重写，在继承过程中，子类重写覆盖父类中的函数。   加final 或static 明确地在编译的时候确定，该函数在继承过程中将不进行重写，可提升性能。

函数覆盖的三个原则：

​	覆盖函数的访问权限不能比被覆盖的函数低

​	覆盖函数抛出的检查异常不能更多（不能抛出未声明的检查异常，运行异常如NullPointerException可以）

​	static，final函数不能被覆盖

## 集合

java.util包下

集合最先只是定义了接口，Collections是大多数集合的公共基类

- List：*回去看文档    *Object 的hasCode
- Set：
- Map：

## 泛型



## 文件操作

*为了保证灵活性和可扩展性，尽量少用常量（如文件名，且不用绝对路径）。

File f = new File("a.dat");   并不是创建了一个文件，而是在内存中创建了一个File对象

RandomAccessFile可进行读写，seek

流对象： 输入输出流

```java
// 流连接
FileInputStream fis = new FileInputStream(args[0]);
BufferInputStream bis = new BufferInputStream(fis);
DataInputStream dis = new DataInputStream(bis);

double d = dis.readDouble();
```

windows 是小端，而mac 大端（不同操作系统，大小端可能也不同）

*ObjectInputStream

java.io.Serializable 序列化接口，没有抽象方法（即标签接口），只是声明一下该对象是可序列化的，jvm可以在流中输出该对象

对象的.clone()是浅复制，若要自己实现深复制则可以利用文件（ObjectOutputStream）的序列化和反序列化来实现

文本文件 .dat   .txt   .docx   .pdf 等。 汉字编码2字节， 编码格式GB2312---GBK---GB18030

Unicode字符编码，2字节，java是统一编码Unicode;    utf-8 是可变长编码  基本的Unicode占1字节，中文3字节

FileReader fr = new FileReader("a.txt");   这个类使用的编码是与平台有关的（平台默认的编码）

```java
// 通常的方法
FileInputStream fis = new FileInputStream("a.txt");
BufferedReader br = new BufferedReader(new InputSreamReader(fis));
```

write时, \r\n是是平台有关的，可以使用PrintWriter的println函数跨平台地去处理换行

Properties 实现配置文件的读写

## 异常处理

Exception, Error 继承于Throwable, Error是致命的错误，机器直接停止运行/宕机，不说去处理它而出现异常，需要去处理。

对RuntimeException 编译器不做任何语法检查：

- NullPointerException（空指针异常）
- ArithmeticException（算术异常）
- ClassCastException（类转型异常）
- IndexOutOfBoundsException（下标越界异常）
- ...

而对CheckException 编译器会做语法检查。

## 多线程编程

必要的时候，经常进行一下Thread.sleep(), 睡眠时间可能会长于规定的时间，因为sleep()后线程重新向处理器请求调度

创建线程的两种方法：

1. 实现Runnable接口传入Thread构造函数
2. 继承Thread类实现run()方法

多线程的同步：synchronized   每一个对象都有一个对象锁

```java
Objecct lock1 = new int[0];
synchronized（Obejct o）{
	...
}
```

wait() 封装在Obejct中，调用该函数的对象必须持有该对象的对象锁

notify() 与 notifyAll() : 唤醒当前对象锁处于wait()状态的线程    * 进入wait()的条件(阻塞条件)应该用while

notifyAll() 唤醒所有处于阻塞状态的线程，但进入阻塞是用的while，可以带来更好的安全性

*对象池    Object 的wait() , notify() , notifyAll()

ThreadLocal类，内部用Map来存储不同的线程变量，key: 线程  value: 变量。 可以用来隐含地参数传递。

+

组合与聚合：与主体生命周期一致的称为组合，组合是更强烈的聚合

*派生属性占用内存，而且致命的是派生属性的同步问题！！

## 网络编程

Socket  以流的方式进行数据传输    （json数据格式）

服务器端，ServerSocket 指定监听的端口，  .accept() 接收客户端请求，实施监听进行阻塞

（可先发一个整数，在服务器端进行功能选择） 传送文件之前， 先把文件名， 文件长度发给对方

readFully() 缓冲区数据有多长就读多少

&小技巧：“upload”.equals(command)   有效防止了空指针异常

*工厂模式

服务器支持多并发：while 中不停地接收请求创建Socket， 创建的每一个Socket给一个线程（提前创建线程池）去处理。线程由线程池中取出，提高效率。     线程中run() 第一条应该是wait() ,	收到请求后处理相应的逻辑



HashMap 是线程不安全的， HashTable 是线程安全的。  可用下面方法将集合转换成线程安全的

Map map =  Collections.synchronizedMap(map); 

TreeSet, TreeMap 中若有自定义的对象，则对象要实现Comparable接口，以进行比较

## 事件处理机制

event (接口),  event source (事件源)   

ActionEvent,  MouseEvent,  KeyEvent,   FocusEvent

event都有一个函数getSource()方法

*java 命令行参数  -D可以设置系统属性  ，如用FileReader来读文件是与平台有关的，若想在日文系统中读中文文本，则可以 java -Dfile.encoding=gbk 类 来运行 

*装饰者模式

## 注解

常用的注解：@Override    @Deprecated      @SuppressWarnings  （用于压制程序中的警告，有{“unchecked", "Deprecated"}等）

使用@interface Annotation{  } 

一个@Annotation注解，一个注解是一个类

动态地根据类名创建实例：（类名）Beans.instantiate(ClassLoad cl, String className)

参考：https://blog.csdn.net/liuwenbo0920/article/details/7290586

import static 类  ：表示导入该类所有的静态方法(原来Math.abs()，现在只用abs() ) 

*java.lang.reflect

*TLV (TAG-LENGTH-VALUE)  变长数据传输的一种报文格式

## JDBC

创建完Connection之后，java会自动进行事务提交(con.commit())，若要自己提交事务则要在此处设置 con.setAutoCommit(false);

分布式事务（例子：跨行取钱），使用2PC来支持分布式事务

o/r mapping  对象关系 映射

*软件设计的一些原则： 开闭原则，依赖倒置原则，里氏替换原则，接口隔离原则



















# J2EE笔记

tel: 18640857288    qq: 1458233700  email: jgh@dlut.edu.cn

## 介绍

j2ee是java se 的扩展，包名均以javax开头。java SE（桌面应用）, java EE（企业应用）, java ME （嵌入式应用）

javaEE平台很多都是接口，是一个抽象的平台称为“定义平台”。

**基于组件的开发**，组件（如servlet）在容器中被调用。应用服务器软件：开源免费:  Apache-tomcat,  收费：IBM-WebSphere Application Server （WAS ）； Bea WebLogic 

没有main函数，需要将项目部署(deploy)到Web服务器。打包成.war文件。

*URL的格式（三个部分）：**协议+主机(IP+端口)+文件路径** (例如： https://127.0.0.1:8080/SearchPage/index.html?username=zzmine&password=123456)。 url地址中？后面的内容叫作 查询字符串(Query String)

request和response的组成：headers和content，用两次回车换行分隔(\r\n)

## Servlet

组件不能自己写构造函数，应用服务器会使用默认构造函数！！若要构造，应该**重写**其init(ServletConfig config)函数。HttpServlet已经实现了init()方法。

```java
//两种init方法
//1,带参数
public void init(ServletConfig config){
	super.init(config);
	...
}
//2,不带参数
public void init(){
    ...
}
```

Servlet的service()方法，参数，返回类型以及抛出什么异常。

```java
public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException{
    HttpServletRequest req = (HttpServletRequest)request;
    HttpServletResponse res = (HttpServletResponse)response;
	// HttpServletReuqest的getMethod方法可以获得用户请求的方式(GET,POST等)
    String method = req.getMethod();
    if("GET".equals(method)){
        ...
    }else if("POST".equals(method)){
        ...
    }
    
    res.setContentType("text/html;charset=utf-8");
    PrintWriter out = res.getWriter();
    out.print("<p>Hello World</p>");
    // 不建议写流的关闭，还有其他程序需要输出
    // 输出形式为jpeg图片
    res.setContentType("image/jpeg;charset=utf-8");
    OutputStream out = res.getOutputStream();
    out.write(...);
}
```

部署(deploy)的方法:

1, 将web应用(HelloServlet)直接复制在Tomcat的WebApps文件夹下, 则用户访问路径：http://localhost:8080/HelloServlet/abc/h.html 

2, 在Tomcat配置中的server.xml下部署：在Host元素下建子标签<content path="/abc" docBase="..."> 。path是url，docBase是文件的物理地址。则用户访问路径: http://localhost:8080/abc/abc/h.html

*javax.websocket包下，Endpoint, Encoder, Decoder。双向发送数据，而Servlet是请求应答式单向的服务。

（必考）ServletConfig, ServletContext

## Session

*HttpSession Api

可以设置session的生存/超时时间，但注意单位。tiemout通常单位是分钟。

session在内存中如何被创建？

![1568274579253](C:\Users\MAIBENBEN\AppData\Roaming\Typora\typora-user-images\1568274579253.png)

*memcached分布式缓存

HttpSession底层实现的三种方法（https://blog.csdn.net/qfs_v/article/details/2652119）：

1. *cookie技术
2. URL重写(rewrite) (浏览器不支持cookie时，可以用URL重写来代替)
3. 隐藏表单域 （form表单有一个隐藏的input）

## RequestDispatcher

用于请求转发，页面重定向。

```java
//Forwards a request from a servlet to another resource (servlet, JSP file, or HTML file) on the server.
public void forward(ServletRequest request, ServletResponse response)
//Include： 将当前请求派发到另一个组件，处理完毕后，回到该组件 
public void include(ServletRequest request, ServletResponse response)
```

若需要传送数据到另一个组件，不要在response的输入输出流中进行，可以利用request.setAttribute()方法，在request域中存放数据。

注意：路径中'/'表示应用的根目录。request的请求转发是应用组件内的转发。而ServeltContext的转发方法可以在用一个tomcat进程中在不同web应用进行转发。

```java
@WebServlet("/Servlet/a");
public class AServlet extends HttpServlet throws...{
	public void service(){
         response.setContentType("text/html;charset=utf-8");
	    // ！response.getWriter().print("...1");
        // text/html  MIME类型
    	
        request.setAttribute("obj",obj);
  		request.getRequestDispathcer("/Servlet/b").forward(request,response);
        // ！response.getWriter().print("88");
        // 若是forward方法，则不能在response的输出流中输出（该组件不能处理response流），应该将要输出的对象先存在reqeust域中，再传给另一个组件。
    }
}
```

(必考) response的sendRedirect与request的转发的区别：

```
request的是由服务端进行转发的，浏览器地址栏不变
response的是由客户端进行重定向，浏览器地址栏会改变，相当于发多次请求，效率较低
```

若web应用直接放在root目录下，则request.getContextPath()返回为空串。



## Filter

(必考) Filter，其服务函数doFilter()，在Servlet运行之前执行

```Java
public void doFilter throws ServletException(ServletRequest r1, ServletResponse r2, FilterChain chain){
	
    long start = System.currentTimeMillis();
    HttpServletRequest request = (HttpServletRequest)r1;
    HttpServletResponse response = (HttpServletResponse)r2;
    // 预处理
    
    // 过滤后，交给Servlet,最后进行后处理。若没有chain.doFilter()，那么该Filter就会产生拦截，不会访问到后面的Servlet
    chain.doFilter(requeset,response);   // 往后传
    
    //后处理
    long end = System.currentTimeMillis();
}
```

按提供的服务分类有：

1. Authentication Filters
2. Logging and Auditing Filters
3. Image conversion Filters
4. Data compression Filters
5. Encryption Filters...

Filter的拦截配置：

```xml
<filter>
    <filter-name>f4</filter-name>
    <filter-class>a.MyFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>f4</filter-name>
    <servlet-name>hello</servlet-name>   <!--拦截某个servlet-->
    <url-pattern>/sss/*</url-pattern>   <!--url模式拦截-->
</filter-mapping>
```

*写一个filter



```java
// 这个路径仍然是绝对路径
File file = new File("/WEB-INF/contact/a.txt");
// 这个是可以将web应用的根路径转换成操作系统的路径
String s = getServletContext().getRealPath("/WEB-INF/contact/a.txt");
// 若打包到war，这样的路径仍是不对的，可以下面或ClassLoader
InputStream in = getServletContext().getResourceAsStream("/WEB-INF/contact/a.txt");
```

分页功能，List的subList方法，取出子集合

## listener

```java
@WebListener
public class ApplicationIntializer implements ServletContextListener{
    public void contextInitialized(ServletContextEvent sce){
        ServletContext context = (ServletContext)sce.getServletContext();
        
        DataSource ds = ...;
    }
    public void contextDestroyed(ServletContextEvent sce){
        ...
    }
}
```

HttpSessionBindingEvent的getValue()返回旧的值

## Jsp

Jsp本质上是Servlet，是服务器中的组件。tomcat运行jsp时将其转化为.java再编译为.class文件。

JspPage类的生命周期：jspInit(), jspDestroy()

HttpJspPage的服务函数，_jspService(HttpServletRequest request, HttpSerletResponse response){}

```jsp
// time.jsp
<%= new java.util.Date()%>
```

jsp的九个隐式对象：

![1569217469521](C:\Users\MAIBENBEN\AppData\Roaming\Typora\typora-user-images\1569217469521.png)

（重点1）JspWriter

1. 与System.out.println以及response.getWriter()的区别：JspWriter会抛出异常，IOException。记得一定要try-catch
2. out对象存在一个新的缓冲区

（重点2）PageContext

*fascade pattern 门面模式，将复杂的系统功能抽象为一个“门面”——更加友好的接口。PageContext封装了jsp在运行过程中使用的所有对象。将jsp的对象传给java只需要一个pageContext

（注意）下面的程序可能会有NullPointerException

```jsp
<%
	String parameter = request.getParameter("page");
	int page = Integer.paraseInt(parameter);
%>
// http://localhost:8080/SearchPage?page=&id=22
// 若url中有page=但没有参数，那么返回空串；若没有page=则返回null
```

（注意）jsp声明一般不声明变量，因为在并发情况下这些变量是不安全的，除非加上syncronized修饰

```jsp
// 页面转发
<jsp:forward page="/servlet/a" />
<%
	request.getRequestDispatcher("/servlet/a").forward(request,response);
	pageContext.forward("/servlet/a");
%>
```

（考）<jsp:include page="/serlvet/a" /> 与 <%@ include file="/serlvet/a"%>的区别，第二个是在编译时包含——静态包含，只能包含静态文件，在jsp转换到serlvet过程中进行包含；而第一个是动态包含。

*useBean转换到java代码

```jsp
<jsp:useBean id="a" class="com.abc.bean.Student" scope="page|request|session|application" />
// 猜数游戏的javaBean应该在session域中
// jsp中取对象属性
<jsp:getProperty name="a" property="age" />
<%= a.getAge() %>
${a.age}
// 设置属性
<jsp:setProperty name="a" property="age" value="26"/>
<jsp:setProperty name="a" property="*" />
```

注意：javaBean的属性都是小写开头！

```jsp
// (考试坑)
<input name="Age"></intput>
<jsp:setProperty name="a" property="age" param="Age"/>
// 注意param与html中name的大小写！
```

*MVC(模型-视图-控制器)



分页逻辑：定义一个PageBean，对分页所需要的数据进行管理

自定义标记（javax.servlet.jsp.tagext)

```java
// 标记处理器类

import ...
import javax.servlet.jsp.tagext.*;

/*public class TimeTag extends TagSupport{
    
    public int doStartTag() throws JspException{
        // pageContext是TagSupport中的保护成员
        JspWriter out = pageContext.getOut();
        out.println(new Date());
        // 但是此时需要对JspWriter做异常处理
        // 不过这里只能通过try-catch来解决
    }
    
}*/

public class ATag extends SimpleTagSupport{
    pubilc String color = "black";
 	public void getData(string data){
        this.color = data;
    }
    
    public void doTag() throws JspException, IOException{
        JspWriter out = getJspContext.getOut();
        ...
    }
}
```

## Tag handler

...

```jsp
// tag的配置  ---创建标签库
// WEB_INF/a.tld
<tag-lib>
    // 给整个标记库定义一个名字
    <uri>Http://www.abc.com/javaee</uri>
	<tag>
		<name>reset</name>
        <tag-class>ResetTag</tag-class>
        <body-content>JSP</body-content>
    </tag>
</tag-lib>

// 引入到jsp中的方法（必考）
<%@ taglib uri="\WEB-INF\a.tld" prefix="t"%>
// 若用网上下载的，只有jar包，那么使用整体定义的名字,支持打包
<%@ taglib uri="Http://www.abc.com/javaee" prefix="t"%>
```

*xml命名空间

（问答题）与数据库建立连接时，两种类加载方式的区别：

```java
// 1, 这种方式需要在编译前就把类放到classpath中
Driver driver = new com.mysql.jdbc.Driver();
// 2, 通过反射机制
Class.forName("com.mysql.jdbc.Driver");
```



## JNDI

（必考） java命名目录服务接口。主要功能：注册，查找

```java
// 利用命名服务来获得DataSource
DataSource ds = (DataSource)ctx.lookup("java:comp/env/jdbc/ExamDB");
// lookup中填写的是 java计算机环境变量  其中“java:comp/env/”为命名前缀
```



## Structs  1

a rapid framework for web development based on JavaEE，基于MVC设计模式。降低软件的粒度，提高了Controller的复用性。分为前端控制器和后端控制器。

（曾经考题）structs 框架的配置文件名 不一定要叫 structs-config.xml

前端控制器（ActionServlet），后端控制器（Action）。控制器功能写在execute()函数中。控制器的另一个功能：选择视图输出。

```
前端控制器名称：org.apache.structs.action.ActionServlet
```

*（考）DispatchAction (由汇聚到支流)，降低Action数量



## Structs 2

使用structs 2 的步骤：

1. 创建文件夹目录结构：![1570858700925](C:\Users\MAIBENBEN\AppData\Roaming\Typora\typora-user-images\1570858700925.png)
2. 导入structs2，import struct 2
3. config, deployment

*structs 1 与 structs 2的区别：



ResourceBundle 支持国际化，

## Spring

是一个轻量级控制反转（IoC）和面向切面编程（AOP）的容器。

（考试）什么是IoC，DI?

```txt
IoC(Inversion of Control)控制反转：将对象的生命周期控制，交给第三方容器
DI(Dependency injection)依赖注入：组件之间的依赖关系由容器在运行期决定，即	由容器动态地将某种依赖关系注入到组件之中
```

*工厂模式， 工厂方法模式， 抽象工厂模式

（考）ClassPathXmlApplicationContext ,FileSystemXmlApplicationContext   前者的xml文件在classpath中（支持打包），后者通过绝对路径指定xml文件。

web应用中创建spring容器，使用XmlWebApplicationContext。听众接口：ServletContextListener，其中两个事件处理函数，

```
// 必考要记
public void contextInitialized(ServletContextEvent sce){
	Context ctx = new InitialContext();
	DataSource ds = (DataSource)ctx.lookup("java:comp/env/jdbc/ExamDB");
	sce.getServletContext().setAttribute("ds",ds);
}
public void contextDestroyed(ServletContextEvent sce){}
```

*（必考）web.xml中声明一个组件：

```xml
<web-app>
	<listener>
    	<listener-class></listener-class>                     
    </listener>
</web-app>
```

AOP编程

（任务）

```
理解BeanFactory
理解ApplicationContext
理解FileSystemXmlApplicationContext
理解ClassPathXmlApplicationContext
理解XmlWebApplicationContext
重点理解JavaEE Web Application如何集成Spring框架
理解Spring framework的配置
```

spring mvc 前端控制器的mapping 不要使用/* , 而应该使用/ (优先级最低) ,否则会拦截.jsp



MVC 三者之间的关系

```
View 访问模型
Controller 前端控制器：访问View(可复用) 后端控制器: 访问模型(不可复用)
```

