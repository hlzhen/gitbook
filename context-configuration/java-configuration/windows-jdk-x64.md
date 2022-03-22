# WindowsJDK配置

## 1. JDK下载
```markdown
1. 本文基于64位操作系统配置
2. 下载地址
    - 官网下载地址：https://www.oracle.com/java/technologies/downloads/
    - 阿里云盘地址：https://www.aliyundrive.com/s/AQ1vbedwWu5 提取码: sr45
云盘分享为jdk的原始安装包
```
## 2. 安装
```markdown
1. 点击下载的安装包进行安装
2. 更改jdk的默认安装路径(如下图)
3. 下一步 > 下一步 > 完成安装
```
![jdk默认安装路径](/assets/context/jdk/winx64_jdk.png)

## 3. 环境配置
```markdown
1. 鼠标右击我的电脑(此电脑) -> 属性 -> 高级系统设置 -> 点击环境变量
2. 新建系统变量
    变量名：JAVA_HOME
    变量值：F:\LocalAppFiles\java\jdk1.8.0_151(JDK的安装目录)
3. 新建系统变量
    变量名：CLASSPATH
    变量值：.;%JAVA_HOME%libdt.jar;%JAVA_HOME%libtools.jar
4. 编辑系统变量Path
    新增 -> %JAVA_HOME%\bin
    新增 -> %JAVA_HOME%\jre\bin
```

## 4. 测试
```markdown
1. 命令行输入 java || javac 如出现一大堆信息则表示配置成功
```