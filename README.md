Example: redirect with nginx S2I
---

Fork of [sclorg/nginx-ex](github.com/sclorg/nginx-ex)
[Upstream readme](README-upstream.md)


## Instructions

Modify [nginx-default-cfg/redirect.conf] with the target website.

Nginx will run with a default config and a default server block, which loads any .conf in nginx-default-cfg.
If it's necessary to customize the nginx.conf, simply include the custom nginx.conf in this repo's root.

### Default nginx.conf

```
worker_processes auto;
error_log /var/opt/rh/rh-nginx112/log/nginx/error.log;
pid /var/opt/rh/rh-nginx112/run/nginx/nginx.pid;

# Load dynamic modules. See /opt/rh/rh-nginx112/root/usr/share/doc/README.dynamic.
include /opt/rh/rh-nginx112/root/usr/share/nginx/modules/*.conf;

events {
    worker_connections  1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/opt/rh/rh-nginx112/log/nginx/access.log  main;

    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;
    keepalive_timeout  65;
    types_hash_max_size 2048;

    include       /etc/opt/rh/rh-nginx112/nginx/mime.types;
    default_type  application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /opt/app-root/etc/nginx.d/*.conf;

    server {
        listen       8080 default_server;
        listen       [::]:8080 default_server;
        server_name  _;
        root         /opt/app-root/src;

        # Load configuration files for the default server block.
        include      /opt/app-root/etc/nginx.default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
        location = /40x.html {
        }

        error_page 500 502 503 504  /50x.html;
        location = /50x.html {
        }

    }
}
```
