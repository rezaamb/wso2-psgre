The guide examines two modes, HTTP and HTTPS: 

1. HTTP (port 80) :

```bash
upstream ssl.wso2.is.com {
    server xxx.xxx.xxx.xx3:9443;
    server xxx.xxx.xxx.xx4:9443;
    ip_hash;
}

server {
listen 443;
    server_name is.wso2.com;
    ssl on;
    ssl_certificate /etc/nginx/ssl/wrk.crt;
    ssl_certificate_key /etc/nginx/ssl/wrk.key;
    location / {
            proxy_set_header X-Forwarded-Host $host;
            proxy_set_header X-Forwarded-Server $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_read_timeout 5m;
            proxy_send_timeout 5m;
            proxy_pass https://ssl.wso2.is.com;

            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
}
```

Here all requests are sent to the upstream which are the WSO2 nodes.

The TLS certificate and private key must be located on Nginx.
```bash
sudo mkdir -p /etc/nginx/ssl
sudo chown root:root /etc/nginx/ssl/wso2.crt /etc/nginx/ssl/wso2.key
sudo chmod 600 /etc/nginx/ssl/wso2.key
sudo chmod 644 /etc/nginx/ssl/wso2.crt
```

```bash
server {
    listen 443;
    server_name mgt.is.wso2.com;
    ssl on;
    ssl_certificate /etc/nginx/ssl/mgt.crt;
    ssl_certificate_key /etc/nginx/ssl/mgt.key;

    location / {
            proxy_set_header X-Forwarded-Host $host;
            proxy_set_header X-Forwarded-Server $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_read_timeout 5m;
            proxy_send_timeout 5m;
            proxy_pass https://xxx.xxx.xxx.xx2:9443/;

            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
    error_log  /var/log/nginx/mgt-error.log ;
        access_log  /var/log/nginx/mgt-access.log;
}
```


```
sudo ln -s /etc/nginx/sites-available/wso2.conf /etc/nginx/sites-enabled/wso2.conf
sudo chown root:root /etc/nginx/sites-available/wso2.conf
sudo chmod 644 /etc/nginx/sites-available/wso2.conf
// If you wrote in conf.d:
sudo chown root:root /etc/nginx/conf.d/wso2.conf
sudo chmod 644 /etc/nginx/conf.d/wso2.conf
```
