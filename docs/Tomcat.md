# Tomcat

## J2EE Tomcat开发环境配置

1.下载tomcat,网址：www.tomcat.apache.org
选择core中的.tar.gz或者.zip下载即可
2.解压安装包。进入安装包所在路径
解压安装包：
3.解压完成之后，将文件夹移动到你的目标位置
4.进入tomcat文件夹下的bin目录中，可以看到
5.使用gedit打开startup.sh文件，在esac那行后面添加JAVA_HOME等信息，其中：
JAVA_HOME:它指向jdk的安装目录，Eclipse/NetBeans/Tomcat等软件就是通过搜索JAVA_HOME变量来找到并使用安装好的jdk。
JRE_HOME：它指向jre的安装路径，通常情况下jdk路径下都会有jre
PATH：所有的系统环境变量都在这里
CLASSPATH：作用是指定类搜索路径，要使用已经编写好的类，前提当然是能够找到它们了，JVM就是通过CLASSPTH来寻找类的。我们需要把jdk安装目录下的lib子目录中的dt.jar和tools.jar设置到CLASSPATH中，当然，当前目录“.”也必须加入到该变量中。
TOMCAT_HOME：tomcat的安装目录
6.通过bin目录下的startup或者shutdown完成

## 其他相关收集

WildFly
