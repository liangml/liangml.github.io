# Alpine

+ 替换源：
```bash
# alpine
sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories
# ubuntu
sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
# debian
sed -i "s@<http://deb.debian.org@http>://mirrors.aliyun.com@g" /etc/apt/sources.list
sed -i "s@<http://security.debian.org@http>://mirrors.aliyun.com@g" /etc/apt/sources.list
```
+ python：
```bash
apk add python3
apk add py-pip
pip install -U pip \\
pip config set global.index-url <https://mirrors.aliyun.com/pypi/simple/> \\
```
+ dockerfile
```bash
COPY *  target  # 会解压第一层目录
COPY .  target  # 保留原始目录
```



---

> 作者: Leon  
> URL: https://blog.liangml.dpdns.org/posts/alpine/  

