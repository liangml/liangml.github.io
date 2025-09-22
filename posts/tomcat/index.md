# Tomcat

# 版本号隐藏
1. 进入到tomcat 目录
2. mv webapps/ROOT webapps/ROOT
3. mkdir webapps/ROOT$(date +%Y%m%d)
4. 编辑conf/server.xml配置文件中的配置项中添加如下配置：
```xml
<Valve className="org.apache.catalina.valves.ErrorReportValve" showReport="false" showServerInfo="false" />
```
5. 修改后再次访问，返回404

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/tomcat/  

