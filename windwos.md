
 #### проброс портов через netstat
 
 
 `netsh interface portproxy reset`    - сбросить все правила  
 `netsh interface portproxy show all` - показать все правила
 
 Пример:
 переходим по 192.168.137.224:81 (пк1) попадаем на (пк2) 95.107.80.8:80
  ```
  netsh interface portproxy add v4tov4 listenport=81 listenaddress=192.168.137.224 connectport=80 connectaddress=95.107.80.8
  ```
 
 netsh interface portproxy add v4tov4 listenport=81 listenaddress=10.54.20.80 connectport=80 connectaddress=192.168.137.1
 