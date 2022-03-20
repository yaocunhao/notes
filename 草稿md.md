http://10.190.9.216:6001/static/map-editor2.0/index.html#/label





-  标椎和红绿灯标注功能？
- 空的数据表 -> 保存region相关数据
- region只展示 guangzhou && 改名为 ceshi





```C
server { #主机设置

    listen       3002;
    server_name   localhost;
    #root         /home/xiaoju/fe/voyager/offboard/cloud/src/fe_static/project/map-editor2.0/;
    #index       index.html index.htm;

    ##location / {
    #   root        /home/xiaoju/fe/voyager/offboard/cloud/src/fe_static/output/map-editor2.0/;
    #   index       index.html index.htm;
    #   root  html;

    #}

    location ^~ /static/map-editor2.0/ {
      alias   /home/xiaoju/fe/voyager/offboard/cloud/src/fe_static/output/map-editor2.0/;
      index index.html;
    }

    location ^~ /static/img/tiles/ {
      alias   /home/xiaoju/data/;
      add_header Access-Control-Allow-Origin '*';
      index index.html;
    }

    location /map_editor/api/v2/ {
       proxy_pass_header Server;
       proxy_redirect off;
       proxy_set_header X-Real-IP $remote_addr;
       proxy_set_header X-Scheme $scheme;
       proxy_pass http://10.190.9.216:6001;
    }


}
```

