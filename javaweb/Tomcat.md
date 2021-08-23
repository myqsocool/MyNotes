Tomcat9解压及安装
### 配置环境变量JAVA_HOME/JRE_HOTM
JAVA_HOME:指向JDK安装地址
JRE_HOME:指向JRE安装地址
部分window系统要求指定tomcat安装地址
CATALINA_HOME:tomcat安装地址


### Tomcat的启动关闭
1. 启动与关闭命令存在Tomcat的bin中
2. 启动命令:start.bat /startup
3. 关闭命令: shutdown.bat/shutdown  关闭时，直到抛出异常才是正确关闭


### 网站的创建
在Tomcat的/webapps文件夹下创建网站


### IDEA管理Tomcat
1. 添加服务器
file->setting->Build……->Application Servers->添加Tomcat路径
2. 设置启动开关
Run->Edit Configurations->+找到Tomcat Server(local本地开关，remote远程开关)->配置
3. 发布网站
Run->Edit Configurations->之前设置的开关->Deployment->



web文件夹下
静态资源文件,jar包,配置文件
其中jar包和配置文件在web(+app)->【WEB-INF】下，jar包在lib文件夹中需要自己建

