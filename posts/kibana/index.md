# Kibana


* kibana无法新建索引模式，F12 "403": 权限问题

```shell
PUT .kibana/_settings
{
   "index": {
     "blocks": {
       "read_only_allow_delete": false
    }
  }
}
```

* kibana磁盘满之后无法写入索引

```shell
PUT _settings
{
  "index": {
      "blocks": {
          "read_only_allow_delete": "false"
      }
  }
}
```

---

> 作者: Leon  
> URL: https://blog.liangml.dpdns.org/posts/kibana/  

