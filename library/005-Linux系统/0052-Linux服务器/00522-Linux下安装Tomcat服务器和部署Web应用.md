#   在windows环境搭建Linux系统
>维护人员：**liuyang**  
>创建时间：2017-03-16   

### Ⅰ.连接虚拟机
　连接虚拟机需要用到文件传输软件，比较常用的有Xftp[下载地址](http://www.netsarang.com/products/xfp_overview.html)和Xshell[下载地址](http://www.netsarang.com/products/xsh_overview.html)；   
Xftp和Xshell区别：   
Xftp是基于可视化界面操作，上手简单，适合初学者。缺点是错误提示不够详细。   
Xshell是基于命令行操作，错误提示详细。适合熟悉命令行的使用者。
注：使用命令ifconfig 查看虚拟机ip地址
### Ⅱ.安装JDK
tomcat服务器运行时是需要JDK支持的，所以必须配置好JDK用到的那些环境变量
###### ❶ 首先是jdk 1.7 64bit & 32bit的下载地址：
[jdk-7u79-linux-x64.tar.gz](http://download.oracle.com/otn-pub/java/jdk/8u111-b14/jdk-8u111-linux-x64.tar.gz?AuthParam=1492154120_f1de8fbc8653282ec6cba084a91b13c4)     
[jdk-7u79-linux-i586.tar.gz](http://download.oracle.com/otn-pub/java/jdk/8u111-b14/jdk-8u111-linux-x64.tar.gz?AuthParam=1492154120_f1de8fbc8653282ec6cba084a91b13c4)   
###### ❷ 检查并卸载OpenJDK   
第一步检查系统是否自带了OpenJDK以及相关安装包，如果有的话则应先将其卸载。
检查命令：   
java -version    
rpm -qa | grep java    
![](assets/005/00522-a7b2f8f1.png)     
(此图是在虚拟机终端操作，也可在Xshell操作)    
如果发现有安装好的OpenJDK以及安装包的话那么首先依次执行卸载。
卸载命令：    
rpm -e –nodeps tzdata-java-2012c-1.el6.noarch    
rpm -e –nodeps java-1.6.0-openjdk-1.6.0.0-1.45.1.11.1.el6.x86_64     
直接复制上面的命令到xshell会出错，务必注意–nodeps前的两个横杠，注意自行修改。      
![](assets/005/00522-2dab7812.png)
###### ❸ 解压安装
将安装文件上传到linux服务器后，进入到该目录执行解压安装：     
tar -zxvf jdk-7u75-linux-x64.tar.gz    
解压完成后创建一个新的java目录并将刚刚解压的jdk目录移动到我们创建的目录：   
mkdir /wocloud/java    
mv ./jdk1.7.0_75 /wocloud/java/   
最后再删除安装文件：    
rm -rf jdk-7u75-linux-x64.gz
### Ⅲ.配置环境变量
###### ❶ 需要配置的环境变量
 1. PATH环境变量。作用是指定命令搜索路径，在shell下面执行命令时，它会到PATH变量所指定的路径中查找看是否能找到相应的命令程序。我们需要把 jdk安装目录下的bin目录增加到现有的PATH变量中，bin目录中包含经常要用到的可执行文如javac/java/javadoc等待，设置好 PATH变量后，就可以在任何目录下执行javac/java等工具了。    
 2. CLASSPATH环境变量。作用是指定类搜索路径，要使用已经编写好的类，前提当然是能够找到它们了，JVM就是通过CLASSPTH来寻找类的。我们 需要把jdk安装目录下的lib子目录中的dt.jar和tools.jar设置到CLASSPATH中，当然，当前目录“.”也必须加入到该变量中。    
 3. JAVA_HOME环境变量。它指向jdk的安装目录，Eclipse/NetBeans/Tomcat等软件就是通过搜索JAVA_HOME变量来找到并使用安装好的jdk。   

######  ❷三种配置环境变量的方法   
1. 修改/etc/profile文件    
如果你的计算机仅仅作为开发使用时推荐使用这种方法，因为所有用户的shell都有权使用这些环境变量，可能会给系统带来安全性问题。     
![](assets/005/00522-fd60e9da.png)   
·用文本编辑器打开/etc/profile    
![](assets/005/00522-1c003144.png)   
·在profile文件末尾加入：   
export JAVA_HOME=/usr/share/jdk1.6.0_14   
export PATH=$JAVA_HOME/bin:$PATH   
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar   
![](assets/005/00522-538e9e20.png)     
编辑完成后记得输入:wq保存退出，最后一步就是通过source命令重新加载/etc/profile文件，使得修改后的内容在当前shell窗口有效：   
source /etc/profile   
![](assets/005/00522-41c3cbb8.png)   
如上图所示正常显示了jdk版本，到此为止就已经安装成功了。
·重新登录   
·注解   
a. 你要将 /usr/share/jdk1.6.0_14改为你的jdk安装目录   
b. linux下用冒号“:”来分隔路径   
c. PATH/PATH/CLASSPATH / $JAVA_HOME 是用来引用原来的环境变量的值
在设置环境变量时特别要注意不能把原来的值给覆盖掉了，这是一种
常见的错误。   
d. CLASSPATH中当前目录“.”不能丢,把当前目录丢掉也是常见的错误。   
e. export是把这三个变量导出为全局变量。  
f. 大小写必须严格区分。   
2. 修改.bash_profile文件     
这种方法更为安全，它可以把使用这些环境变量的权限控制到用户级别，如果你需要给某个用户权限使用这些环境变量，你只需要修改其个人用户主目录下的.bash_profile文件就可以了。    
·用文本编辑器打开用户目录下的.bash_profile文件    
·在.bash_profile文件末尾加入：    
export JAVA_HOME=/usr/share/jdk1.6.0_14    
export PATH=$JAVA_HOME/bin:$PATH    
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar    
·重新登录
3. 直接在shell下设置变量    
不赞成使用这种方法，因为换个shell，你的设置就无效了，因此这种方法仅仅是临时使用，以后要使用的时候又要重新设置，比较麻烦。    
只需在shell终端执行下列命令：    
export JAVA_HOME=/usr/share/jdk1.6.0_14    
export PATH=$JAVA_HOME/bin:$PATH    
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
### Ⅱ.上传Tomcat服务器
 ![](assets/005/00522-d656f164.png)
 ![](assets/005/00522-5dbb4bf0.png)
### Ⅲ.安装Tomcat服务器
#### 1.解压tomcat服务器压缩包
![](assets/005/00522-58c2dfd4.png)
![](assets/005/00522-38eccef8.png)
### Ⅳ.修改tomcat服务器的启动端口   
![](assets/005/00522-3698b865.png)   
　修改将Tomcat服务器启动时使用的端口，例如改成9999    
![](assets/005/00522-480c1ea0.png)   
修改完server.xml文件之后，保存，退出。   
### Ⅳ.Tomcat服务器启动和关闭    
###### ❶ 启动Tomcat服务器   
　进入tomcat服务器的bin目录，然后执行"./startup.sh"命令启动Tomcat服务器，如下图所示：    
![](assets/005/00522-26d6c725.png)   
查看tomcat服务器的Log信息，看看tomcat服务器是否已经正常启动，进入tomcat服务器下的logs目录，打开catalina.out文件进行查看，如下图所示：    
![](assets/005/00522-2e838a19.png)    
访问Tomcat服务器，如下图所示：   
![](assets/005/00522-76b01ee6.png)   
可以正常访问了，说明Tomcat服务器已经正常启动成功了。   
如果tomcat启动不了，那么最有可能就是启动时使用的端口被其他应用程序占用了，那么可以通过如下的办法进行排查，例如查看9999端口被哪个应用程序占用了。    
![](assets/005/00522-7170db74.png)   
###### ❷ 关闭Tomcat服务器   
进入tomcat服务器的bin目录，然后执行"./shutdown.sh"命令启动Tomcat服务器，如下图所示：   
![](assets/005/00522-601da4da.png)   
查看tomcat服务器的Log信息，看看tomcat服务器是否已经正常关闭，进入tomcat服务器下的logs目录，打开catalina.out文件进行查看，如下图所示：   
![](assets/005/00522-a20016a1.png)
