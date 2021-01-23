
 #### проброс портов через netstat
 
 
 `netsh interface portproxy reset`    - сбросить все правила  
 `netsh interface portproxy show all` - показать все правила
 
 Пример:  
 переходим по 192.168.137.224:81 (пк1) попадаем на (пк2) 95.107.80.8:80
  ```
  netsh interface portproxy add v4tov4 listenport=81 listenaddress=192.168.137.224 connectport=80 connectaddress=95.107.80.8
  ```
 Пример:
 (пк1)- подключен к точки доступа на (пк2), переходиш в браузере по 192.168.137.224:8881(онже ip пк1),попадаеш на сетевую карту пк2 
 ```
 netsh interface portproxy add v4tov4 listenaddress=192.168.137.224 listenport=8881 connectaddress=192.168.137.1 connectport=8881 
 ```
 пк2 слушает 192.168.137.1:8881 ( сетевая wi-fi ), и отправляет на 10.54.20.80
 ```
 netsh interface portproxy add v4tov4 listenaddress=192.168.137.1 listenport=8881  connectaddress=10.54.20.80 connectport=80
 ```
 