


  `sudo apt-get install lighttpd` -  веб-сервера  
  
  настройка форвардинга, исчезнут после перезагрузки. ppp0 - это интерфейс на который приходин инэт.  
  ```
  echo 1 > /proc/sys/net/ipv4/ip_forward 
  iptables -t nat -A POSTROUTING -o ppp0 -j MASQUERADE 
  ```
  ` nano /etc/sysctl.conf ` - на постоянную после перезагрузки редактируй  
  ` net.ipv4.ip_forward=1 `
  ` sudo sysctl -p `  - применить настройки.