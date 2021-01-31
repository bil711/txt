


  `sudo apt-get install lighttpd` -  веб-сервера  
  
  настройка форвардинга, исчезнут после перезагрузки. ppp0 - это интерфейс на который приходин инэт.  
  `echo 1 > /proc/sys/net/ipv4/ip_forward
  iptables -t nat -A POSTROUTING -o ppp0 -j MASQUERADE`  