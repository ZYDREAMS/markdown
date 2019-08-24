## 一、linux下JDK的卸载与安装
1. 查看linux安装路径
which java （java执行路径）
echo $JAVA_HOME
echo $PATH
2. 卸载旧版本的JDK
rpm -qa | grep jdk
rpm -qa | grep gcj
可能的结果是：
java-1.7.0-openjdk-headless-1.7.0.51-2.4.5.5.el7.x86_64
java-1.7.0-openjdk-1.7.0.51-2.4.5.5.el7.x86_64
然后执行卸载命令:
 yum -y remove java-1.7.0-openjdk-1.7.0.51-2.4.5.5.el7.x86_64
如果这种方法不行，可以使用如下的方法卸载：
rpm -e –nodeps java-1.7.0-openjdk-headless-1.7.0.51-2.4.5.5.el7.x86_64
rpm -e –nodeps java-1.7.0-openjdk-1.7.0.51-2.4.5.5.el7.x86_64
3. 安装新的JDK
下载JDK，我是用的是：jdk-8u201-linux-x64.tar.gz
将jdk-8u201-linux-x64.tar.gz放到/usr/java中，如果没有这个目录自己新建一个
，放入后执行解压命令：tar -zxvf jdk-8u201-linux-x64.tar.gz 会生成一个jdk1.8.0_201的目录
解压后直接执行命令：vim /etc/profile
在文件最后添加：
JAVA_HOME=/usr/java/jdk1.8.0_201
JAVA_BIN=/usr/java/jdk1.8.0_201/bin
PATH=$PATH:$JAVA_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
添加完后wq保存退出，执行命令source /etc/profile立即使文件生效
最后java -version查看JDK版本信息
