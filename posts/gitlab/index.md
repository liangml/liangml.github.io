# Gitlab

## git lfs 问题
* 客户端配置：```git config --system http.sslverify false```
* 参考连接：https://blog.csdn.net/root_miss/article/details/81450687
## gitlab邮箱配置
```shell
gitlab_rails[&#39;smtp_enable&#39;] = true
gitlab_rails[&#39;smtp_address&#39;] = &#34;smtp.exmail.qq.com&#34;
gitlab_rails[&#39;smtp_port&#39;] = 25
gitlab_rails[&#39;smtp_user_name&#39;] = &#34;gitlab@xxx.cn&#34;
gitlab_rails[&#39;smtp_password&#39;] = &#34;xxx&#34;
gitlab_rails[&#39;smtp_domain&#39;] = &#34;smtp.exmail.qq.com&#34;
gitlab_rails[&#39;smtp_authentication&#39;] = &#34;login&#34;
gitlab_rails[&#39;smtp_ssl&#39;] = false
gitlab_rails[&#39;smtp_enable_starttls_auto&#39;] = true
gitlab_rails[&#39;gitlab_email_from&#39;] = &#34;gitlab@xxx.cn&#34;
user[&#34;git_user_email&#34;] = &#34;gitlab@xxx.cn&#34;
```
## gitlab迁移
* gitlab备份
```shell
gitlab-rake gitlab:backup:create
```
* gitlab 定制相关数据服务
```shell
# 停止相关数据连接服务
gitlab-ctl stop unicorn
gitlab-ctl stop sidekiq
# 从1393513186编号备份中恢复
gitlab-rake gitlab:backup:restore BACKUP=1393513186
# 启动Gitlab
gitlab-ctl start
```
## submodule
* add submodule to project
```shell
git submodule add https://github.com/yyy/xxx.git
```
* list submodule
```shell
git submodule
```
* delete submodule
```shell
git submodule deinit
```
* pull submodule: when git clone add parms --recursive
```shell
git clone https://github.com/xxx.git --recursive
```
* if you forget add --recursive
  * ```git submodule update --init```
  * ```git submodule update --init --recursive```
* update submodule
```git submodule update --remote```
* change branch
```git config submodule.xxx.branch dev```
or ```git config -f .gitmodules submodule.xxx.branch dev```
## gitconfig 常用配置
```shell
[alias]
	ch = checkou
	st = status
	staust = &#39;gitst&#39;
	cf = config
	ck = checkout
	ft = fetch
	fh = fetch
	br = branch
	brv = branch --v
	geturl = config --get remote.origin.url
	bs = bisect
	lg = log --graph --decorate --oneline --all
	cfg = config --global
	cfga = config --global alias.
	pull = pl
	pl = pull --rebase
	cm = commit -c HEAD
	ps = push
	lsr = ls-remote --heads
	udc = reset HEAD~
	ftg = fetch --tags
	am = commit -amend
	cmd = commit --amend
	rsh = reset HEAD~
	cmi = commit --interactive -c HEAD --reset-author
	i = --interactive
	rss = reset --soft
	rmc = rm --cached
	cp = cherry-pick
	cpx = cherry-pick -x
	bl = blame
	gk = gitk
	ltn = ls-tree -r HEAD~ --name-only
	lt = ls-tree -r HEAD~ --name-only
	ltng = ls-tree -r HEAD~ --name-only |grep
	lgd = log -p --full-diff
	bcm = &#34;branch -a --contains &#34;
	brc = branch -a --contains
	tagc = tag --contains
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/gitlab/  

