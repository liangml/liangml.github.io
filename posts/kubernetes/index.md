# Kubernetes


## 资源清单格式

```yaml
apiVersion: group/apiversion  # 指定当前的组和版本（写的资源清单具体去哪个路径下调代码）。如果没有给定 group 名称，那么默认为 core，可以使用 kubectl api-versions # 获取当前 k8s 版本上所有的 apiVersion 版本信息( 每个版本可能不同 )
kind: Service      #资源类别
metadata: #资源元数据
  name: pod-demo      #资源对象名称；
  namespace: default   #使用名称级别的资源才需要填写； #处于default名称空间下；
  lables: #标签，筛选操作；一般用于集群内部的筛选功能；
    app: myapp                #标签
  annotations:   # 主要目的是方便用户阅读查找
spec: # 期望的状态（disired state）
  containers: #代表mainC；
    - name: myapp-1            #mainC子对象1名称；列表开头使用   -开头
      image: wangyanglinux/myapp:v1
    - name: busybox-1            #mainC子对象2名称；
      image: busybox:1.35.0
      command: #此处代表的是ENTRYPOINT；
        - "/bin/sh"
        - "-c"
        - "sleep 3600"
status: # 当前状态，本字段有 Kubernetes 自身维护，用户不能去定义
```

## 字段格式配置

```shell
apiVersion <string>          #表示字符串类型
metadata <Object>            #表示需要嵌套多层字段
labels <map[string]string>   #表示由k:v组成的映射
finalizers <[]string>        #表示字串列表
ownerReferences <[]Object>   #表示对象列表
hostPID <boolean>            #布尔类型
priority <integer>           #整型
name <string> -required-     #如果类型后面接 -required-，表示为必填字段
```

## 常用资源清单格式定义

> Object ：对象，下方是对象的属性  
> list：类别，下方包含多个数组，每个数组是一个对象  
> String：字符串 后边直接跟value的值

### 通用字段

| 参数名       | 字段类型    | 说明                                                   |                                                 
|-----------|---------|------------------------------------------------------|
| version   | String  | 这里是指的是K8S API的版本，目前基本上是v1，可以用kubectl apiversions命令音询 |
| kind	     | String	 | 这里指的是yamI文件定义的资源类型和角色，比如: Pod                        |
| metadata	 | Object	 | 元数据对象，固定值就写metadata                                  |
| Spec	     | bject   | 	详细定义对象，固定值就写Spec                                    |

### metadate相关字段

| 参数名	             | 字段类型	    | 说明                          |
|------------------|----------|-----------------------------|
| metadata	        | Object   | 	元数据对象，固定值就写metadata        |
| metadata.name    | 	String	 | 元数据对象的名字，这里由我们编写，比如命名Pod的名字 |
| metadata.lables	 | list     | 	标签列表。有key=value组成          |

### pod相关字段

| 参数名	                  | 字段类型    | 	说明                                                                                                                                                                                                                  |
|-----------------------|---------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spec.restartPolicy    | 	String | 	定义Pod的重启策略，可选值为Always、OnFailure，默认值为Always。1.Always: Pod一旦终止运行，则无论容器是如何终止的，kubelet服务都将重启它。2.OnFailure: 只有Pod以非零退出码终止时，kubelet才会重启该容器。如果容器正常结束 (退出码为0) ，则kubelet将不会重启它3.Never: Pod终止后，kubelet将退出码报告给Master，不会重启该Pod. |
| spec.nodeSelector	    | Object  | 	定义Node的Label过滤标签，以key:value格式指定                                                                                                                                                                                     |
| spec.imagePullSecrets | 	Object | 	定义pull镜像时使用secret名称，以name:secretkey格式指定                                                                                                                                                                             |
| spec.hostNetwork	     | Boolean | 	定义是否使用主机网络模式，默认值为false。设置true表示使用宿主机网络，不使用docker网桥，同时设置了true将无法在同一台宿主机上启动第二个副本。                                                                                                                                     |
| sepc.NodeName	        | string	 | 定义pod要调度的节点，可以忽略污点，还是要预选、优选。                                                                                                                                                                                         |

#### mainC字段

| 参数名	                                         | 字段类型	  | 说明                                                                                                                                                         |
|----------------------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spec.containers[]	                           | list   | 	这里是Spec对象的容器列表定义，是个列表                                                                                                                                     |
| spec.containers[].name	                      | String | 	这里定义容器的名字                                                                                                                                                 |
| spec.containers[].image	                     | String | 	这里定义要用到的镜像名称                                                                                                                                              |
| spec.containers[].imagePullPolicy	           | String | 	定义镜像拉取策略，有Always、Never、lfNotPresent三个值可选 (1) Aways: 意思是每次都尝试重新拉取镜像 (2) Never: 表示仅使用本地镜像 (3) lfNotPresent: 如果本地有镜像就使用本地镜像，没有就拉取在线镜像。上面三个值都没设置的话，默认是Always。 |
| spec.containers[].command[]	                 | List   | 	指定容器启动命令，因为是数组可以指定多个，不指定则使用镜像打包时使用的启动命令                                                                                                                   |
| spec.containers[].args[]	                    | List   | 	指定容器启动命令参数，因为是数组可以指定多个。                                                                                                                                   |
| spec.containers[].workingDir	                | string | 	指定容器的工作目录                                                                                                                                                 |
| spec.containers[].volumeMounts[]	            | List   | 	指定容器内部的存储卷配置                                                                                                                                              |
| spec.containers[].volumeMounts[].name	       | String | 	指定可以被容器挂载的存储卷的名称                                                                                                                                          |
| spec.containers[].volumeMounts[].mountPath	  | string | 	指定可以被容器挂载的存储卷的路径                                                                                                                                          |
| spec.containers[].volumeMounts[].readOnly	   | string | 	设置存储卷路径的读写模式，ture 或者false默认为读写模式                                                                                                                          |
| spec.containers[].ports[]	                   | List   | 	指定容器需要用到的端口列表                                                                                                                                             |
| spec.containers[].ports[].name	              | String | 	指定端口名称                                                                                                                                                    |
| spec.containers[].ports[].containerPort	     | String | 	指定容器需要监听的端口号                                                                                                                                              |
| spec.containers[].ports[].hostPort	          | String | 	指定容器所在主机需要监听的端口号，默认跟上面containerPort相同，注意设置了hostPort同一台主机无法启动该容器的相同副本 (因为主机的端口号不能相同，这样会冲突)                                                                 |
| spec.containers[].ports[].protocol	          | String | 	指定端口协议，支持TCP和UDP，默认值为TCP                                                                                                                                  |
| spec.containers[].env[]	                     | List   | 	指定容器运行前需设置的环境变量列表                                                                                                                                         |
| spec.containers[].env[].name	                | string | 	指定环境变量名称                                                                                                                                                  |
| spec.containers[].env[].value	               | String | 	指定环境变量值                                                                                                                                                   |
| spec.containers[].resources	                 | Object | 	指定资源限制和资源请求的值 (这里开始就是设置容器的资源上限)                                                                                                                           |
| spec.containers[].resources.limits	          | Object | 	指定设置容器运行时资源的运行上限                                                                                                                                          |
| spec.containers[].resources.limits.cpu	      | String | 	指定CPU的限制，单位为core数，将用于docker run --cpu-shares参数 (这里前面文章Pod资源限制有讲过)                                                                                         |
| spec.containers[].resources.limits.memory	   | String | 	指定MEM内存的限制，单位为MIB、GiB                                                                                                                                     |
| spec.containers[].resources.requests	        | Object | 	指定容器启动和调度时的限制设置                                                                                                                                           |
| spec.containers[].resources.requests.cpu	    | String | 	CPU请求，单位为core数，容器启动时初始化可用数量                                                                                                                               |
| spec.containers[].resources.requests.memory	 | String | 	内存请求，单位为MIB、GiB，容器启动的初始化可用数量                                                                                                                              |

#### 就绪探针

| 参数名                                                 | 	字段类型	 | 说明                                                                                                         |
|-----------------------------------------------------|--------|------------------------------------------------------------------------------------------------------------|
| spec.containers.readinessProbe	                     | Object | 	定义容器的就绪探测策略。有三种模式可选:<br/>exec：返回码为0为就绪<br/>tcpsoket：tcp 对应检测端口响应成功为就绪<br/>httpget：返回码 >= 200 && < 400 为就绪 |
| spec.containers.readinessProbe.exec	                | list   | 	定义容器的就绪探测策略为exec。                                                                                         |
| spec.containers.readinessProbe.exec.command	        | List   | 	exec所对应的命令                                                                                                |
| spec.containers.readinessProbe.initialDelaySeconds	 | init   | 	探测开始时间                                                                                                    |
| spec.containers.readinessProbe.periodSeconds	       | init   | 	探测间隔时间。s为单位                                                                                               |

#### 存活探针

| 参数名	                                               | 字段类型	  | 说明                                                                                                         |
|----------------------------------------------------|--------|------------------------------------------------------------------------------------------------------------|
| spec.containers.livenessProbe	                     | object | 	定义容器的就绪探测策略。有三种模式可选:<br/>exec：返回码为0为就绪<br/>tcpsoket：tcp 对应检测端口响应成功为就绪<br/>httpget：返回码 >= 200 && < 400 为就绪 |
| spec.containers.livenessProbe.httpGet	             | object | 	定义容器的就绪探测策略为httpGet。                                                                                      |
| spec.containers.livenessProbe.httpGet.port	        | init   | 	探测的端口                                                                                                     |
| spec.containers.livenessProbe.httpGet.path	        | string | 	探测的文件。根为网页根路径                                                                                             |
| spec.containers.livenessProbe.initialDelaySeconds	 | init   | 	探测开始时间                                                                                                    |
| spec.containers.livenessProbe.periodSeconds	       | init   | 	探测间隔时间。s为单位                                                                                               |
| spec.containers.livenessProbe.timeoutSeconds	      | init   | 	探测超时时间，超时后就是探测失败                                                                                          |

#### 钩子字段

| 参数名                                               | 	字段类型	  | 说明            |
|---------------------------------------------------|---------|---------------|
| spec.containers.lifecycle	                        | object	 | 定义钩子          |
| spec.containers.lifecycle.postStart	              | object	 | 定义启动后的钩子      |
| spec.containers.lifecycle.preStop	                | object  | 	定义退出前钩子      |
| spec.containers.lifecycle.postStart.exec	         | object	 | 定义启动后的钩子动作为命令 |
| spec.containers.lifecycle.postStart.exec.command	 | list	   | 定义启动后的钩子详细动作  |

#### initC字段

> 与容器字段通过，只不过把containers换成initcontainers

| 参数名	                             | 字段类型    | 	说明                                     |
|----------------------------------|---------|-----------------------------------------|
| spec.initcontainers[]	           | list    | 	这里是Spec对象的容器列表定义，是个列表，定义initC容器        |
| spec.initcontainers[].name	      | String	 | 这里定义容器的名字                               |
| spec.initcontainers[].image	     | String  | 	这里定义要用到的镜像名称                           |
| spec.initcontainers[].command[]	 | List	   | 指定容器启动命令，因为是数组可以指定多个，不指定则使用镜像打包时使用的启动命令 |

### 控制器通用字段

| 参数名                   | 字段类型    | 	说明                                         |
|-----------------------|---------|---------------------------------------------|
| spec.replicas         | 	init	  | 副本数（pod数量）                                  |
| spec.template	        | object	 | pod模板，内容就是pod中的字段内容                         |
| spec.selector	        | object  | 	标签匹配（用来区分是谁的pod），将适合的pod加入到该该控制器下 内容是：标签：值 |
| spec.minReadySeconds	 | init	   | 用定义新建的 Pod 经过多少秒后才被视为可用，默认为0。               |
| spec.backoffLimit	    | init	   | 失败次数达到指定次数之后进入懒启动状态（重建间隔时间长）                |

#### 标签选择—标签选择器

> 处理RC其他都支持

| 参数名                                      | 	字段类型	  | 说明                                                                                                                                                                              |
|------------------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spec.selector	                           | object  | 	标签匹配（用来区分是谁的pod），将适合的pod加入到该该控制器下                                                                                                                                              |
| spec.selector.matchLabels[]	             | list	   | 集合式的标签匹配（用来区分是谁的pod），将适合的pod加入到该该控制器下内容是：标签：值（RS支持）                                                                                                                             |
| spec.selector.matchExpressions[]	        | object	 | 支持运算符匹配进行标签选择（用来区分是谁的pod），将适合的pod加入到该该控制器下<br/>内容是：<br/>key： xxxx<br/>operator: 运算符<br/>vlaues：<br/>- xxxx                                                                      |
| spec.selector.matchExpressions[].key	    | string	 | 标签选择器的key字段                                                                                                                                                                     |
| spec.selector.matchExpressions.operator	 | string  | 	标签选择的运算符，有In、NotIn、Exists、DoesNotExist 四个值可选<br/>In ：label 的值在某个列表中（根据value的值进行选择）<br/>NotIn ：label 的值不在某个列表中<br/>Exists ：某个 label 存在（根据key进行选择）<br/>DoesNotExist：某个 label 不存在 |
| spec.selector.matchExpressions[].values  | list	   | 标签选择器下的value字段                                                                                                                                                                  |

### RC 控制器

| 参数名	          | 字段类型  | 	说明                                         |
|---------------|-------|---------------------------------------------|
| spec.selector | 	list | 	标签匹配（用来区分是谁的pod），将适合的pod加入到该该控制器下 内容是：标签：值 |

* RC （ReplicationController ）主要的作用就是用来确保容器应用的副本数始终保持在用户定义的副本数
  。即如果有容器异常退出，会自动创建新的Pod来替代；而如果异常多出来的容器也会自动回收（回收最晚创建的）
* 根据标签确认是否输入控制器
* 不支持集合式的标签匹配

### RS控制器

| 参数名	                                     | 字段类型    | 	说明                                                                                                                                                                            |
|------------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spec.minReadySeconds	                    | init	   | 用定义新建的 Pod 经过多少秒后才被视为可用，默认为0。                                                                                                                                                  |
| spec.selector.matchLabels[]	             | list    | 	集合式的标签匹配（用来区分是谁的pod），将适合的pod加入到该该控制器下内容是：标签：值（RS支持）                                                                                                                           |
| spec.selector.matchExpressions[]	        | object  | 	支持运算符匹配进行标签选择（用来区分是谁的pod），将适合的pod加入到该该控制器下<br/>内容是：<br/>key： xxxx<br/>operator: 运算符<br/>vlaues：xxxx                                                                           |
| spec.selector.matchExpressions[].key	    | string  | 	标签选择器的key字段                                                                                                                                                                   |
| spec.selector.matchExpressions.operator	 | string	 | 标签选择的运算符，有In、NotIn、Exists、DoesNotExist 四个值可选<br/>In ：label 的值在某个列表中（根据value的值进行选择）<br/>NotIn ：label 的值不在某个列表中<br/>Exists ：某个 label 存在（根据key进行选择）<br/>DoesNotExist：某个 label 不存在 |
| spec.selector.matchExpressions[].values	 | list    | 	标签选择器下的value字段                                                                                                                                                                |

### deployment控制器

| 参数名	                                                   | 字段类型    | 	说明                                                                                                                                                                                                                                                                                                                                                                                                                            |
|--------------------------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spec.minReadySeconds	                                  | init    | 	用定义新建的 Pod 经过多少秒后才被视为可用，默认为0。                                                                                                                                                                                                                                                                                                                                                                                                 |
| spec.selector	                                         | list    | 	默认标签选择器。默认是matchLabels子集形式进行匹配                                                                                                                                                                                                                                                                                                                                                                                                |
| spec.selector.matchLabels[]	                           | list	   | 集合式的标签匹配（用来区分是谁的pod），将适合的pod加入到该该控制器下内容是：标签：值                                                                                                                                                                                                                                                                                                                                                                                  |
| spec.selector.matchExpressions[]	                      | object  | 	支持运算符匹配进行标签选择（用来区分是谁的pod），将适合的pod加入到该该控制器下<br/>内容是：<br/>key： xxxx<br/>operator: 运算符<br/>vlaues：xxxx                                                                                                                                                                                                                                                                                                                           |
| spec.selector.matchExpressions[].key	                  | string	 | 标签选择器的key字段                                                                                                                                                                                                                                                                                                                                                                                                                    |
| spec.selector.matchExpressions.operator                | 	string | 	标签选择的运算符，有In、NotIn、Exists、DoesNotExist 四个值可选<br/>In ：label 的值在某个列表中（根据value的值进行选择）<br/>NotIn ：label 的值不在某个列表中<br/>Exists ：某个 label 存在（根据key进行选择）<br/>DoesNotExist：某个 label 不存在<br/>spec.selector.matchExpressions[].values	list	标签选择器下的value字段<br/>sepc.revisionHistoryLimit	init	设置保留的滚动更新历史版本数量，默认值为2147483647,表示保存所有历史版本<br/>spec.strategy.type	string	部署类型，可以选择 的值为ecreate (字母开头大写) 或 RollingUpdate , 默认为 RollingUpdate 。 |
| Deployment.spec.strategy.Recreate	                     |         | 	滚动更新方式为重建                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Deployment.spec.strategy.rollingUpdate	                | 映射      | 	滚动更新方式为滚动更新                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Deployment.spec.strategy.rollingUpdate.maxSurge	       | init    | 	设置更新时pod最多多几个（或者指定百分比）                                                                                                                                                                                                                                                                                                                                                                                                        |
| Deployment.spec.strategy.rollingUpdate.maxUnavailable	 | init    | 	设置更新时pod最多几个不可用（或者指定百分比）                                                                                                                                                                                                                                                                                                                                                                                                      |
| Deployment.spec.revisonHistoryLimit	                   | init    | 	指定 deployment 最多保留多少 镜像版本修改的历史记录。默认的会保留所有的 revision；如果将该项设置为0，Deployment 就不允许回退了                                                                                                                                                                                                                                                                                                                                              |

### job控制器

| 参数名                               | 	字段类型   | 	说明                                                                                                                                                                                                                                                            |
|-----------------------------------|---------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spec.template                     | 	–	     | 格式同Pod相同                                                                                                                                                                                                                                                       |
| spec.template.spec.restartPolicy	 | String	 | 仅支持Never或OnFailure，需要修改<br/>定义Pod的重启策略，可选值为Always、OnFailure，默认值为Always。<br/>1.Always: Pod一旦终止运行，则无论容器是如何终止的，kubelet服务都将重启它。<br/>2.OnFailure: 只有Pod以非零退出码终止时，kubelet才会重启该容器。如果容器正常结束 (退出码为0) ，则kubelet将不会重启它<br/>3.Never: Pod终止后，kubelet将退出码报告给Master，不会重启该Pod. |
| spec.completions                  | 	init	  | 标志Job结束需要成功运行的Pod个数，默认为1                                                                                                                                                                                                                                       |
| spec.parallelism	                 | init    | 	标志并行运行的Pod的个数，默认为1                                                                                                                                                                                                                                            |
| spec.activeDeadlineSeconds        | 	init   | 	标志失败Pod的重试最大时间，超过这个时间不会继续重试。秒为单位                                                                                                                                                                                                                              |

### CronJob控制器

| 参数名	                             | 字段类型	   | 说明                                                                                                                                                                                                                                                                                                                                                                                                           |
|----------------------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .spec.schedule	                  | string	 | 调度，必需字段，指定任务运行周期，格式同 Cron。如：“*/1 * * * *”                                                                                                                                                                                                                                                                                                                                                                    |
| spec.completions	                | init	   | 标志Job结束需要成功运行的Pod个数，默认为1                                                                                                                                                                                                                                                                                                                                                                                     |
| spec.parallelism	                | init    | 	标志并行运行的Pod的个数，默认为1                                                                                                                                                                                                                                                                                                                                                                                          |
| spec.concurrencyPolicy           | 	string | 	-并发策略，该字段也是可选的。它指定了如何处理被 Cron Job 创建的 Job 的并发执行。只允许指定下面策略中的一种：<br/>Allow（默认）：允许并发运行 Job<br/>Forbid：禁止并发运行，如果前一个还没有完成，则直接跳过下一个<br/>Replace：取消当前正在运行的 Job，用一个新的来替换                                                                                                                                                                                                                                            |
| spec.suspend	                    | string  | 	挂起，该字段也是可选的。有true、false两个值可以选择。如果设置为 true，后续所有执行都会被挂起。它对已经开始执行的 Job 不起作用。默认值为 false                                                                                                                                                                                                                                                                                                                         |
| spec.successfulJobsHistoryLimit	 | init    | 	历史限制。是可选的字段。它们指定了可以保留多少完成的 Job。默认为3                                                                                                                                                                                                                                                                                                                                                                         |
| spec.failedJobsHistoryLimit	     | init	   | 历史限制。是可选的字段。它们指定了可以保留多少失败的 Job。默认为0                                                                                                                                                                                                                                                                                                                                                                          |
| spec.activeDeadlineSeconds       | 	init   | 	标志失败Pod的重试最大时间，超过这个时间不会继续重试。该字段是可选的。如果因为任何原因而错过了被调度的时间，那么错过执行时间的 Job 将被认为是失败。秒为单位                                                                                                                                                                                                                                                                                                                           |
| spec.jobTemplate	                | object  | 	的。如果没有指定，则没有期限Job 模板，必需字段，指定需要运行的任务，格式同 Job<br/>jobTemplate.spec.template	–	格式同Pod相同<br/>jobTemplate.spec.template.spec.restartPolicy	String	仅支持Never或OnFailure，需要修改<br/>定义Pod的重启策略，可选值为Always、OnFailure，默认值为Always。<br/>1.Always: Pod一旦终止运行，则无论容器是如何终止的，kubelet服务都将重启它。<br/>2.OnFailure: 只有Pod以非零退出码终止时，kubelet才会重启该容器。如果容器正常结束 (退出码为0) ，则kubelet将不会重启它<br/>3.Never: Pod终止后，kubelet将退出码报告给Master，不会重启该Pod. |

### service

| 参数名	                                   | 字段类型   | 	是否必选	                                                                                      | 取值说明 |
|----------------------------------------|--------|---------------------------------------------------------------------------------------------|------|
| .apiVersion	                           | string | 	Required	v1                                                                                |
| .kind	                                 | string | 	Required	Service                                                                           |
| .metadata	                             | object | 	Required	元数据                                                                               |
| .metadata.name	                        | string | 	Required	Service名称，需符合RFC 1035规范                                                           |
| .metadata.namespace	                   | string | 	Required	命名空间，不指定系统时将使用名为default的命名空间                                                      |
| .metadata.labels	[]                    | list   | 		自定义标签属性列表                                                                                 |
| .metadata.annotation[]	                | list   | 		自定义注解属性列表                                                                                 |
| .spec	                                 | object | 	Required	期望描述                                                                              |
| .spec.selector[]	                      | list   | 	Required	Label Selector配置，将选择具有指定Label标签的Pod作为管理范围                                         |
| .spec.type	                            | string | 	Requited	Service的类型，指定Service的防问方式，默认值为ClusterIP。取值可有：ClusterIP、NodePort、LoadBalancer。     |
| .spec.clusterIP	                       | string | 		虚拟服务的IP地址，当type=ClusterIP时，如果不指定，则系统自动分配，也可以手工分配；当type=LoadBalancer时，需要指定                 |
| .spec.sessionAffinity	                 | string | 		会话保持字段是否支持Session,可选值为ClientIP,默认值为None。ClientIP:表示将同一个客户端（根据客户端的IP地址决定）的访问请求都转发到同一个后端Pod |
| .spec.ports[]	                         | list   | 		Service端口列表                                                                               |
| .spec.ports[].name	                    | string | 		端口名称                                                                                      |
| .spec.ports[].protocol	                | string | 		端口协议，支持TCP和UDP，默认值为TCP                                                                    |
| .spec.ports[].port	                    | int	   | 	服务监听的端口号                                                                                   |
| .spec.ports[].targetPort               | 	int	  | 	需要转发到后端Pod的端口号                                                                             |
| .spec.ports[].nodePort	                | int	   | 	当spec.type=NodePort时，指定映射到宿主机的端口号                                                          |
| .status	                               | object | 		当spec.type=LoadBalancer时，设置外部负载均衡器的地址                                                     |
| .status.loadBalancer	                  | object | 		外部负载均衡器                                                                                   |
| .status.loadBalancer.ingress	          | object | 		外部负载均衡器                                                                                   |
| .status.loadBalancer.ingress.ip	       | string | 		外部负载均衡器的IP地址                                                                              |
| .status.loadBalancer.ingress.hostname	 | string | 		外部负载均衡器的主机名                                                                               |

### Endpoint

> 作用：连接外部服务值本地svc
> 原理：只有配置了 selector 的 service 才会自动创建一个同名的 endpoints，没有配置 selector 的 service 不会产生 endpoints
> 资源对象

| 参数名	             | 字段类型    | 	说明                              |
|------------------|---------|----------------------------------|
| metadata.name	   | string	 | 必需字段，必须与要匹配的svc名字完全一致，并且在同一名字空间中 |
| svc.subsets	     | object  | 	#配置外部集群信息（一级字段）                 |
| subsets.address	 | list	   | 外部地址                             |
| subsets.ports	   | list	   | 外部端口                             |

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  ports:
    - protocol: TCP
      port: 6666
      targetPort: 80
---
apiVersion: v1
kind: Endpoints
metadata:
  name: nginx     #名字必须与svc名字完全一致，并且在同一名字空间中
subsets: #配置外部集群信息
  - addresses: #外部地址
      - ip: 192.168.110.102
    #      - ip: xxxxxx   跟踪多个 ip (多个 pod 或者容器或者应用)，可以使用以下方式
    ports:
      - port: 80  #外部端口
```

### ingress

> Ingress（入口，准入）是Kubernetes提供的接口，Kubernetes使用Ingress策略定义和Ingress Controller
> 实现了基于Ingress策略定义的服务路由功能，用于对Kubernetes集群外部的客户端提供服务。  
> Ingress 专门对接用于七层负载的

| 参数名                                             | 	字段类型    | 	说明         |
|-------------------------------------------------|----------|-------------|
| Ingress.spec.rules	                             | list     | 	规则设置       |
| spec.rules.host	                                | string	  | 代理到的域名      |
| spec.rules.http	                                | object   | 	基于http协议   |
| spec.rules.http.path	                           | list	    | 访问的网址路径     |
| spec.rules.http.path.paths	                     | string   | 	访问的网址路径是哪个 |
| spec.rules.http.path.paths.backend	             | object   | 	代理设置       |
| spec.rules.http.path.paths.backend.serviceName	 | string   | 	代理到哪个svc   |
| spec.rules.http.path.paths.backend.servicePort  | 	string	 | 代理的端口       |

### configmap

> 创建方式：使用命令创建，在命令中

下方是如何使用：

#### 在资源清单中添加环境变量

| 字段	                           | 字段类型    | 	描述                                                                                                                 |
|-------------------------------|---------|---------------------------------------------------------------------------------------------------------------------|
| pod.spec.containers.env	      | list	   | 要在容器中设置的环境变量列表。 无法更新。 EnvVar 表示容器中存在的环境变量。                                                                          |
| pod.spec.containers.env.name	 | string  | 	环境变量的Key值，必须设置。                                                                                                    |
| pod.spec.containers.env.value | string	 | 设置环境变量的Value，可以使用 `$(VAR_NAME)` 的方式对容器中的环境变量进行引用。 `$(VAR_NAME)` 表示对 `$` 进行转义，会将`$(VAR_NAME)` 以值的方式设置为环境变量的值而不是将其展开。 |

#### 通过ConfigMap逐个添加env（变量名容器内二次定义）

> key=容器定义。value=configmap中的key的value

| 字段	                                                         | 字段类型    | 	描述                                          |
|-------------------------------------------------------------|---------|----------------------------------------------|
| pod.spec.containers.env                                     | 	list   | 要在容器中设置的环境变量列表。 无法更新。 EnvVar 表示容器中存在的环境变量。   |
| pod.spec.containers.env.valueFrom	                          | object	 | 环境变量值的源。如果值不为空，则无法使用。                        |
| pod.spec.containers.env.valueFrom.configMapKeyRef	          | object  | 	选择configMap中的一个Key值的字段。                     |
| pod.spec.contaienrs.env.valueFrom.configMapKeyRef.name	     | string  | 	指定configMap的名字                              |
| pod.spec.containers.env.valueFrom.configMapKeyRef.key       | string  | 	指定configMap引用的key值，会将对应的value赋予环境变量的value值。 |
| pod.spec.contianers.env.valueFrom.configMapKeyRef.optional	 | boolean | 	指定configMap和key值是否必需预先定义好逐个添加env（没学，不用看）    |

#### 通过ConfigMap批量添加env（变量名无法二次定义）

> key=configmap中的key。value=configmap中的key的value

| 字段                                            | 字段类型    | 描述                                                                                              |
|-----------------------------------------------|---------|-------------------------------------------------------------------------------------------------|
| pod.spec.contianers.envFrom                   | list    | 用于在容器中填充 环境变量的源列表 ，格式必须为C_IDENTIFIER(C语言标识符格式标准) 。如果Key值无效则报告为事件，如果key重复，则以最后的为准，环境变量注入 无法被更新 。 |
| pod.spec.containers.envFrom.configMapRef      | Object  | 选择ConfigMap，注入环境变量                                                                              |
| pod.spec.containers.envFrom.configMapRef.name | string  | ConfigMap的名称                                                                                    |
| pod.spec.containers.optional                  | boolean | （没学，不用看）指定configMap是否需要被预先定义好                                                                   |

#### 通过数据卷插件使用configmap

> 将文件填入数据卷。key就是文件名，value就是文件内容

| 字段	                                        | 字段类型	    | 描述             |
|--------------------------------------------|----------|----------------|
| pod.sepc.containers.volumeMounts	          | list	    | 挂载一个卷          |
| pod.sepc.containers.volumeMounts.name	     | string   | 	挂载的卷名         |
| pod.sepc.containers.volumeMounts.mountPath | 	srting	 | 挂载到哪个目录下       |
| pod.sepc.volumes                           | 	list    | 	定义卷           |
| pod.sepc.volumes.name                      | 	string	 | 卷名             |
| pod.sepc.volumes.name.configMap	           | object   | 	卷来源是configmap |
| pod.sepc.volumes.name.configMap.name	      | string	  | configmap的名字   |

#### configmap的热更新（修改 pod annotations 的方式）

> 更新 ConfigMap 目前并不会触发相关 Pod 的滚动更新，可以通过修改 pod annotations 的方式强制触发滚动更新 这个例子里我们在
`.spec.template.metadata.annotations` 中添加 `version/config`，每次通过修改 `version/config`的值 来触发滚动更新

```shell
$ kubectl patch deployment nginx-test --patch '{"spec": {"template": {"metadata": {"annotations": {"version/config": "20190411" }}}}}'
# 在其中添加此字段即可滚动更新，修改也可以触发滚动更新
```

* 使用该 ConfigMap 挂载的 Env 不会同步更新
* 使用该 ConfigMap 挂载的 Volume 中的数据需要一段时间（实测大概10秒）才能同步更新<br/>
  ⚠️ 注意：apps/v1版本的api接口，默认滚动幅度为25%

### secret

#### service Account

用来访问 Kubernetes API，由 Kubernetes 自动创建，并且会自动挂载到 Pod 的 `/run/secrets/kubernetes.io/serviceaccount`
目录中。专门用于集群安全中pod的认证。

#### Opaque Secret

类似于 configmap，但是 value 字段是编码的value 数据必须是原数据经过 base64 编码以后的结果，secret 在使用的时候，value
会被自动解码

| 字段          | 字段类型   | 描述                                                                |
|-------------|--------|-------------------------------------------------------------------|
| secret.type | string | secret类型。有三种类型、service Account、opaque 、dockerconfigjson。本处写opaque |
| secret.data | list   | 数据段。格式：key=value；value必须是过 base64 编码以后的结果                         |

* `kubernetes.io/dockerconfigjson`
  使用 Kuberctl 命令创建 docker 仓库认证的 secret，命令中有写到。资源清单不在做描述

### volume

#### emptyDir（空目录）

> * 当 Pod 被分配给节点时，首先创建 emptyDir 卷，并且只要该 Pod 在该节点上运行，该卷就会存在。正如卷的名字所述，它最初是空的。Pod
    中的容器可以读取和写入 emptyDir 卷中的相同文件，尽管该卷可以挂载到每个容器中的相同或不同路径上。当出于任何原因从节点中删除
    Pod 时，emptyDir 中的数据将被永久删除
> * 用法：pod的暂存空间。（存放initC执行结果、容器运行产生元数据-mfsmaster的元数据日志）

⚠️注意：emptyDir跟pod生命周期一致。容器崩溃不会从节点中移除 pod，因此 emptyDir 卷中的数据在容器崩溃时是安全的

| 字段                                         | 字段类型   | 描述                                       |
|--------------------------------------------|--------|------------------------------------------|
| pod.spec.volumes                           | list   | 定义卷                                      |
| pod.spec.volumes.name                      | string | 卷名                                       |
| pod.spec.volumes.emptyDir                  | string | 卷类型是emptydir。value值为{ }<br/>emptyDir: {} |
| pod.sepc.containers.volumeMounts           | list   | 挂载一个卷                                    |
| pod.sepc.containers.volumeMounts.name      | string | 挂载的卷名                                    |
| pod.sepc.containers.volumeMounts.mountPath | srting | 挂载到哪个目录下                                 |

#### hostPath

> hostPath卷将主机节点的文件系统中的文件或目录挂载到集群中，可对主机文件/目录进行判断，判断是否存在，存在是否合理，并做出动作

| 字段	                                         | 字段类型	    | 描述                             |
|---------------------------------------------|----------|--------------------------------|
| pod.sepc.containers.volumeMounts	           | list     | 	挂载一个卷                         |
| pod.sepc.containers.volumeMounts.name	      | string   | 	挂载的卷名                         |
| pod.sepc.containers.volumeMounts.mountPath	 | string   | 	挂载到哪个目录下                      |
| pod.spec.volumes	                           | list     | 	定义卷                           |
| pod.spec.volumes.name	                      | string   | 	卷名                            |
| pod.spec.volumes.hostPath	                  | object   | 	卷类型为hostpath                  |
| pod.spec.volumes.hostPath.path	             | string   | 	卷的物理机路径（主机上的目录位置）             |
| pod.spec.volumes.hostPath.type              | 	string	 | key值填hostPath卷的类型。有八个值可选，下方详解。 |

* hostPath卷类型的vlaue值

| 值	                 | 行为                                                                                                                      |
|--------------------|-------------------------------------------------------------------------------------------------------------------------|
|                    | 空字符串（默认）用于向后兼容，这意味着在挂载 hostPath 卷之前不会执行任何检查。（不检查直接使用）                                                                   |
| DirectoryOrCreate	 | 如果在给定的路径上没有任何东西存在，那么将根据需要在那里创建一个空目录，权限设置为 0755，与 Kubelet 具有相同的组和所有权**（就是节点所在的Kubelet创建的）。（如果目录不存在则创建一个空目录，存在的是个文件则报错）** |
| Directory	         | 给定的路径下必须存在目录，不存在则报错                                                                                                     |
| FileOrCreate       | 	如果在给定的路径上没有任何东西存在，那么会根据需要创建一个空文件，权限设置为 0644，与 Kubelet 具有相同的组和所有权。（如果文件不存在则创建空文件，存在的是个目录则报错）                            |
| File	              | 给定的路径下必须存在文件，否则报错。                                                                                                      |
| Socket	            | 给定的路径下必须存在 UNIX 套接字，否则报错。                                                                                               |
| CharDevice         | 	给定的路径下必须存在字符设备，否则报错。                                                                                                   |
| BlockDevice        | 	给定的路径下必须存在块设备，否则报错。                                                                                                    |

### PVC持久化卷

#### PV

> 类别是：PersistentVolume  
> PV 也是集群中的资源。 PV 是 Volume 之类的卷插件，但具有独立于使用 PV 的 Pod 的生命周期。可被PVC调用提供给pod

| 字段	                                 | 字段类型	   | 描述                                                                                                                                 |
|-------------------------------------|---------|------------------------------------------------------------------------------------------------------------------------------------|
| spec.capacity	                      | object  | 	pv容量                                                                                                                              |
| spec.capacity.storage	              | string  | 	pv的大小。5Gi表示5GB                                                                                                                    |
| spec.volumeMode	                    | string  | 	卷模式。写Filesystem即可                                                                                                                 |
| spec.accessModes	                   | string  | 	访问模式（读写策略）。三个值可选 ReadWriteOnce——该卷可以被单个节点以读/写模式挂载ReadOnlyMany——该卷可以被多个节点以只读模式挂载ReadWriteMany——该卷可以被多个节点以读/写模式挂载                   |
| spec.persistentVolumeReclaimPolicy	 | string  | 	卷的回收策略，三个值可选<br/>Retain（保留）——手动回收（基本都用这个）Recycle（回收）——基本擦除（rm -rf /thevolume/*）<br/>Delete（删除）——关联的存储资产，用于动态pv。此策略会将卷直接删除----节省成本 |
| sepc.storageClassName	              | string  | 	定义存储类的类名（是自定义的，严格匹配）                                                                                                              |
| sepc.nfs	                           | object  | 	通过nfs提供底层数据存储                                                                                                                     |
| sepc.nfs.path	                      | string	 | nfs共享的路径                                                                                                                           |
| sepc.nfs.server	                    | string  | 	nfs的地址                                                                                                                            |

* Retain（保留）——手动回收（基本都用这个）
* Recycle（回收）——基本擦除（rm -rf /thevolume/*）
* Delete（删除）——关联的存储资产（例如 AWS EBS、GCE PD、Azure Disk 和 OpenStack Cinder 卷）将被删除----节省成本

#### PVC

> `pvc`是用户存储的请求。它与Pod相似。Pod消耗节点资源，PVC消耗PV资源。Pod可以请求特定级别的资源（CPU和内存）。声明可以请求特定的大小和访问模式（例如，可以以读/写一次或只读多次模式挂载）
> 声明需要在StatefulSet控制器中进行声明  
> `StatefulSet`是为了 解决有状态服务的问题 （对应 Deployments 和 ReplicaSets 是为无状态服务而设计），需要配合无头服务Service使用

* 其特性包括：
    * 稳定的存储：即 Pod 重新调度后还是能访问到相同的持久化数据，基于 PVC 来实现。  
      `PV卷的名称为：PodName+PV模板Name。`

* 稳定的网络标识：即 Pod 重新调度后其 PodName 和 HostName（域名）不变，基于 Headless Service（即没有 Cluster IP 的 Service
  ）来实现。

* 有序扩容：有序扩展，即 Pod 是有顺序的，在部署或者扩展的时候要依据定义的顺序依次依次进行  
  `即从 0 到 N-1，在 下一个 Pod 运行之前所有之前的 Pod 必须都是 Running 和 Ready 状态 ，基于 init containers 来实现。 只有当第一个pod就绪以后才会创建第二个pod`

* 有序的回收：有序删除（即从 N-1 到 0）

* Service具有以下特点：

  > * 没有ClusterIP地址
  > * kube-proxy不会创建负载均衡转发规则
  > * Service是否有DNS域名解析取决于还Headless Service是否设置了Label Selector
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      ports:
      - port: 80
        name: web
      clusterIP: None     #无头svc
      selector:
        app: nginx
    ---
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"    #必须填写无头服务svc的名字
      replicas: 3
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: wangyanglinux/myapp:v1
            ports:
            - containerPort: 80
              name: web
            volumeMounts:  #卷绑定
            - name: www     
              mountPath: /usr/share/nginx/html
      volumeClaimTemplates:    #pvc的卷声明模板（一个pod对应一个pvc，跟随pod一起创建）
      - metadata:
          name: www
        spec:
          accessModes: [ "ReadWriteOnce" ]     #访问策略
          storageClassName: "nfs"      #使用存储类
          resources:
            requests:
              storage: 1Gi    #请求资源是1G  回收策略可不写
    ```

### 调度器

#### 节点亲和性

* 匹配逻辑
  > 匹配的是节点的标签。只要node符合该标签即为匹配成功

| 字段	                                                                             | 字段类型	    | 描述                                                                                                                                                                         |
|---------------------------------------------------------------------------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| pod.spec.schedulername	                                                         | string   | 	指定使用调度器（自定义调度器的使用）                                                                                                                                                        |
| pod.spec.affinity	                                                              | object   | 	pod的亲和性设置                                                                                                                                                                 |
| pod.spec.affinity.nodeAffinity	                                                 | object   | 	节点亲和性设置（硬亲和跟软亲和可在一起）                                                                                                                                                      |
| pod.spec.affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution   | 	object  | 	节点硬亲和                                                                                                                                                                     |
| 硬亲和.nodeSelectorTerms	                                                          | list     | 	节点标签选择器                                                                                                                                                                   |
| 硬亲和.nodeSelectorTerms.matchExpressions	                                         | 	        | 匹配运算符。下面有多个选项的话，满足任何一个条件就可以了                                                                                                                                               |
| 硬亲和.nodeSelectorTerms.matchExpressions.matchExpressions                         | 	list    | 	匹配运算符                                                                                                                                                                     |
| 硬亲和.nodeSelectorTerms.matchExpressions.matchExpressions.key	                    | string	  | 匹配节点标签的key                                                                                                                                                                 |
| 硬亲和.nodeSelectorTerms.matchExpressions.matchExpressions.operator                | 	string	 | 使用哪个运算符<br/>In：label 的值在某个列表中<br/>NotIn：label 的值不在某个列表中<br/>Gt：label 的值大于某个值<br/>Lt：label 的值小于某个值<br/>Exists：某个 label 存在（key是否存在）<br/>DoesNotExist：某个 label 不存在（key是否不存在）  |
| 硬亲和.nodeSelectorTerms.matchExpressions.matchExpressions.values	                 | list     | 	列表内写匹配的node标签值                                                                                                                                                            |
| pod.spec.affinity.nodeAffinity.preferredDuringSchedulingIgnoredDuringExecution	 | object   | 	节点软亲和                                                                                                                                                                     |
| 软亲和.weight	                                                                     | string   | 	软亲和权重范围在 1-100 之间（多个软亲和若产生冲突，选择权重高的）                                                                                                                                      |
| 软亲和.preference	                                                                 | object   | 	节点选择器条目，与权重（weight）相关                                                                                                                                                     |
| 软亲和.preference.matchExpressions                                                 | 	list    | 	节点选择器条目，与权重（weight）相关                                                                                                                                                     |
| 软亲和.preference.matchExpressions	                                                | list     | 	匹配运算符有多个选项的话，则必须同时满足这些条件才能正常调度                                                                                                                                            |
| 软亲和.preference.matchExpressions.matchExpressions.key	                           | string   | 	匹配节点标签的key                                                                                                                                                                |
| 软亲和.preference.matchExpressions…matchExpressions.operator	                      | string   | 	使用哪个运算符<br/>In：label 的值在某个列表中<br/>NotIn：label 的值不在某个列表中<br/>Gt：label 的值大于某个值<br/>Lt：label 的值小于某个值<br/>Exists：某个 label 存在（key是否存在）<br/>DoesNotExist：某个 label 不存在（key是否不存在） |
| 软亲和.preference.matchExpressions.matchExpressions.values	                        | list	    | 列表内写匹配的node标签值                                                                                                                                                             |

#### pod亲和性

* 匹配逻辑
  > pod亲和性匹配的是拓扑域，亲和的是拓扑域，反亲和的也是拓扑域。决定跟不跟pod在同一个拓扑域

* 首先在整个集群中寻找标签符合匹配条件的pod
* 找到该pod后，根据设置的拓扑域的key匹配其拓扑域的value
* 只要在设置的拓扑域的value与符合标签的pod的拓扑域的value一致，就可以建立pod

> 根据拓扑域匹配新的pod该建立在哪里，只要与上面符合条件的pod在同拓扑域即可。  
> 拓扑域：在同一层次就属于同一拓扑域。例如：同一主机、同一机房、同一地区等等  
> pod亲和性不支持Gt、Lt运算符。

| 字段	                                                     | 字段类型   | 	描述        |
|---------------------------------------------------------|--------|------------|
| pod.spec.affinity	                                      | object | 	pod的亲和性设置 |
| pod.spec.affinity.podAffinity	                          | object | 	pod亲和性设置  |
| pod.spec.affinity.podAntiAffinity	                      | object | 	pod反亲和性设置 |
| pod亲和性.requiredDuringSchedulingIgnoredDuringExecution	  | list   | 	pod硬亲和策略  |
| pod亲和性.preferredDuringSchedulingIgnoredDuringExecution	 | list   | 	pod软亲和策略  |

> 下方字段通过node亲和性一致

### 亲和性相关运算符

> 键值运算关系

* In：label 的值在某个列表中
* NotIn：label 的值不在某个列表中
* Gt：label 的值大于某个值–pod不支持
* Lt：label 的值小于某个值–pod不支持
* Exists：某个 label 存在（key是否存在）
* DoesNotExist：某个 label 不存在（key是否不存在

| 调度策略	            | 匹配标签	 | 操作符	                                     | 拓扑域支持 | 	调度目标             |
|------------------|-------|------------------------------------------|-------|-------------------|
| nodeAffinity	    | 主机	   | In, NotIn, Exists, DoesNotExist, Gt, Lt	 | 否     | 	指定主机             |
| podAffinity	     | POD   | 	In, NotIn, Exists, DoesNotExist	        | 是     | POD与指定POD同一拓扑域    |
| podAnitAffinity	 | POD   | 	In, NotIn, Exists, DoesNotExist	        | 是     | 	POD与指定POD不在同一拓扑域 |

* 节点亲和性的前提条件是：
    * 在指定拓扑域内，有目标Pod（设置为flag的Pod）。
    * 如果是硬限制，则目标Pod的标签与matchExpressions中的条件（一个或多个）必须完全匹配。
    * 如果是软限制，则目标Pod的标签，不是必须的，如果相匹配，还要考虑weight（1~100）关系。

#### 固定节点调度

| 字段	                    | 字段类型    | 	描述                                                                 |
|------------------------|---------|---------------------------------------------------------------------|
| Pod.spec.nodeName	     | string  | 	固定节点调度，后边跟节点名，可以跳过污点                                               |
| Pod.spec.nodeSelector	 | string	 | 匹配节点标签进行调度，不会跳过污点。 <br/>类似于节点硬亲和，只会在符合匹配的节点上建立。节点不让建立则pending（等待调度） |

### 污点与容忍

#### 污点

```shell
# 设置污点
$ kubectl taint node 节点名 key1=value:NoSchedule

# 节点说明中，查找 Taints 字段
$ kubectl describe node  节点名  

# 去除污点
$ kubectl taint node 节点名  key=value:NoSchedule-   #加个  -  号
```

#### 容忍字段

| 字段                             | 	字段类型	  | 描述                          |
|--------------------------------|---------|-----------------------------|
| pod.spec.tolerations	          | list    | 	容忍设置                       |
| pod.spec.tolerations.key	      | string  | 	污点的key                     |
| pod.spec.tolerations.operator	 | string  | 	污点运算符 <br/>“Equal” #运算符是等于 |
| pod.spec.tolerations.value     | 	srting | 	污点的value                   |
| pod.spec.tolerations.effect	   | string  | 	污点的effect（效果）              |

### RBAC

#### Role and ClusterRole

> 在 RBAC API 中，Role 表示一组规则权限，权限只会增加(累加权限)，不存在一个资源一开始就有很多权限而通过 RBAC 对其进行减少的操作；Role
> 可以定义在一个 namespace 中，如果想要跨 namespace 则可以创建 ClusterRole

* Role资源清单

  ```yaml
  kind: Role
  apiVersion: rbac.authorization.k8s.io/v1beta1
  metadata:
    namespace: default   #必须写，不写就会有个默认值是default
    name: pod-reader
  rules:
    - apiGroups: [ "" ] # "" indicates the core API group    #对哪个接口组版本的资源进行操作，不写就代表核心组：v1 
      resources: [ "pods" ]     #可进行操作的资源对象
      verbs: [ "get", "watch", "list" ]  #对上方资源的操作，一般写all即可，不同资源有不同操作，在开发者手册中有。get：获取这个pod。watch：监控。lsit：列出，加起来就是查看pod的信息
  ```

* ClusterRole资源清单

  > ClusterRole 具有与 Role 相同的权限角色控制能力，不同的是 ClusterRole 是集群级别的，ClusterRole 可以用于:
  > * 集群级别的资源控制( 例如 node 访问权限 )
  > * 非资源型 endpoints( 例如 /health 访问 )
  > * 所有命名空间资源控制(例如 pods )

    ```yaml
    kind: ClusterRole
    apiVersion: rbac.authorization.k8s.io/v1beta1
    metadata:
    # "namespace" omitted since ClusterRoles are not namespaced不能写名字空间
    name: secret-reader
    rules:
      - apiGroups: [ "" ]
    resources: [ "secrets" ]
    verbs: [ "get", "watch", "list" ]
    ```
  ⚠️ 集群角色不能写名字空间

#### RoleBinding and ClusterRoleBinding

> RoleBinding and ClusterRoleBinding是什么？  
> RoloBinding可以将角色中定义的权限授予用户或用户组，RoleBinding 包含一组权限列表(subjects)
> 权限列表中包含有不同形式的待授予权限资源类型(users, groups, or service accounts)；RoloBinding同样包含对被Bind的Role
> 引用；RoleBinding 适用于某个命名空间内授权，而 ClusterRoleBinding 适用于集群范围内的授权

* RoleBinding绑定Role

  > 角色通过角色绑定，绑定给用户（user/group/sa）  
  > 例如：将 default 命名空间的 pod-reader Role 授予 jane 用户，此后 jane 用户在 default 命名空间中将具有 pod-reader 的权限

  ```yaml
  kind: RoleBinding
  apiVersion: rbac.authorization.k8s.io/v1beta1
  metadata:
    name: read-pods
    namespace: default      #角色所在的名字空间
  subjects: #绑定给说
    - kind: User        #绑定给用户
      name: jane        #jane用户
      apiGroup: rbac.authorization.k8s.io      #接口组版本
  roleRef: #角色来源
    kind: Role       #来源于角色
    name: pod-reader   #来源哪个角色用户
    apiGroup: rbac.authorization.k8s.io
  ```

* RoleBinding绑定ClusterRole
  > * 集群角色通过集群角色绑定，绑定给用户（user/group/sa）
  > * 同样可以引用ClusterRole来对当前namespace内用户、用户组或ServiceAccount进行授权，这种操作允许集群管理员在整个集群内定义一些通用的ClusterRole，然后在不同的namespace中使用
      `RoleBinding`来引用
  > * 例如：以下 `RoleBinding` 引用了一个 ClusterRole，这个 ClusterRole 具有整个集群内对 secrets 的访问权限；但是其授权用户dave只能访问
      development 空间中的 secrets(因为 `RoleBinding` 定义在 development 命名空间)

  ```yaml
  # This role binding allows "dave" to read secrets in the "development" namespace.
  #这个角色绑定允许“dave”读取“dev”命名空间中的信息。
  kind: RoleBinding          #资源类型
  apiVersion: rbac.authorization.k8s.io/v1beta1
  metadata:
      name: read-secrets
      namespace: dev # 这只授予“dev”名称空间内的权限。必写
  subjects:
  - kind: User
    name: dave
    apiGroup: rbac.authorization.k8s.io
  roleRef:
    kind: ClusterRole      #角色来源于集群角色
    name: secret-reader
    apiGroup: rbac.authorization.k8s.io
  ```
* ClusterRoleBinding绑定ClusterRole

  > * 集群角色通过角色绑定，绑定给用户（user/group/sa）。将集群角色权限限制在角色名字空间下
  > * 使用ClusterRoleBinding可以对整个集群中的所有命名空间资源权限进行授权；以下ClusterRoleBinding样例展示了授权manager组内所有用户在全部命名空间中对secrets进行访问

    ```yaml
    # This cluster role binding allows anyone in the "manager" group to read secrets in any namespace.
    #此集群角色绑定允许“manager”组中的任何人读取任何名称空间中的秘密。
    kind: ClusterRoleBinding
    apiVersion: rbac.authorization.k8s.io/v1beta1
    metadata:
      name: read-secrets-global
    subjects:
    - kind: Group
      name: manager
      apiGroup: rbac.authorization.k8s.io
    roleRef:
      kind: ClusterRole
      name: secret-reader
      apiGroup: rbac.authorization.k8s.io
    ```

#### Resources（子资源）

> Kubernetes 集群内一些资源一般以其名称字符串来表示，这些字符串一般会在 API 的 URL 地址中出现；同时某些资源也会包含子资源，例如
> logs 资源就属于 pods 的子资源，API 中 URL 样例如下

  ```text
  GET /api/v1/namespaces/{namespace}/pods/{name}/log
  /api/v1/namespaces/default/pods/pod-demo/log
  ```

> 例如：如果要在 RBAC 授权模型中控制这些子资源的访问权限，可以通过 / 分隔符来实现，以下是一个定义 pods 资资源 logs 访问权限的
> Role 定义样例

  ```yaml
  kind: Role
  apiVersion: rbac.authorization.k8s.io/v1beta1
  metadata:
    namespace: default
    name: pod-and-pod-logs-reader
  rules:
    - apiGroups: [ "" ]
      resources: [ "pods", "pods/log" ]  #这里添加了两个权限。pods权限，pods/log权限。pods权限包含pods/log权限，这里写pods/log就是给看看写的格式。
      verbs: [ "get", "list" ]
  ```

#### to Subjects

> 权利附着点：user、group、sa  
> 权利附着点命名方式  
> RoleBinding 和 ClusterRoleBinding 可以将 Role 绑定到 Subjects；Subjects 可以是 groups、users 或者 service accounts

* Subjects 中 Users 使用字符串表示，
    * 它可以是一个普通的名字字符串，如 “alice”；
    * 也可以是 email 格式的邮箱地址，如 “xxx@163.com”；
    * 甚至是一组字符串形式的数字 ID 。
    * 但是 Users 的前缀 system: 是系统保留的，集群管理员应该确保普通用户不会使用这个前缀格式
* Groups 书写格式与 Users 相同，都为一个字符串，并且没有特定的格式要求；同样 system: 前缀为系统保留

### 资源限制

| 参数	                                        | 含义                                                |
|--------------------------------------------|---------------------------------------------------|
| spec.container[].resources.requests.cpu    | 	容器初始要求的CPU数量（Pod运行可能不需要要求的CPU，但必须分配，不能被其他Pod使用）。 |
| spec.container[].resources.limits.cpu      | 	容器所能使用的最大CPU数量（单位：500m=0.5）。                     |
| spec.container[].resources.requests.memory | 	容器初始要求的内存数量(可以不用，必须分配，不能被其他Pod使用)                |
| spec.container[].resources.requests.memory | 	容器能使用的最大内存数量（单位：Ki,Mi,Gi,Ti,Pi,Ei）               |

| 字段	                                         | 描述                                                                       |
|---------------------------------------------|--------------------------------------------------------------------------|
| .kind LimitRange	                           | （指定资源限制对象类型）,注意,需要在metadata中指定命名空间。                                      |
| .spec.limits[].default                      | 	设置默认的CPU和memory                                                         |
| .spec.limits[].defaultRequest	              | 设置默认的请求值                                                                 |
| .spec.limits[].max                          | 设置Pod可以请求的（在Pod中设置的）最大资源限制                                               |
| .spec.limits[].min                          | 设置Pod可以请求的（在Pod中设置的）最小资源限制 量（单位：500m=0.5）。                               |
| .spec.container[].resources.requests.memory | 容器初始要求的内存数量(可以不用，必须分配，不能被其他Pod使用)<br/>容器能使用的最大内存数量（单位：Ki,Mi,Gi,Ti,Pi,Ei） |
| .spec.limits[].maxLimitRequestRatio	        | 设置默认超售值，Pod可以使用可以超过的的最大资源限制。                                             |


---

> 作者: Leon  
> URL: https://blog.liangml.dpdns.org/posts/kubernetes/  

