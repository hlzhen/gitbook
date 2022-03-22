# Linux JDK 安装

## 1. 下载安装
```markdown
1. 安装前需先检查是否有自带jdk,如有先删除在继续安装
    -  检查是否已经存在java相关命令
        rpm -qa|grep java
        rpm -qa|grep jdk
        rpm -qa|grep gcj
    - 如果需要卸载
    rpm -qa | grep java | xargs rpm -e --nodeps

2. 下载地址：https://www.oracle.com/java/technologies/downloads/
3. 在/usr/local/目录下新建文件夹java
4. 把下载的安装包上传到/usr/local/java/
5. 解压 tar -zxvf jdk-8u291-linux-x64.tar.gz
```

## 2. 配置环境
```markdown
1. 编辑配置文件
vim /etc/profile

2. 在末尾追加
export JAVA_HOME=/usr/local/java/jdk1.8.0_291
export JRE_HOME=/usr/local/java/jdk1.8.0_291/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$PATH

3. 使配置文件生效
source /etc/profile
```

## 3. 测试
```markdown
1. 测试javac命令
javac

2. 测试java
java

3. 测试版本号
java -version

版本号返回如下
java version "1.8.0_291"
Java(TM) SE Runtime Environment (build 1.8.0_291-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.291-b10, mixed mode)

4. 查询JAVA_HOME
echo $JAVA_HOME

返回
/usr/local/java/jdk1.8.0_291
```