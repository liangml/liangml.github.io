# Apisix


# Nginx proxy_pass &#43; / 问题:

```shell
location /api {
	proxy_pass http://api_proxy/;
}
# 访问: http://api.xxx.com/api/admin;
# 转发效果：http://api_proxy/admin;
```

&gt; 由于Apisix 使用的是radixtree 的写法, 导致只支持匹配规则(Full match,Prefix matching) , 并且不会去掉匹配的路径, 这个时候,
&gt; 为了去掉上面的 /api , 需要使用到插件：proxy-rewrite&lt;br&gt;
&gt; Apisix-Router：https://apisix.apache.org/zh/docs/apisix/terminology/router&lt;br&gt;
&gt; Proxy-rewrite：https://apisix.apache.org/zh/docs/apisix/plugins/proxy-rewrite&lt;br&gt;
&gt; ngx_http_rewirte_module：https://nginx.org/en/docs/http/ngx_http_rewrite_module.html&lt;br&gt;
&gt; ngx_http_proxy_module：https://nginx.org/en/docs/http/ngx_http_proxy_module.html&lt;br&gt;
&gt; ngx_stream_proxy_module：http://nginx.org/en/docs/stream/ngx_stream_proxy_module.html

```json
{
  &#34;uri&#34;: &#34;/api*&#34;,
  &#34;name&#34;: &#34;api&#34;,
  &#34;id&#34;: &#34;489259072256738721&#34;,
  &#34;upstream&#34;: {
    &#34;type&#34;: &#34;roundrobin&#34;,
    &#34;pass_host&#34;: &#34;pass&#34;,
    &#34;nodes&#34;: {
      &#34;10.1.1.1:3030&#34;: 10
    },
    &#34;timeout&#34;: {
      &#34;send&#34;: 6,
      &#34;connect&#34;: 6,
      &#34;read&#34;: 6
    },
    &#34;scheme&#34;: &#34;http&#34;,
    &#34;keepalive_pool&#34;: {
      &#34;idle_timeout&#34;: 60,
      &#34;requests&#34;: 1000,
      &#34;size&#34;: 320
    }
  },
  &#34;plugins&#34;: {
    &#34;proxy-rewrite&#34;: {
      &#34;regex_uri&#34;: [
        &#34;^/api(.*)$&#34;,
        &#34;$1&#34;
      ]
    }
  },
  &#34;status&#34;: 1,
  &#34;host&#34;: &#34;api.xxx.com&#34;
}
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/apisix/  

