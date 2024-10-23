# Java

# java home 配置
```shell
vim /etc/profile
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.312.b07-10.p01.ky10.aarch64/jre/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```
## Java
```plain
• Jcmd 开启java 进程remote 端口
jcmd 20364 ManagementAgent.start jmxremote.port=9999 jmxremote.ssl=false jmxremote.authenticate=false

-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9999  -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false  -Djava.rmi.server.hostname=10.1.219.21
• Java dump堆栈信息
jmap -dump:format=b,file=VipQuickRoutePlatAsyn.dat 29473
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/java/  

