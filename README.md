## reverse-proxy-with-docker

    * docker-compose wordpress *
---
```txt
|---wordpress-compose/
| |
| |---docker-compose.yml
| |
| |---nginx/
| |   |---wordpress.conf
| |
| |---db-data/
| |
| |---logs/
| |   |
| |   |---nginx/
| |
| |---wordpress
```
```yml
docker-compose.yml

nginx:
    image: nginx:latest
    ports:
        - '90:80'
    volumes:
        - ./nginx:/etc/nginx/conf.d
        - ./logs/nginx:/var/log/nginx
        - ./wordpress:/var/www/html
    links:
        - wordpress
    restart: always

mysql:
    image: mariadb
    ports:
        - '3306:3306'
    volumes:
        - ./db-data:/var/lib/mysql
    environment:
        - MYSQL_ROOT_PASSWORD=aqwe123
    restart: always

wordpress:
    image: wordpress:4.9.6-php7.2-fpm
    ports:
        - '9000:9000'
    volumes:
        - ./wordpress:/var/www/html
    environment:
        - WORDPRESS_DB_NAME=wpdb
        - WORDPRESS_TABLE_PREFIX=wp_
        - WORDPRESS_DB_HOST=mysql
        - WORDPRESS_DB_PASSWORD=aqwe123
    links:
        - mysql
    restart: always
```
```conf
wordpress.conf

server {
    listen 80;
    server_name [IP Address];

    root /var/www/html;
    index index.php;

    access_log /var/log/nginx/nginx-access.log;
    error_log /var/log/nginx/nginx-error.log;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass wordpress:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}


```
