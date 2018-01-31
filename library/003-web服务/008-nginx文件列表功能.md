工作中常常有写不能有网页下载东西的需求，在Apache下搭建完成后直接导入文件即可达到下载/显示文件的效果，而Nginx也可以满足这样的需求(nginx 目录列表功能默认是关闭的)，这时就需要配置。  
ngx_http_autoindex_module:此模块用于自动生成目录列表（ngx_http_autoindex_module只在ngx_http_index_module模块未找到索引文件时发出请求）
开启目录列表功能
```shell
1.打开nginx.conf文件，在location server或http中加入
　　autoindex on;　　　　　　　　　　#自动显示目录
　　autoindex_exact_size off;　　　#人性化方式显示文件大小否则以byte显示
　　autoindex_localtime on;　　　　#按服务器时间显示，否则以gmt时间显示
 ```
