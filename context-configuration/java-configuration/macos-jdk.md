# MacOS JDK安装

## 1. 下载JDK
```markdown
1. Intel下载地址：https://www.oracle.com/java/technologies/downloads/
2. M1下载地址：https://www.azul.com/downloads/?package=jdk
3. 选择对应版本下载安装
```

## 2. 配置环境
```markdown
1. 打开终端
输入 open ~/.bash_profile
2. 添加配置信息(注意修改自己的安装地址)
export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH:.
export CLASSPATH=$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.
3. 保存后刷新
source ~/.bash_profile
```