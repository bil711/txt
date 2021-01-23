
 #### проброс портов через netstat
 
 
 `netsh interface portproxy reset`    - сбросить все правила  
 `netsh interface portproxy show all` - показать все правила  
 
 netsh interface portproxy add v4tov4 listenport=81 listenaddress=192.168.137.224 connectport=80 connectaddress=192.168.137.1
 