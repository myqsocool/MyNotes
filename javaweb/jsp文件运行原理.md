## 1.Http服务器调用JSP文件步骤:(常常问)
1. Http服务器将JSP文件内容【编辑】为一个Servlet接口实现类
2. Http服务器将Servlet接口实现类【编译】为class文件(.class)
3. Http服务器负责创建这个class的实例对象，这个实例对象就是Servlet实例对象
4. Http服务器通过Servlet实例对象调用_JSPServlet方法，将JSP文件内容写入到响应体
JSP编辑为Servlet接口实现类将这个接口实现类编译为class文件，服务器创建这个class的实例对象，服务器通过实例对象调用Servlet实例方法，将JSP文件内容写入到响应体