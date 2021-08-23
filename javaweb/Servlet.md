## 一.Servler规范
 Servler规范来自与javaee规范中的一种
#### 作用:
    + 在Servelet规范中，指定【动态资源文件】开发步骤
    + 在Servlet规范中，指定Http服务器调用动态资源文件规则
    + 在Servlet规范中，指定Http服务器管理动态资源文件实例对象的规则


## 二.Servlet接口实现类
1. Servlet接口来自于Servlet规范下的一个接口，这个接口存在Http服务器提供的jar包
2. Tomcat服务器下的lib文件有一个serlet-api.jar存放Servlet接口(javax.servlet.Servlet)
3. Serverlet规范中认为，http服务器能调用的【动态资源文件】必须是一个Servlet接口的实现类

### Servlet接口实现类的开发步骤
1. 创建一个java类，继承HttpServlet父类，成为一个Servlet接口实现类
2. 重写HttpServlet父类的两个方法。【doGet/doPost】
浏览器以get的方式请求时，doGet
浏览器以post的方式请求时，doPost
3. 【注册】Servlet接口实现类信息到Tomcat服务器中
【网站】->【web】->【WEB-INF】->【web.xml】
将Servlet接口实现类的类路径地址交给Tomcat
```
<servlet>
    <!--声明一个变量存储servlet接口实现类的类路径-->
    <servlet-name>first</servlet-name>
    <!-- 声明servlet接口实现类 -->
    <servlet-class>com.example.firstWeb.OneServlet</servlet-class>
    <!--填写一个大于0的整数，自动创建某个Servlet接口实现类的实例对象-->
    <load-on-startup>30</load-on-startup>
</servlet>

<!-- 降低用户访问Servle接口实现类难度，需要设置简短的请求别名 -->
<!--设置别名-->
<servlet-mapping>
    <servlet-name>firt</servlet-name>
<!--设置简短的请求别名,书写时必须以“/”开头-->
     <url-pattern>/one</url-pattern>
</servlet-mapping>    

```

## 三.Servlet对象的生命周期
1. 网站中所有的Servlet接口实现类的实例对象，只能由http服务器负责创建。
    开发人员不能手动创建Servlet接口实现类的实例对象  

2. 在默认情况下，Http服务器接收到对于当前Servlet接口实现类第一次请求时才会自动创建这个Servlet接口实现类的实例对象  
在手动配置的情况下，要求http服务器在启动时自动创建某个Servlet接口实现类的实例对象
```
<servlet>
    <servlet-name>first</servlet-name>
    <servlet-class>com.example.firstWeb.OneServlet</servlet-class>
    <!--填写一个大于0的整数，自动创建某个Servlet接口实现类的实例对象-->
    <load-on-startup>30</load-on-startup>
</servlet>
```
  
3. 在Http服务器运行期间，一个Servlet接口实现类只能被创建出一个实例对象
4. 在Http服务器关闭的时候，自动将网站中所有的Servlet对象进行销毁


## 四.HttpServletResponse接口
1. 介绍
    + HttpServletResponse接口来自于Servlet规范中，在Tomcat中存在Serlet-api.jar
    + HttpServletResponse接口实现类由Http服务器负责提供
    + HttpServletResponse接口负责将doGet/doPost方法的执行结果写入到【响应体】交给浏览器
    + 通常将HttpServletResponse接口修饰的对象称为【响应对象】
2. **主要功能**
    + 将执行结果以二进制的形式写入到【响应体】中
    + 设置响应头中的[content-type]属性值，控制浏览器使用对应的编译器，将响应体中的二进制数据编译为【文字图片视频等】
    + 设置响应头中的【location】属性，将一个请求地址赋值给location.**【response.sendRedirect(location地址)】**
    从而控制浏览器向执行的服务器发送请求。

```
        //方法处理后的返回结果(假设)
        String result="hello<br>jack<br>rolls";
        String result2 = "你好<br>你好<br>你好";
        String result3="https://www.baidu.com?username=jack";


        //设置响应头content-type
        response.setContentType("text/html;charset=utf-8");

        //通过响应对象，将地址赋值给响应头中location属性
        response.sendRedirect(result3);//location="https://www.baidu.com"
        /**
         * 浏览器在接收到响应包之后
         * 如果发现响应头中存在location属性
         * 自动通过地址栏向location指定网站发送请求
         *
         * sendRedirect方法远程控制浏览器请求行为【请求地址，请求方式，请求参数】
         */
        //通过响应对象将结果写入到响应体中------->start
        //向Tomcat索要输出流
        PrintWriter out = response.getWriter();

        //响应对象将结果写入到响应体中----------->start
        //通过输出流将结果写入到响应体
        //推送到浏览器
        out.print(result);
        out.print(result2);
```

## 五.HttpServletRequest接口
1. 介绍
    + HttpServletRequest接口来自Servlet规范，在Tomcat中存在serlet-api.jar
    + HttpServletRequest接口实现类由Http服务器负责提供
    + HttpServletRequest接口负责在doGet/doPost方法运行时读取Http请求协议包中信息
    + 通常将HttpServletRquest接口修饰的对象称为【请求对象】

2. **作用**
    + 可以读取Http请求协议包中【请求行】信息
    + 可以读取保存在Http协议包中【请求头】或【请求体】中请求参数信息
    + 可以代替浏览器向Http服务器申请资源文件调用
```
作用一：
        1.通过请求对象，读取【请求行】中的【url】信息
        String url=req.getRequestURL().toString();
        //2.通过请求对象，读取【请求行】中的【method】信息
        String method=req.getMethod();
        //3.通过请求对象，读取【请求行】中的uri信息
        /**
         * URI:资源文件精准定位地址，在请求行并没有URI这个属性
         *  实际上URI中截取一个字符串，这个字符串的格式 ”/网站名/资源文件名“
         *  URI用于让Http服务器对被访问的资源文件进行定位
         */
        String uri=req.getRequestURI();

==============================================================
作用二:

    1.通过请求对象获得【请求头】中【请求参数】的值，调用请求对象获取请求体参数信息
        String username=request.getParameter("username")
------------------------------------------------------
    2.通过请求对象获得【请求头】中【所有请求参数】，
        Enumeration paramNames=request.getParameterNames();
        while (paramNames.hasMoreElements()){
            String paramName=(String)paramNames.nextElement();
            //通过请求对象读取指定的请求参数的值
            String value=request.getParameter(paramName);
            System.out.println("请求参数:"+paramName+"="+value);
        }
===============================================================
作用三:
```

## 六.请求对象和响应对象的生命周期
1. 在Http服务器接收到浏览器发送的【http请求协议包】后，
自动为当前【http请求协议包】生成一个【请求对象】和一个【响应对象】
2. 在Http服务器调用doGet和doPost方法时，负责将【请求对象】和【响应对象】作为实参作为传递到方法中，确保doGet和doPost正确执行
3. 在Http服务器准备推送Http响应协议包之前，负责将本次请求关联的【请求对象】和【响应对象】销毁
** 浏览器->发送请求【请求对象】-->Tomcat(doGet/doPost)->【响应对象】推送回->浏览器

## 七.欢迎资源文件(默认访问的文件)
1. 默认欢迎资源文件
    用户对某个网站发送请求时，http服务器自动从当前网站返回资源文件
    正常:http://localhost:xxxx/myWeb/index.html
    默认:http://localhost:xxxx/myweb/
2. Tomcat对于默认欢迎资源文件的定位规则
    + 规则位置:Tomcat安装位置/conf/web.xml, 一般在文件最下方 welcome-file-list
```
    <welcome-file-list>
        <welcome-file>login.html</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>

```
3. **设置当前网站的默认欢迎资源规则**  
    + 位置： 网站/web/WEB-INF/web.xml
    + 规则语法和Tomcat中的一样（同上），**如果将Servlet作为默认资源文件时，开头的斜线必须去掉**
    + 网站自定义默认文件，此时Tomcat自带的定位规则将失效
```
 <welcome-file-list>
        <welcome-file>login.html</welcome-file>
        <welcome-file>user/add</welcome-file>
    </welcome-file-list>
```

## 八.Http状态码
1. 介绍
    1. 由三位数组组成的一个符号
    2. Http服务器在推送响应包之前，根据本次请求处理情况，将Http状态码写入到响应包中的【状态行】上
    3. 作用：
        + 如果Http服务器针对本次，返回了对应的资源文件。通过Http状态码通知浏览器应该如何处理结果
        如果无法返回对应的资源文件，则通过Http状态码向浏览器解释不能通过服务的原因
2. **分类**
    1. 组成：【100-599】之间，分为5大类(最有特征的)
        + 1xx:**【100】**:
        通知浏览器本次返回的资源文件不是一个独立的资源文件，
        需要浏览器在接收响应包后，继续向Http服务器索要依赖的其他资源文件
        + 2xx:**【200】**:
        通知浏览器本次返回的资源文件是一个完整独立的资源文件，
        浏览器在接收后不需要索要其他关联的文件
        + 3xx:**【302】**:
        通过之浏览器本次返回的不是一个资源文件的内容，而是一个资源文件地址，
        需要浏览器根据地址自动发送请求来索要这个资源文件
        如：response.sendRedirest("资源文件地址/网址")写入到响应头的location中，这个行为将会出现302状态码
        + 4xx:
            + **【404】**:通知浏览器，在服务端没有定位到被访问的资源文件，不能访问
            + **【405】**:(servlet中)通知浏览器，在服务端中定位到了被访问的资源文件，
            但是这个Servlet对于浏览器采用的请求方式不能处理
        + 5xx:**【500】**:(servlet中)
            通知浏览器，在服务端已经定位到被访问的资源文件，这个Servlet可以接收浏览器采用的请求方式
            但是Servlet在处理请求期间，由于java异常导致处理失败

-----------------------------
## 九.多个Servlet间的调用规则
某些来自于浏览器发送的请求，往往需要服务端中多个Servlet协同处理。
但浏览器一次只能访问一个Servlet,导致用户需要手动通过浏览器发起多次请求才能得到服务。
增加了用户获取服务的难度，导致用户放弃访问当前网站

提高用户使用体验的解决方案：
    无论本次请求涉及到多少个Servler，用户只需要【手动】通知浏览器发起一次请求即可

**动态获取虚拟路径：
给客户端浏览器使用
String contextPath=request.getContextPath();
repose.sendRedirect(contextPath+"/login")
**
1. 多个Servlet之间调用的解决方案：
    1. **重定向**
    2. **请求转发**

## 9.1:**重定向(重点)**
1. 原理:
用户第一次通过【手动的方式】通过浏览器访问AServlet。
AServlet工作完毕后，将BServlet地址写入到响应头的location中
此时，Tomcat会将**302**状态码写入到状态行中提醒浏览器这是一个资源地址
浏览器读取状态码自动根据响应体中的location地址发起第二次请求，访问BServlet完成请求

2. **实现:**
    response.sendRedirect("请求地址");【/网站名/资源文件名】
    将地址写入到响应包中的响应头的location属性中
3. **特征**
    1. 请求地址
    既可以将当前网站内部的资源文件地址发送给浏览器
    也可以将其他网站的资源文件地址发送给浏览器(http://ip地址(域名):端口号/网站名/资源文件名)
    2. 请求次数
    浏览器至少发送两次请求，但是只有第一次请求时用户手动发送，后面都是浏览器自动发送。
    3. **请求方式【get】**
    重定向解决方案中，通过地址栏通知浏览器发起下一次请求，因此通过重定解决的方案调用的资源文件接收的请求方式一定是【get】
4. **缺点**
    重定向需要在浏览器于服务器之间进行多次往返
    在往返的次数上会消耗大量时间，增加用户等待时间

## 十一:**请求转发(重点)**
1. 原理:
    用户第一次通过手动的方式要求浏览器访问AServlet
    AServlet工作完毕后，通过当前的请求对象代替浏览器
    向Tomcat发送请求，申请调用BServlet
    Tomcat在接收到这个请求后，自动调用BServlet来完成剩余任务

    **AServlet(向Tomcat请求转发BServlet)->BServlet(请求转发CServlet)->CServlet->……**

2. **实现**
请求对象代替浏览器向Tomcat发送请求
```
1. 通过当前请求对象生成资源文件申请报告对象
RequsetDispatcher report=request.getRequestDispatcher("/资源文件名");//一定要以"/"开头
2. 将报告的对象发送给Tomcat
report.forward(当前的请求对象,当前的响应对象);

例：
//通过当前请求对象生成two资源文件的申请报告
RequestDispatcher report=req.getRequestDispatcher("/two");
//将报告发送给Tomcat，由Tomcat自动调用two来完成剩余任务
report.forward(req,resp);

```

3. **优点**
    + 无论本次请求涉及到多少个Servlet，用户只需要手动通过浏览器发送一次请求
    + Servlet之间调用发生在服务端计算机上，节省服务端与浏览器之间的往返次数，增加处理服务的速度

4. **特征**
    + 请求的次数:  
    在请求转发过程中，浏览器只发送一次请求
    + 请求地址:  
    只能向Tomcat服务器申请调用当前网站下资源文件地址
    request.getRequestDispathcer("/资源文件名");//不能写网站名/网址
    + 请求方式:  
    在请求转发的过程中，浏览器只发送了一个Http请求协议包，
    参与请求的所有Servlet共享同一个请求协议包，
    所以**Servlet接收的请求方式与浏览器发送的请求方式保持一致**


-----------------------------

## 十二.多个Servlet之间的数据共享
1. 数据共享:AServlet工作完毕后，将产生的数据交给TwoServlet使用
2. Servlet规范中提供四种数据共享的方法
    1. ServletContext接口
    2. Cookie类
    3. HttpSession接口
    4. HttpServletRequest接口

## 十三.**ServletContext接口【全局作用域对象】**
1. 介绍
   + 来自于Servlet规范中的一个接口，在Tomcat中存在servlet-api.jar,在Tomcat中负责提供这个接口实现类
   + 如果两个Servlet来自于同一个网站。那么之间可以通过网站的ServletContext实例对象实现数据共享
   + 通常将【ServletContext】对象称为【全局作用域对象】
2. 原理
    每一个网站都存在一个全局作用域对象。
    这个全局作用域对象【相当于】一个Map，
    在这个网站中AServlet可以将一个数据存放到全局作用域对象中，当前网站中其他Servlet此时都可以从全局作用域对象得到这个数据进行使用  

3. **全局作用域对象生命周期**
**贯穿当前网站的整个运行期间**
    + 在Http服务器启动时,自动为当前网站在内存中创建一个全局作用域对象
    + 在Http服务器运行时，一个网站只有一个全局作用域对象
    + 在Http服务器运行时，全局作用域对象一直都是存活状态，
    在Http服务器准备关闭时，全局作用域对象将被销毁

4. **实现**
同一个网站中，AServlet将数据共享给BServlet
```

class AServlet{
    void doGet(HttpServletRequest request,HttpServletResponse response){
        1. 通过【请求对象】向Tomcat索要当前网站中的【全局作用域对象】
        (全局作用域对象通常称为 application)
        ServletContext application=requst.getServletContext();
        2. 将数据添加到全局作用域对象中作为【共享数据】(类似于map,key:value)
        application.setAttribute("key",数据);
    }
}
class BServlet{
     void doGet(HttpServletRequest request,HttpServletResponse response){
        1. 通过【请求对象】向Tomcat索要当前网站中的【全局作用域对象】
        (全局作用域对象通常称为 application)
        ServletContext application=requst.getServletContext();
        2.从全局作用域对象得到指定关键字对应的数据(Object类型的数据，后面根据需求强转)
        Object 数据=application.getAttribute("key")
    }

}

```


## 14.Cookie
1. 介绍
    + Cookie来自于Servlet规范中的一个工具类，存在于Tomcat提供servlet-api.jar中
    + 如果两个Servlet来自于同一个网站并且为同一个浏览器/用户提供服务，此时借助于Cookie对象进行数据共享
    + Cookie存放当前用户的私人数据，在共享数据的过程中提高服务质量
    + Cookie相当于用户在服务端得到的【会员卡】


2. 原理:
用户通过浏览器第一次向网站发起请求申请AServlet，AServlet在运行期间创建一个Cookie存储与用户相关的数据，
AServlet工作完毕后,【将Cookie写入到响应头中】交还给当前浏览器，浏览器接收到响应包后，将cookie存储在浏览器的缓存中
当用户通过【同一个浏览器】再次【访问网站】发送请求申请BServlet时，【浏览器需要无条件的将网站之前推送的Cookie，写入到请求头中】发送过去
此时BServlet在运行时，就可以通过读取请求头中cookie中的信息，得到OneServlet提供的共享数据

3. **实现**
    同一个网站 AServlet与BServlet借助Cookie实现数据共享
```
class AServlet{
    void doGet(HttpServletRequest request,HttpServletResponse response){
        1.创建一个Cookie对象，保存共享数据(当前用户数据)
            Cookie相当于一个map
            一个cookie中只能存放一个键值对
            这个键值对的key和value只能是String类型
            键值对中的key不能为中文
        Cookie card=new Cookie("key1","abc");
        Cookie card2=new Cookie("key2","def");

        2.将cookie写入到响应头，交给浏览器
        reponse.addCookie(card)
        reponse.addCookie(card2)
    }


class BServlet{
     void doGet(HttpServletRequest request,HttpServletResponse response){
        
        1. 调用请求对象从请求头得到浏览器返回的所有Cookie
        Cookie[] cookieArray=request.getCookies();

        2. 循环遍历数组得到每一个cookie的key和value
        for(Cookie card:cookieArray){
            //读取key和value
            Strign key=card.getName();
            Strign valuecard.getValue();
            提供服务

        }
     }


```


4. Cookie的生命周期
    + 销毁时机：
        Cookie对象默认存放在浏览器的缓存中，所以只要浏览器关闭，cookie对象就被销毁
    + 在手动设置的情况下，可以要求浏览器将接收的Cookie存放在客户端计算机硬盘上，
    同时需要指定cookie在硬盘上的存活时间。在存活期内，cookie永远不会销毁，存活期到了自动删除  
    ```
    手动设置存活时间
    Cookie card=new Cookie(key,value);
    //按秒设置
    card.setMaxAge(60)
    ```



## 十五:HttpSession接口(会话)
1. 介绍
    + HttpSession接口来自Servlet规范下的一个接口。在Tomcat的servlet-api.jar
    实现类由Http服务提供。Tomcat提供实现类存在于servlet-api.jar

    + 如果两个servlet来自于同一个网站，并且为同一个浏览器/用提供服务，此时借助HttpSession对象进行数据共享
    
    + 开发人员习惯将HttpSession接口修饰的对象称为【会话作用域对象】

2. 与Cookie的区别:【面试常问】
    1. 存储位置不同:
        + 【cookie存放在客户端计算机中(内存/硬盘)】
        + 【HttpSession存放在服务端内存中】
    2. 数据类型不同:
        + 【cookie只能存储**String类型**的共享数据】
        + 【HttpSession可以存储任意类型的共享数据**Object类型**】
    3. 数据数量:
        +  一个cookie对象只能存储一个共享数据
        + HttpSession使用map集合存储共享数据，可以存储任意数量的共享数据
    4. 参照物
        + Cookie相当于客户在服务端的【会员卡】
        + HttpSession相当于客户在服务端的【私人保险柜】

3. 实现(和全局作用域共享的使用类似)
在同一个网站AServlet将数据传递给BServlet
```
AServlet{
    public void doGet(HttpServletRequest request,HttpServletResponse response){
        1.调用请求对象向Tomcat索要当前用户在服务端的Session【私人保险柜】
        HttpSession session=request.getSession();
        2. 将数据添加到用户私人保险柜
        session.setAtttribute("key",共享数据)
    }
}
BServlet{
    public void doGet(HttpServletRequest request,HttpServletResponse response){
        1. 调用请求对象向Tomcat索要当前用户在服务端的私人保险柜
        HttpSession session=request.getSession();
        2. 从会话作用域对象得到AServlet提供的共享数据
        session.getAttribute("key")

    }
```

4. Http服务器如何将用户和HttpSession的?
Tomcat在创建一个HttpSession对象时自动为这个HttpSession对象生成一个编号(唯一的)
Tomcat将编号保存到Cookie对象中，推送到当前的浏览器缓存(JSESSIONID=xxxxxx)
等用户下次访问时，Tomcat根据请求头JSESSIONI确认私有有HttpSession以及哪一个是当前用户

5. getSession()与getSession(false)
    1. getSession():
    如果当前用户在服务端已经拥有自己的session,则要求tomcat将这个session进行返回
    如果当前用户在服务端尚未拥有自己的session，则要求tomcat为当前用户创建一个全新的session

    2. getSession(false):
    如果当前用户在服务端已经拥有自己的session,则要求tomcat将这个session进行返回
    如果当前用户在服务端尚未拥有自己的session，此时Tomcat返回null
    3. 使用时机:
    如果用户身份确认，使用getSession();
    如果用户身份不确认，使用getSession(false)

6. HttpSession的生命周期
    + 用户与HttpSession关联时使用的Cookie只能存放在浏览器缓存中
    + 在浏览器关闭时，意味着用户与它的HttpSession关系被切断
    + 由于Tomcat无法检测浏览器何时关闭，因此在浏览器关闭时并不会导致Tomcat将浏览器关联的HttpSession进行销毁
    + 为了解决这个问题，Tomcat为每一个HttpSession对象设置【空闲时间】，这个空闲时间默认为30分钟，如果空闲时间超出则Tomcat认为用户已经放弃了自己的HtppSession，Tomcat销毁这个HttpSession

7. **手动设置HttpSession空闲时间**
用户的HttpSession越多，服务器占用内存就越大,通过缩短空闲时间节省资源
```
当前网站/web/WEB-INF/web.xml
    <session-config>
<!--        设置当前网站中的空闲时间为5分钟-->
        <session-timeout>5</session-timeout>
    </session-config>

```

##  十六.HttpServletRequest接口实现数据共享
1. 介绍
    + 同一网站中，如果两个Servlet之间通过【请求转发】方式进行调用，
        彼此之间共享一个请求协议包。而一个请求协议包只对应一个请求对象
        因此servlet之间共享一个请求对象，此时可以利用这个请求对象在两个Servlet之间实现数据共享
    + 在请求对象实现Servlet之间数据共享时，一般将请求对象称为【请求作用域对象】
2. 实现
AServlet通过请求转发申请调用BServlet时，需要给BServlet提供共享数据
```
AServlet{
    void doGet(HttpServletRequest req,HttpServletResponse response){
        1. 将数据添加到【请求作用域对象】中的attribute属性
        request.setAttribute("key",任意类型Object);
        2. 向Tomcat申请调用TwoServlet
        request.getRequestDispatcher("/two").forward(request,reponse);
    }
}
BServlet{
    void doGet(HttpServletRequest req,HttpServletResponse response){
        从当前请求对象中得到AServlet写入到共享数据(根据需求转换数据类型)
        Object 数据=request.getAttribute("key");
    }
}

```

--------------------------------

## 17.监听器接口[【Servlet规范扩展】
1. 介绍
    + 一组来自于Servlet规范下的接口，共8个接口，在servlet-api.jar中
    + 监听器接口需要开发人员亲自实现
    + 监听器接口用于监控【作用域对象生命周期变化时刻】以及【作用域对象共享数据变化时刻】
2. **作用域对象:**
    + 在Servlet规范中，认为在服务端内存中可以在某些条件下为两个Servlet之间提供数据共享方案的对象，被称为【作用域对象】
    + Servlet规范下的作用域对象:
        ServletContext:全局作用域对象
        HttpSession:会话作用域对象
        HttpServletRequest:请求作用域对象
3. **监听器接口实现类开发规划:(三步)**
    1. 根据监听的实际情况，选择对应监听器接口进行实现
    2. 重写监听器接口中声明【监听事件处理方法】
    3. 在web.xml文件中将监听器接口实现类注册到Http服务器

4. ServletContextListener接口:
    + 作用:通过这个接口合法的检测全局作用域对象被【**初始化时刻以及被销毁时刻**】
    + 监听事件处理方法:  
    ```
    监听器接口一般写在listener包下
    1. public void contextInitlized():在全局作用域对象被Http服务器初始化时被调用
    2. public void contextDestory():在全局作用域对象被Http服务器销毁的时调用
        
    ```
5. ServletContextAttributeListener接口
    + 作用: 可以合法检测全局作用域对象中 **共享数据的变化时刻**
    + 监听事件处理方法
    ```
    1. public void attributeAdded();在全局作用域对象添加共享数据时被触发
    2. public void attributeReplaced();在全局作用域对象更新共享数据时被触发
    3. public void attributeRemoved();在全局作用域对象删除共享数据时被触发

    全局作用域对象共享数据的变化时刻
    ServletContext application=request.getServletContext();
    1.新增共享数据
    application.setAttribute("key1",100);
    2.修改共享数据
    application.setAttribute("key1",200);
    3.删除共享数据
    application.removeAttribute("key1")
    ```

## 18.Filter(过滤器接口)接口[【Servlet规范扩展】
1. 介绍
    + Filter接口实现类由开发人员负责提供，Http服务器不负责提供
    + Filter接口在Http服务器调用资源文件之前，对Http服务器进行拦截
2. 作用：
    1. 拦截Http服务器，帮助Http服务器检测当前请求的合法性
    2. 对拦截的请求对象做增强处理

3. Filter接口实现类开发步骤【三步】
doFilter主要方法
    1. 创建一个java类实现Filter接口
    2. 重写Filter接口中的doFilter方法【对当前的请求和合法性做处理】
    3. web.xml中注册Filter接口实现类到Http服务器中
```
例:
浏览器发起请求 http:localhost:8080/myWeb/1.png?age=70
//    拦截请求对象和响应对象
//    FilterChain将拦截的对象返还
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        //1. 通过拦截请求对象得到请求包参数信息，从而得到来访用户的真实年龄
        String age=servletRequest.getParameter("age");
        //2. 根据年龄帮助Http服务器判断本地请求的合法性
        if (Integer.valueOf(age)<60){
            System.out.println("符合要求");
//            要合法，将拦求截请求对象和响应对象交换给Tomcat,由Tomcat继续调用资源文件
//            放行
             filterChain.doFilter(servletRequest,servletResponse);
        }else {
            //过滤器代替Http服务器拒绝本次请求
            servletResponse.setContentType("text/html;charset=utf-8");
            PrintWriter out=servletResponse.getWriter();
            out.print("<center><h1>拒绝访问</h1></center>");
        }
    }


============================================================
注册到Tomcat中:web.xml
    <filter>
        <filter-name>OneFilter</filter-name>
        <filter-class>com.myq.filter.OneFilter</filter-class>
    </filter>
<!--    通知Tomcat在调用何种资源文件时，需要被当前过滤器拦截-->
    <filter-mapping>
        <filter-name>OneFilter</filter-name>
        <url-pattern>/拦截地址</url-pattern>
    </filter-mapping>

拦截地址：通知Tomcat在调用何种资源文件之前需要调用OneFilter过滤进行拦截

```
4. Filter拦截地址格式
    + 要求Tomcat在调用某个具体文件之前，来调用Filter对象进行拦截
        ```<url-pattern>/img/1.png</url-pattern>```  
    + 要求Tomcat在调用某一个文件夹下所有的资源文件之前，调用Filter拦截
    ```
    img文件夹下的所有资源
    <url-pattern>/img/*</url-pattern>
    ```
    + 要求在调用任意文件夹下的某种类型文件之前，进行拦截
    ```
        拦截所有文件中的jpg文件
        <url-pattern>*.jpg</url-pattern>
    ```
    +  调用网站中的人员文件，都需要进行拦截
    ```
        <url-pattern>/*</url-pattern>
    ```


## 19.利用过滤器防止用户恶意登录行为
```
1. 使用令牌
用户登录时，服务器给用户创建一个Session,资源文件根据用户时候有session判断是否为合法用户
如:login:
        dopost:【request.getSession】
    资源文件:
        doGet:【if(request.getSession(false)==null){拒绝访问}】
缺点:
    1. 增加开发难度(每创建一个servlet都要判断来访者身份的合法性)
    2. 不能保护静态资源文件,只能保护动态资源文件

2.使用过滤器
    1. 通知Tomcat在调用任意文件之前都要先调用当前的过滤器<url-pattern>/*</url-pattern>
    2.重写foFilter方法，向拦截的请求所有HttpSession
    判断是否合法
    需要转型(父类转之类，向下转型)

    例： 通过请求对象向Tomcat索要当前用户的HttpSession
        //将父类无法使用之类中的方法，需要向下转型，使用之类中的方法
        HttpServletRequest request=(HttpServletRequest)servletRequest;

        //调用请求对象读取读取请求包中的请求行中的URI，定位用户访问的资源文件
        String uri=request.getRequestURI();

        //如果请求的资源文件与登录相关【login.html,LoginServlet】时，无条件放行
        //判断uri中是否存在login(login在uri中第一次出现的位置),如果存在，就放行 或者用户访问的是默认页面，也做放行处理
        if (uri.indexOf("/login")!=-1 || "/myWeb/".equals(uri)){
            filterChain.doFilter(servletRequest,servletResponse);
            return;
        }
        //如果访问的是其他的资源文件，需要用户得到Session，注册
        HttpSession session=request.getSession(false);//得到用户的session，如果没有则返回null
        if (session==null){
            //拒绝请求
            HttpServletResponse response=(HttpServletResponse) servletResponse;
            response.sendRedirect("/myWeb/login_err.html");
        }else {
            //合法用户，放行
            filterChain.doFilter(servletRequest,servletResponse);
            return;
        }
```

