# Kibana


* kibana无法新建索引模式，F12 &#34;403&#34;: 权限问题

```shell
PUT .kibana/_settings
{
   &#34;index&#34;: {
     &#34;blocks&#34;: {
       &#34;read_only_allow_delete&#34;: false
    }
  }
}
```

* kibana磁盘满之后无法写入索引

```shell
PUT _settings
{
  &#34;index&#34;: {
      &#34;blocks&#34;: {
          &#34;read_only_allow_delete&#34;: &#34;false&#34;
      }
  }
}
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/kibana/  

