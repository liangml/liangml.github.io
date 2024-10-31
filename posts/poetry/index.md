# Poetry: Python虚拟环境管理

&gt; macos 使用
## install
```shell
brew install poetry
```
## 初始化
```shell
mkdir project-demo
cd project-demo
poetry init
```
## 管理虚拟环境
* 创建虚拟环境
* 虚拟环境的命名模式为 项目名-随机数-python版本
```shell
poetry env use PYTHONPATH
```
* 查看当前虚拟环境
```shell
poetry env list
```
* 查看当前poetry配置
```shell
poetry config --list 
```
* 允许在项目目录下创建虚拟环境
```shell
poetry config virtualenvs.in-project true
```
&gt; 如果已经创建了环境需要先移除 ```poetry env remove PYTHONPATH```  
&gt; 创建目录：poetry env use PYTHONPATH
* 退出与启动环境
&gt; 已 poetry 开头的命令自动检测当前环境  
&gt; 同样也可以使用```poetry shell```进入  
&gt; 退出环境：deactivate

## poetry 指令
### 添加依赖包
&gt; poetry add
```shell
poetry add requests
```
## poetry.lock 与更新顺序
&gt; 除了更新 pyproject.toml ，此时项目中还会新增一个文件，名为 poetry.lock ，它实际上就相当于 pip 的 requirements.txt ，详细记录了所有安装的模块与版本。  
&gt; 当使用 poetry add 指令时，poetry 会自动依序帮你做完这三件事：
* 更新 `pyproject.toml`。
* 依照 `pyproject.toml` 的内容，更新 `poetry.lock` 。
* 依照 `poetry.lock` 的内容，更新虚拟环境。
* 由此可见， `poetry.lock` 的内容是取决于 `pyproject.toml` ，但两者并不会自己连动，一定要基于特定指令才会进行同步与更新， `poetry add` 就是一个典型案例。

## poetry lock ：更新 poetry.lock
当你自行修改了 `pyproject.toml` 内容，比如变更特定模块的版本（这是有可能的，尤其在手动处理版本冲突的时候），此时 `poetry.lock` 的内容与 `pyproject.toml` 出现了脱钩，必须让它依照新的 `pyproject.toml` 内容更新、同步，使用指令：
```shell
poetry lock
```
如此一来，才能确保手动修改的内容，也更新到 `poetry.lock` 中，毕竟虚拟环境如果要重新建立，是基于 `poetry.lock` 的内容来安装模块，而非 `pyproject.toml` 。
还是那句话：
&gt; poetry.lock 相当于 Poetry 的 requirements.txt。

但要特别注意的是， `poetry lock` 指令，仅会更新 `poetry.lock` ，不会同时安装模块至虚拟环境
因此，在执行完 `poetry lock` 指令后，必须再使用 `poetry install` 来安装模块。否则就会出现 `poetry.lock` 和虚拟环境不一致的状况。
更多 `poetry lock` 细节可参考 官方文件，其中特别值得注意的是 `--no-update` 参数。

## 新增模块至 dev-dependencies
有些模块，比如 pytest 、 black 等等，只会在开发环境中使用，产品的部署环境并不需要。
Poetry 允许你区分这两者，将上述的模块安装至 dev-dependencies 区块，方便让你轻松建立一份「不包含」 dev-dependencies 开发模块的安装清单。
在此以 Black 为例，安装方式如下：
```shell
poetry add black --group dev
```
结果的区别显示在 pyproject.toml 里：
```[tool.poetry.dependencies]
python = &#34;^3.10&#34;
flask = &#34;^2.3.2&#34;

[tool.poetry.group.dev.dependencies]
black = &#34;^23.7.0&#34;
```
可以看到 black 被列在不同区块： `tool.poetry.dev-dependencies` 。  
**强烈建议善用 `dev-dependencies`**  
善用 `--group dev` 参数，明确区分开发环境，我认为非常必要。  
首先，这些模块常常属于「检测型」工具，相关的依赖模块着实不少！比如 flake8 ，它依赖了 `pycodestyle` 、 `pyflakes` 、 `mccabe` 等等，还有 `black` 、 `pre-commit` ，依赖模块数量也都很可观。  
## Poetry 更新模块
这个就很简单了，使用 poetry update 指令即可：
```shell
poetry update
```
上面指令会更新全部可能可以更新的模块，也可以仅指定特定模块，比如：
```shell
poetry update requests toml
```
关于 `poetry update` 的其余参数，请参考文件。  
还一件重要的事，那就是关于模块版本的升级限制规则，取决于你在 pyproject.toml 中的设定。  
## 列出全部模块清单
```shell
poetry show
```
树状显示依赖层
```shell
poetry show --tree
```
**也可以指定模块显示**
```shell
poetry show celery --tree 
```
## 删除模块
```shell
poetry remove celery
```
## 导出poetry 虚拟环境requirements.txt
```shell
poetry export -f requirements.txt -o requirements.txt --without-hashes
```
* 导出dev的包
```shell
poetry export -f requirements.txt -o requirements-dev.txt --without-hashes --dev
```
## 添加阿里云源
```shell
poetry source add aliyun https://mirrors.aliyun.com/pypi/simple
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/poetry/  

