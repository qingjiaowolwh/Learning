### 前端跨域问题

```server {
  listen       83;
  server_name  localhost;
  location ^~/diff/ {
    proxy_pass http://localhost:9091;
    add_header Access-Control-Allow-Origin $http_origin;
    add_header Access-Control-Allow-Credentials true;
    add_header Access-Control-Allow-Methods *;
    add_header Access-Control-Allow-Headers $http_access_control_request_headers;
    if ($request = OPTIONS){
      return 200;
    }
  }
  location ^~/api/ {
    add_header Access-Control-Allow-Origin $http_origin;
    add_header Access-Control-Allow-Credentials true;
    add_header Access-Control-Allow-Methods *;
    add_header Access-Control-Allow-Headers $http_access_control_request_headers;
    if ($request = OPTIONS){
      return 200;
    }
    proxy_pass http://localhost/api/user/login;
  }
  location / {
    root   /opt/web/portal/yapi-diff;
    index  index.html index.htm;
    try_files $uri $uri/ /index.html?s = $uri&$args;
  }
}
```
