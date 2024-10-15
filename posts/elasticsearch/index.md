# Elasticsearch

# elasticsearch伸缩容
## 水平扩容
&gt; 将现有集群中的一个节点镜像到一个新机器(主要是相关配置 插件都使用已有配置，避免再重新安装插件修改设置等)
### 更改 elasticsearch.yml 配置
```shell
# new add config
node.master: false
node.data: true
# modify config
node.name: new_instance_name
network.host: real_intranet_address
```
### 清空数据目录
* 方案一：清空数据目录
```shell
# default path.data /var/lib/elasticsearch
# exec shell
rm -rf /var/lib/elasticsearch/*;
```
* 方案二：更改数据目录：```path.data```
```shell
# new add config
path.data: new_stroage_system_path
# shell exec
chown elasticsearch.elasticsearch /data/application/elasticsearch/
```
### start services
```shell
/etc/init.d/elasticsearch start
```
* 查看集群状态：http://ip:9200/_cat/health?v
* 查看分片迁移进度：http://ip:9200/#/overview?host=Pd_elasticsearch
* 查看恢复进度：http://ip:9200/_cat/recovery?v
* 待平衡完成后 修改索引对应的副本数
```shell
curl -XPUT &#34;&lt;http://ip:9200/INDEXNAME/_settings&gt;&#34; -d &#39;{
 &#34;index&#34; : {
    &#34;number_of_replicas&#34; : 3
  }
}&#39;
```
* http://ip:9200/INDEXNAME/list/_search?pretty&amp;from=0&amp;size=1 

## 缩容操作
* 服务使用的es集群中应先剔除要下线的节点
* 恢复成之前的副本数量
```shell
curl -XPUT &#34;&lt;http://ip:9200/INDEXNAME/_settings&gt;&#34; -d &#39;{
 &#34;index&#34; : {
    &#34;number_of_replicas&#34; : 2
  }
}&#39;
```
*  排除es集群要下线的节点
```shell
curl -XPUT &#34;&lt;http://ip:9200/_cluster/settings&gt;&#34;  -d&#39;{
	&#34;transient&#34;: {
      &#34;cluster.routing.allocation.exclude._ip&#34;: &#34;要下线的ip&#34;
    }
 }&#39;
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/elasticsearch/  

