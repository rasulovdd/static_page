<h1 align="center">static_page</h1>

## Описание
Статическая страница SNI, Вам сюда нельзя

## Установка 
1. Переходим в папку /var/www/html<br/>
    ```bash
    cd /var/www/html
    ```

2. Скачайте репозиторий<br/>
    ```bash
    git clone https://github.com/rasulovdd/static_page.git && cd static_page
    ```

## Nginx (по стандарту default, если вы меняли настройки то открывайте свой файл)
1. Открываем конфиг сайта и добавляем 
    ```bash
    nano /etc/nginx/sites-available/default
    ```

2. Удаляем все и вставляем код
    ```bash
    server {
        listen 80;
        server_name mysite.ru;

        if ($host = mysite.ru) {
            return 301 https://$host$request_uri;
        }

        return 404;
    }
    server {
        listen 127.0.0.1:8443 ssl http2 proxy_protocol;
        server_name mysite.ru;

        ssl_certificate /etc/letsencrypt/live/mysite.ru/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/mysite.ru/privkey.pem;

        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers on;

        ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305';
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout 1d;
        ssl_session_tickets off;

        # Настройки Proxy Protocol
        real_ip_header proxy_protocol;
        set_real_ip_from 127.0.0.1;
        set_real_ip_from ::1;

        root /var/www/html/static_page;
        index index.html;

        location / {
            try_files $uri $uri/ =404;
        }
    }
    ```

3. Меняем mysite.ru на свой домен (сертификат установлен через certbot)

4. Проверяем настройки nginx
    ```bash
    nginx -t
    ```
5. Если все ок, перезапускаем nginx 
    ```bash
    sudo systemctl restart nginx
    ```