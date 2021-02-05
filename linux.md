
### Службы

` systemctl list-units --type service` - список только программ   
` systemctl list-units --type service -all ` - список всех  
` systemctl --failed ` - список служб с неудачным запуском  
` systemctl show --property=UnitPath ` - пути расположения юнит файлов    
` systemctl list-dependencies openvpn-server@server.service ` - посмотреть зависимость юнита, что за чем запускаеться   

` systemctl daemon-reload ` - перезакрузить настройки, приминить настройки   

для того чтобы узнать содержимое, и расположение файла юниты - ` systemctl cat openvpn-server@.service `    






` ip -c a `  ` ip -c link show ` 

`sudo apt-get install lighttpd` -  легкий веб-сервер  
 HTTP File Server - [аналог для windows](http://www.rejetto.com/hfs/?f=dl)  
 ` apt install links` - браузер в терменале links (нажми Esc)    
  
  
  настройка форвардинга, исчезнут после перезагрузки. ppp0 - это интерфейс на который приходин инэт.  
  ```
  echo 1 > /proc/sys/net/ipv4/ip_forward  
  iptables -t nat -A POSTROUTING -o ppp0 -j MASQUERADE 
  ```
  ` nano /etc/sysctl.conf ` - на постоянную после перезагрузки редактируй  
  ` net.ipv4.ip_forward=1 `  
  ` sudo sysctl -p `  - применить настройки.
  
  
  ### iptables.
  
  ` iptables -L -v -n ` - посмотреть правила.  
  ` iptables -t nat -L -n -v ` - + nat
  
  очистить все правила:  
  ```
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
```


## ufw брэндмауэр


`sudo ufw status` - статус.  
`ufw enable, reload` - включить фаерволл и добавить его в автозагрузку.
!`ufw reset` - сбросить все правила!   
`ufw reset` - сбросить все настройки.  


`ufw default deny incoming` - запретить все входящие  
`ufw default allow outgoing` - разрешить входящие  

`ufw status verbose` - посмотреть польз. правила.   
`ufw show raw` - посмотреть правила в вида iptables.  


`ufw allow OpenSSH` - открыть ssh  
`ufw allow 22`  
`ufw allow 22/tcp`   
`ufw deny 80` - блокировка 80 порта   
`ufw allow 80/tcp` - разрешить 80 по протоколу TCP   
`ufw allow from 10.68.23.15` - разрешить 10.68.23.15 доступ к всем портам сервера   
`ufw allow from 10.68.23.15 to any port 22` - разрешить 10.68.23.15 порт 22   

`ufw allow in on enp0s3 to any port 80` - разрешить на определенный сетевой интерфейс  
`ufw allow in on enp0s3 to any port 1521` - разрешить на определенный сетевой интерфейс   
`ufw deny in on enp0s3 to any port 1521`  - запретить на определенный сетевой интерфейс       

чтобы запретить ping, протокол icmp редактируй `/etc/ufw/before.rules`  
закоментируй все строки на протоколе icmp 

это правила по дефолту которые применяються перед всеми пользовательскими правилами.  
/etc/ufw/before.rules/  etc/ufw/before6.rules  
    




 

## включение NAT для ufw

In the file /etc/default/ufw change the parameter DEFAULT_FORWARD_POLICY  

```
DEFAULT_FORWARD_POLICY="ACCEPT"   
```

Also configure /etc/ufw/sysctl.conf to allow ipv4 forwarding (the parameters is commented out by default). Uncomment for ipv6 if you want.  

```
net.ipv4.ip_forward=1  
#net/ipv6/conf/default/forwarding=1  
#net/ipv6/conf/all/forwarding=1  
```
### в UFW напярмую нельзя пробросить порт, поэтому правила пишуться напрямую в дефолтные настройки ufw -> /etc/ufw/before.rules ,впереди всех строк:
```
# NAT table rules
*nat
:POSTROUTING ACCEPT [0:0]

 -A PREROUTING -i enp0s3 -p tcp --dport 8080 -j DNAT --to-destination 10.0.2.12:80 
 -A POSTROUTING -s 10.0.2.12/24 -o enp0s3 -j MASQUERADE
COMMIT
```

непроверенный пример:  
```
iptables -t nat -A PREROUTING -i eth0 -p tcp -d {PUBLIC_IP} --dport 80 -j DNAT --to {INTERNAL_IP}:80  
iptables -t nat -A PREROUTING -i eth0 -p tcp -d {PUBLIC_IP} --dport 443 -j DNAT --to {INTERNAL_IP}:443  
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 ! -d 192.168.1.0/24 -j MASQUERADE  
```
пример:
```  
iptables -t nat -A PREROUTING -i enp0s3 -d 192.168.137.217   -p tcp --dport 8080 -j  DNAT --to-destination 10.0.2.12:80
iptables -t nat -A POSTROUTING -s 10.0.2.12/24 ! -d 10.0.2.12/24 -j MASQUERADE
```
пример:
```
iptables -t nat -A PREROUTING -i enp0s3 -p tcp --dport 80 -j DNAT --to-destination 10.0.2.12  
iptables -t nat -A POSTROUTING -s 10.0.2.12/24 -o enp0s3 -j MASQUERADE
```
пример:
```
iptables -t nat -A PREROUTING -d 192.168.137.217 -p tcp -m tcp --dport 80 -j DNAT --to-destination 10.0.2.12:80
iptables -t nat -A POSTROUTING -s 10.0.2.12 -p tcp -m tcp --dport 80 -j SNAT --to-source 192.168.137.217:80
```



  