# ---------- Upstreams (WSO2 Backend listeners) ----------
upstream sslapi.am.wso2.com {      # Carbon / UI (9443)
    server 127.0.0.1:9443;
    keepalive 32;
}

upstream sslgw.am.wso2.com {       # Gateway (8243)
    server 127.0.0.1:8243;
    keepalive 32;
}

# ---------- UI: carbon / admin / publisher / devportal ----------
server {
    listen 443 ssl;
    server_name ptnapim.example.ir;
    http2 on;

    ssl_certificate     /etc/nginx/ssl/wso2-fullchain.crt;
    ssl_certificate_key /etc/nginx/ssl/wso2.key;

    # Forwarded headers (WSO2 expects real host/https)
    proxy_set_header Host              $http_host;
    proxy_set_header X-Forwarded-Proto https;
    proxy_set_header X-Forwarded-Host  $host;
    proxy_set_header X-Forwarded-Port  443;
    proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;

    proxy_ssl_server_name on;
    proxy_ssl_verify     off;

    proxy_http_version 1.1;
    proxy_set_header Connection "";
    proxy_read_timeout  300;
    proxy_send_timeout  300;
    client_max_body_size 50m;
    # --- OAuth/OIDC & Auth endpoints ---
    location /oauth2/                 { proxy_pass https://sslapi.am.wso2.com$request_uri; }
    location /oidc/                   { proxy_pass https://sslapi.am.wso2.com$request_uri; }
    location /authenticationendpoint/ { proxy_pass https://sslapi.am.wso2.com$request_uri; }

  
    location /logincontext            { proxy_pass https://sslapi.am.wso2.com$request_uri; }
    location /commonauth              { proxy_pass https://sslapi.am.wso2.com$request_uri; }
    # location /samlsso/             { proxy_pass https://sslapi.am.wso2.com$request_uri; }

    # SOAP/Carbon services ﻭ REST Admin/Devops/Publisher APIs
    location /services/ { proxy_pass https://sslapi.am.wso2.com$request_uri; proxy_redirect off; }
    location /api/      { proxy_pass https://sslapi.am.wso2.com$request_uri; proxy_redirect off; }

    # UI apps
    location /publisher/ { proxy_pass https://sslapi.am.wso2.com$request_uri; proxy_redirect off; }
    location /devportal/ { proxy_pass https://sslapi.am.wso2.com$request_uri; proxy_redirect off; }
    location /carbon/    { proxy_pass https://sslapi.am.wso2.com$request_uri; proxy_redirect off; }

    # Root → Publisher
    location = / { return 302 https://$host/publisher/; }

    access_log /var/log/nginx/apim_ui_access.log;
    error_log  /var/log/nginx/apim_ui_error.log;
}

# ---------- Gateway (8243) ----------
server {
    listen 443 ssl;
    server_name api.ptnapim.example.ir;
    http2 on;

    ssl_certificate     /etc/nginx/ssl/wso2-fullchain.crt;
    ssl_certificate_key /etc/nginx/ssl/wso2.key;

    proxy_set_header Host              $http_host;
    proxy_set_header X-Forwarded-Proto https;
    proxy_set_header X-Forwarded-Host  $host;
    proxy_set_header X-Forwarded-Port  443;
    proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;

    proxy_ssl_server_name on;
    proxy_ssl_verify     off;

    proxy_http_version 1.1;
    proxy_set_header Connection "";
    proxy_read_timeout  300;
    proxy_send_timeout  300;
    client_max_body_size 50m;

    location / {
        proxy_pass https://sslgw.am.wso2.com;
        proxy_redirect off;
    }

    access_log /var/log/nginx/apim_gw_access.log;
    error_log  /var/log/nginx/apim_gw_error.log;
}

# ---------- HTTP → HTTPS ----------
server {
    listen 80;
    server_name ptnapim.example.ir;
    return 301 https://$host$request_uri;
}
server {
    listen 80;
    server_name api.ptnapim.example.ir;
    return 301 https://$host$request_uri;
}

# ---------- Catch-all ----------
server {
    listen 443 ssl default_server;
    server_name _;
    http2 on;

    ssl_certificate     /etc/nginx/ssl/wso2-fullchain.crt;
    ssl_certificate_key /etc/nginx/ssl/wso2.key;

    return 301 https://ptnapim.csdiran.ir$request_uri;
}
