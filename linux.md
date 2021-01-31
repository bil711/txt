
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

```
iptables -t nat -A PREROUTING -i eth0 -p tcp -d {PUBLIC_IP} --dport 80 -j DNAT --to {INTERNAL_IP}:80  
iptables -t nat -A PREROUTING -i eth0 -p tcp -d {PUBLIC_IP} --dport 443 -j DNAT --to {INTERNAL_IP}:443
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 ! -d 192.168.1.0/24 -j MASQUERADE
```
пример:
```  
-A PREROUTING -i eth0 -d 202.54.1.1   -p tcp --dport 80 -j  DNAT --to-destination 192.168.1.100:80
-A POSTROUTING -s 192.168.1.0/24 ! -d 192.168.1.0/24 -j MASQUERA
```
пример:
```
-A PREROUTING -i eth0 -p tcp --dport 22 -j DNAT --to-destination 192.168.1.10  
-A POSTROUTING -s 192.168.1.0/24 -o eth0 -j MASQUERADE
```



  