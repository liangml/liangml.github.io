# Macosboot

## macos 启动过程
## launchd开启之后，会依次去完成以下的工作：
* 根据`/System/Library/LaunchDaemons` 和`/Library/LaunchDaemons`路径下的plist文件，加载系统级守护进程；
* 注册上述守护进程需要的套接字及文件描述符；
* 根据plist文件中的KeepAlive键值，启动那些需要在系统周期内一直保持的进程；
* 根据plist文件中的设定，在条件满足时启动进程；
* 关机时，给所有由launchd开启的进程发送SIGTERM信号。
我们将log信息中的内容与`/System/Library/LaunchDaemons`路径下的plist进行对照，发现在系统开启之初的bootlog，blued，mDNSResponder等都能再该路径下找到。

### LaunchDaemons路径下的plist指定的进程启动是否存在一定的先后顺序呢？
> 在launchd依次完成的工作中，可以看到它是先注册套接字和文件描述符，然后才去启动进程，因此plist指定的进程的启动先后顺序并不明确。

* launchd配置文件总共有五个路径，在系统开启之初，只加载了`/System/Library/LaunchDaemons` 和`/Library/LaunchDaemons`路径下的plist文件，另外三个路径下的plist文件是在用户login之后才进行的。
* 用户的login是由loginwindow进程完成的，而loginwindow的启动又是由`/System/Library/LaunchDaemons`路径下的com.apple.loginwindow.plist指定的。
* 用户登录之后，launchd才会去加载`/System/Library/LaunchAgents` 和`/Library/LaunchAgents`以及`~/Library/LaunchAgents`路径下的plist文件，从而根据plist文件的具体设置去启动相应的进程。

---

> 作者: Leon  
> URL: https://blog.liangml.dpdns.org/posts/macosboot/  

