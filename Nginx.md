
 ` systemctl stop nginx , stop i td` -- 
 ` systemctl reload nginx ` - обнавить настройки не сброив соед.
 
 
  
 
 
 ### Базовая авторизация   
 
 ` sudo apt install apache2-utils ` - ставим утил от апача для генерации пасс   
 ` sudo touch /etc/nginx/htpasswd ` - фаил хронения пасс   
 ` sudo htpasswd /etc/nginx/htpasswd bob ` - добавить польз. боб   
 
 
 пример конфига вертуального домена  
 ```
 server {
listen 80;
listen [::]:80;
root /var/www/w1.lan;
index index.html index.htm index.nginx-debian.html;
server_name w1.lan;
location / {
try_files $uri $uri/ =404;
auth_basic "Restricted Content";
auth_basic_user_file /etc/nginx/htpasswd;
}
}
```
