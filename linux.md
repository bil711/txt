
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
  