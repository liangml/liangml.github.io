# Lrzsz

# 安装lrsz

```shell
brew install lrzsz
```

# iterm2-send-zmodem.sh

```shell
#!/bin/bash
osascript -e &#39;tell application &#34;iTerm2&#34; to version&#39; &gt; /dev/null 2&gt;&amp;1 &amp;&amp; NAME=iTerm2 || NAME=iTerm
if [[ $NAME = &#34;iTerm&#34; ]]; then
	FILE=$(osascript -e &#39;tell application &#34;iTerm&#34; to activate&#39; -e &#39;tell application &#34;iTerm&#34; to set thefile to choose file with prompt &#34;Choose a file to send&#34;&#39; -e &#34;do shell script (\&#34;echo \&#34;&amp;(quoted form of POSIX path of thefile as Unicode text)&amp;\&#34;\&#34;)&#34;)
else
	FILE=$(osascript -e &#39;tell application &#34;iTerm2&#34; to activate&#39; -e &#39;tell application &#34;iTerm2&#34; to set thefile to choose file with prompt &#34;Choose a file to send&#34;&#39; -e &#34;do shell script (\&#34;echo \&#34;&amp;(quoted form of POSIX path of thefile as Unicode text)&amp;\&#34;\&#34;)&#34;)
fi
if [[ $FILE = &#34;&#34; ]]; then
	echo Cancelled.
	# Send ZModem cancel
	echo -e \\x18\\x18\\x18\\x18\\x18
	sleep 1
	echo
	echo \# Cancelled transfer
else
	/usr/local/bin/sz &#34;$FILE&#34; --escape --binary --bufsize 4096
	sleep 1
	echo
	echo \# Received &#34;$FILE&#34;
fi
```

# iterm2-recv-zmodem.sh

```shell
#!/bin/bash
osascript -e &#39;tell application &#34;iTerm2&#34; to version&#39; &gt; /dev/null 2&gt;&amp;1 &amp;&amp; NAME=iTerm2 || NAME=iTerm
if [[ $NAME = &#34;iTerm&#34; ]]; then
	FILE=$(osascript -e &#39;tell application &#34;iTerm&#34; to activate&#39; -e &#39;tell application &#34;iTerm&#34; to set thefile to choose folder with prompt &#34;Choose a folder to place received files in&#34;&#39; -e &#34;do shell script (\&#34;echo \&#34;&amp;(quoted form of POSIX path of thefile as Unicode text)&amp;\&#34;\&#34;)&#34;)
else
	FILE=$(osascript -e &#39;tell application &#34;iTerm2&#34; to activate&#39; -e &#39;tell application &#34;iTerm2&#34; to set thefile to choose folder with prompt &#34;Choose a folder to place received files in&#34;&#39; -e &#34;do shell script (\&#34;echo \&#34;&amp;(quoted form of POSIX path of thefile as Unicode text)&amp;\&#34;\&#34;)&#34;)
fi

if [[ $FILE = &#34;&#34; ]]; then
	echo Cancelled.
	# Send ZModem cancel
	echo -e \\x18\\x18\\x18\\x18\\x18
	sleep 1
	echo
	echo \# Cancelled transfer
else
	cd &#34;$FILE&#34;
	/usr/local/bin/rz --rename --escape --binary --bufsize 4096 
	sleep 1
	echo
	echo
	echo \# Sent \-\&gt; $FILE
fi
```

# iterm2 triggers配置

```shell
Regular expression: rz waiting to receive.\*\*B0100
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-send-zmodem.sh
Instant: checked

Regular expression: \*\*B00000000000000
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-recv-zmodem.sh
Instant: checked
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/lrzsz/  

