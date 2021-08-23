jsp文件的作用是将Servlet中的 doGet/doPost 的执行结果写入到响应体中
## 一: 书写规则

1. 执行标记(<%%>)
```
    一个jsp中可以出现多个执行标记
    所有的执行标记被当作一个整体看待
    在jsp文件中，只有书写在执行标记中的内容才会被当作java命令
    可以声明java变量，运行表达式，数学运算，关系运算，逻辑运算，控制语句，
```
2. 输出标记(<%=值%>)
```
在jsp文件中，通过输出标记，通知jsp将java变量，运算结果的值写入到响应体中

```
```
例：
<%
for(int i=0;i<=10;i++){
%>

<p><%=i%></p>

<%
})
%>
```


## 二.jsp文件内置的java对象
1. **request**   
**类型**:HttpServletRequest  
**作用**:在JSP文件运行时读取请求包中的信息  
        与Servlet在请求转发过程中实现数据共享  
在JSP文件执行时，借助内置request对象读取请求包参数信息
```
http://localhost:8080/myWeb/request.jsp?userName=Tom&password=123
<%
String username=request.getParameter("userName");
String password=request.getParameter("password")
%>
用户名:<%=username%>
密码:<%=password%>
```
2. **session**
**类型:**HttpSession
**作用:**在JSP文件运行时，可以使用session指向当前用户的session,添加共享数据或者 读取共享数据
```
例：
session_1.jsp
<%
设置session
session.setAttribute("key1",1000);
%>

session_2.jsp
<%
读取session_1.jsp中的数据
Integer value=(Integer)session.getAttribute("key1");
%>
session的值为<%=value%>

```

3. **aplication;全局作用域对象**
```

<%
存放共享数据
application.setAttribute("key1","application");
%>

jsp读取
<%
String value=(String)application.getAttribute("key1");
%>
<%=value%>
====================================================================
Servlet读取
void doGet(HttpServletRequest request,HttpServletReponse reponse){
    得到全局作用域对象
    ServletContext application=request.getServletContext();
    String value=(String)application.getAttribute("key1");
}


```

## 三.Servlet与JSP文件分工
1. 各自任务
    Servlet:负责处理业务并得到处理结果
    JSP:不负责业务处理，主要任务将Servlet中处理的结果写入到响应体中

2. Servlet与JSP之间的调用关系
Servlet工作完毕后，一般通过请求转发的方式，向Tomcat申请调用JSP

3. Servlet与JSP之间实现数据共享
Servlet将处理结果添加到【请求作用域对象】
JSP文件在运行时从【请求作用域对象】中得到处理结果


## 


