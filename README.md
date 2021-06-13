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
   
7. Мы на предприятии достаточно часто используем сертификаты от Let's Encrypt. Технология знакомая. 

# Дополнительная работа
## Скриншоты по 2,3,4 заданию
### Работа с Vault
1. Запускаем vault сервер в режиме -dev  
![Alt text](/Images/01.png)
2. Проверяем что ui доступно  
![Alt text](/Images/02.png)
3. Настраиваем переменную окружения VAULT_ADDR и включаем хранилище pki (имя по умолчанию тоже будет pki)  
![Alt text](/Images/03.png)
4. Задаем в хранилище pki максимальный TTL  
![Alt text](/Images/04.png)
5. Создаем самоподписанный сертификат для корневого CA  
![Alt text](/Images/05.png)
6. Публичная часть сертификата корневого CA  
![Alt text](/Images/06.png)
7. Задаем точку распространения списков отзыва для корневого CA  
![Alt text](/Images/07.png)
8. Включаем хранилище pki (с именем pki_int) для промежуточного CA и настраиваем максимальный TTL  
![Alt text](/Images/08.png)
9. Создаем запрос на сертификат для промежуточного CA  
![Alt text](/Images/09.png)
10. Получившийся запрос на сертификат промежуточного CA  
![Alt text](/Images/10.png)
11. Подписываем запрос на сертификат промежуточного CA в корневом CA  
![Alt text](/Images/11.png)
12. Получившаяся публичная часть сертификата промежуточного CA  
![Alt text](/Images/12.png)
13. Добавляем публичная часть сертификата промежуточного CA в хранилище pki_int  
![Alt text](/Images/13.png)
14. В хранилище pki_int создаем роль для выпуска сертификатов с разрешенным доменом nt-example.com  
![Alt text](/Images/14.png)
15. Выпускаем сертификат для CN nginx.nt-example.com 
![Alt text](/Images/15.png)
16. Публичная часть ключа промежуточного CA и CN nginx.nt-example.com. Для использования в nginx нужно будет поменять местами, сначала сертификат для nginx.nt-example.com потом промежуточный CA. Иначе тесты nginx ругались что сертификат не найден  
![Alt text](/Images/16.png)
17. Закрытая часть ключа для CN nginx.nt-example.com  
![Alt text](/Images/17.png)
### Что в WEB UI после проведения работ
1. Страница с хранилищами секретов  
![Alt text](/Images/ui01.png)
2. Список сертификатов в хранилище pki  
![Alt text](/Images/ui02.png)
3. Список сертификатов в хранилище pki_int  
![Alt text](/Images/ui03.png)
4. Роль для выпуска сертификатов  
![Alt text](/Images/ui04.png)

## Чиним ошибку в curl 
Проблема связана с тем что в Ubuntu, как оказалось, индексная страница называется index.nginx-debian.html. Подправленный конфиг nginx-а  
```
server {
        listen 443 ssl;

        server_name nginx.nt-example.com;

        ssl_certificate /etc/nginx/ssl/nginx.crt;
        ssl_certificate_key /etc/nginx/ssl/nginx.key;

        root /var/www/html;
        index index.html index.nginx-debian.html;

        location / {
                try_files $uri $uri/ =404;
        }
}
```
Вывод curl после изменений и перезапуска nginx  
![Alt text](/Images/nginx.png)