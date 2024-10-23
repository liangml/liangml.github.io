# Alpine

&#43; 替换源：
```bash
# alpine
sed -i &#39;s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g&#39; /etc/apk/repositories
# ubuntu
sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
# debian
sed -i &#34;s@&lt;http://deb.debian.org@http&gt;://mirrors.aliyun.com@g&#34; /etc/apt/sources.list
sed -i &#34;s@&lt;http://security.debian.org@http&gt;://mirrors.aliyun.com@g&#34; /etc/apt/sources.list
```
&#43; python：
```bash
apk add python3
apk add py-pip
pip install -U pip \\
pip config set global.index-url &lt;https://mirrors.aliyun.com/pypi/simple/&gt; \\
```
&#43; dockerfile
```bash
COPY *  target  # 会解压第一层目录
COPY .  target  # 保留原始目录
```



---

> 作者: Leon  
> URL: https://liangml.github.io/posts/alpine/  

