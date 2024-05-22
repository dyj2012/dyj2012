### jdbc

```
driver=com.oceanbase.jdbc.Driver
conn=jdbc:oceanbase://10.10.22.189:2883/console?useUnicode=true&characterEncoding=utf-8&rewriteBatchedStatements=true&allowMultiQueries=true&allowPublicKeyRetrieval=true&useSSL=false&serverTimezone=Asia/Shanghai
user=yn_console@ecs#yn
password=aaAA1122__
```

### nginx

```
user root;
worker_processes 1;

error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;


events {
    worker_connections 1024;
}


http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
    '$status $body_bytes_sent "$http_referer" '
    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    sendfile on;

    gzip on;
    gzip_types text/css
    application/javascript text/plain application/json application/x-javascript
    application/css application/xml application/xml+rss text/javascript
    application/x-httpd-php image/jpeg image/gif image/png image/x-ms-bmp;
    client_max_body_size 100M;

    upstream xxljobSrv {
        server localhost:80;
    }

    upstream consoleSrv {
        server localhost:8095;
    }

    upstream ecsSrv {
        server localhost:8096;
    }

    upstream datasetSrv {
        server localhost:8094;
    }

    upstream dashboardSrv {
        server localhost:8585;
    }

    server {
        listen 8080;
        server_name localhost;
        location = / {
            rewrite ^ /ecs_console/index.html permanent;
        }

        location /xxl-job-admin/ {
            proxy_read_timeout 240s;
            proxy_pass http://xxljobSrv;
        }

        location ~/(xxl-job-admin)/(.+)\.(css|js|html|map|gif|jpg|jpeg|png|ico|ttf|woff)$ {
            proxy_pass http://xxljobSrv;
        }

        location ~* \.(css|js|html|map|gif|jpg|jpeg|png|ico|mp4|svg|icon|ttf|woff|apk|ipa)$ {
            if ($request_filename ~* .\.(?:htm|html)$) {
                add_header Cache-Control "no-cache";
            }
            if ($request_filename ~ .*\.(?:js|css|png|jpg|gif)$) {
                add_header Cache-Control "public";
                expires 30d;
            }
            root /usr/share/nginx/html;
        }

        location ~ /console/ {
            rewrite ^/console/(.*)$ /$1 break;
            proxy_read_timeout 240s;
            proxy_pass http://consoleSrv;
        }

        location ~ /(ecs|masterdata)/ {
            rewrite ^/(ecs|masterdata)/(.*)$ /$2 break;
            proxy_read_timeout 240s;
            proxy_pass http://ecsSrv;
        }


        location ~ /dmp-dataset/ {
            rewrite ^/(dmp-dataset)/(.*)$ /$2 break;
            proxy_read_timeout 240s;
            proxy_pass http://datasetSrv;
        }

        location ~ /dashboard/ {
            rewrite ^/(dashboard)/(.*)$ /$2 break;
            proxy_read_timeout 240s;
            proxy_pass http://dashboardSrv;
        }

        location = /designer/ { index /designer/index.html; }
        location = /homepageDesigner/ { index /homepageDesigner/index.html; }
        location = /ecs-console/ { index /ecs-console/index.html; }
        location = /ecs_console/ { index /ecs_console/index.html; }
        location = /ecs-console-mobile/ { index /ecs-console-mobile/index.html; }
        location = /ecs_console_mobile/ { index /ecs_console_mobile/index.html; }
        location = /ecs-web/ { index /ecs-web/index.html; }
        location = /ecs-web-mobile/ { index /ecs-web-mobile/index.html; }
        location = /ecs-mobile/ { index /ecs-mobile/index.html; }
        location = /fssc-web/ { index /fssc-web/index.html; }
        location = /dataset/ { index /dataset/index.html; }
        location = /dashboard/ { index /dashboard/index.html; }
        location = /dataObject/ { index /dataObject/index.html; }

        location / {
            proxy_read_timeout 240s;
            proxy_pass http://consoleSrv;
        }

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        real_ip_recursive on;
        keepalive_timeout 65;
    }
}
```