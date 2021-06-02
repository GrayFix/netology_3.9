# 3.9 Элементы безопасности информационных систем
1. Vagrant файл в репозитории
2. Запустил в виде демона systemctl start vault, сейчас в конфигурации он сразу слушает порт 8200 на всех интерфейсах и UI сразу включен.
3. И 4 пункт.  Открытые части сертификатов CA, промежуточный и сертификат для nginx лежат в соответствующих файлах в репозитории. Сертификаты для домена nt-example.com
4.
5. Из конфигурации nginx. CN в сертификате nginx.nt-example.com
```
server {
        listen 443 ssl;

        server_name nginx.nt-example.com;

        ssl_certificate /etc/nginx/ssl/nginx.crt;
        ssl_certificate_key /etc/nginx/ssl/nginx.key;

        root /var/www/html;
        index index.html;

        location / {
                try_files $uri $uri/ =404;
        }
}
```
6.
   1. Прописываем в hosts  строку 127.0.0.1 nginx.nt-example.com
   2. В каталог /usr/local/share/ca-certificates складываем файл с открытым ключом промежуточного сертификата. Файл должен быть с расширением .crt. И выполняем команду update-ca-certificates
   3. Вывод curl 
   ```
   root@client:/usr/local/share/ca-certificates# curl https://nginx.nt-example.com
   <html><head><title>403 Forbidden</title></head>
   <body>
   <center><h1>403 Forbidden</h1></center>
   <hr><center>nginx/1.18.0 (Ubuntu)</center>
   </body>
   </html>
   ```
   
7. Мы на предприятии достаточно часто используем сертфикаты от Let's Encrypt. Технология знакомая. 