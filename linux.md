
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

## NAT

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
  