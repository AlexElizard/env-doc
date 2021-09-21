# Установка и конфигурация Nginx
## Установка через репозиторий в Debian
* Подключите apt-репозиторий
```
echo "deb http://nginx.org/packages/debian `lsb_release -cs` nginx" | sudo tee /etc/apt/sources.list.d/nginx.list
```
* Скачайте ключ проверки для apt
```
curl -fsSL https://nginx.org/keys/nginx_signing.key | sudo apt-key add -
```
* Установите nginx
```
sudo apt update && sudo apt install nginx
```

## Конфигурация
* Создайте конфигурационный файл на основе примера
```
sudo nano /etc/nginx/conf.d/домен.conf

# /etc/nginx/conf.d/example.conf

server {
    listen       80;            # Port
    server_name  example.com;   # Host
   
    ###########
    # Logging #
    ###########
    access_log  /var/log/nginx/example/access.log combined;
    error_log /var/log/nginx/example/error.log error;

    ##################
    # Frontend-React #
    ##################
    location / {
        root /home/user/www/example/react/build;
        try_files $uri /index.html;
    }
    
    ###################################
    # Backend-Django static and media #
    ###################################
    location /upload {
        alias /home/user/www/example/django/media;
    }

    location /django-static {
        alias /home/user/www/example/django/static;
    }
    
    ##################
    # Backend-Python #
    ##################
    location ~^/(api|admin|swagger) {
        try_files $uri @backend;
    }

    location @backend {
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_redirect off;
        proxy_pass http://unix:/run/gunicorn-example.sock;
    }
}
```

# Расширения (Опционально). Установка через пересборку
## Nginx spnego module
* Узнайте версию установленного nginx
```
nginx -V
```
* Создайте переменную окружения ***NGINX_VERSION***
```
NGINX_VERSION=1.20.0
```
* Скачайте и распакайке исходники nginx и nginx-spnego-module
```
cd /tmp \
&& wget http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz \
&& tar -xzf nginx-${NGINX_VERSION}.tar.gz \
&& git clone https://github.com/stnoonan/spnego-http-auth-nginx-module.git nginx-${NGINX_VERSION}/spnego-http-auth-nginx-module
```
* Перейдите в папку с распакованными исходниками nginx
```
cd /tmp/nginx-${NGINX_VERSION}
```
* Cкопируйте ***configure arguments*** установленного nginx
```
nginx -V
```
* Выполните конфигурацию сборки со скопированными параметрами добавив параметр ***--add-module=spnego-http-auth-nginx-module***
```
./configure
--prefix=/etc/nginx
...
--add-module=spnego-http-auth-nginx-module
```
* Запустите сборку. Значение параметра `-j` должно соответствовать числу, полученному при выводе команды `nproc`
```
make -j 4 && sudo make install
```
* Удалите файлы сборки
```
cd ~ && sudo rm -rf /tmp/nginx-${NGINX_VERSION}*
```