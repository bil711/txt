
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


## NAT
Если вам нужен ufw для NAT, соединения от внешнего интерфейса к внутреннему, решение довольно простое.
В файле / etc / default / ufw измените параметр DEFAULT_FORWARD_POLICY

``
DEFAULT_FORWARD_POLICY = "ПРИНЯТЬ"
``

Также настройте /etc/ufw/sysctl.conf, чтобы разрешить пересылку ipv4 (по умолчанию параметры закомментированы). Если хотите, раскомментируйте ipv6.

``
net.ipv4.ip_forward = 1
# net / ipv6 / conf / default / forwarding = 1
# net / ipv6 / conf / all / forwarding = 1
``


Последний шаг - добавить NAT в конфигурацию ufw. Добавьте следующее в /etc/ufw/before.rules непосредственно перед правилами фильтрации.

``
# Правила таблицы NAT
* нац
: ПРИНЯТИЕ ПОСТРОУТИРОВКИ [0: 0]

# Перенаправлять трафик через eth0 - Измените в соответствии с вашим интерфейсом
-A POSTROUTING -s 192.168.1.0/24 -o eth0 -j МАСКАРАД

# не удаляйте строку 'COMMIT', иначе эти правила нат-таблицы не будут
# быть обработанным
COMMIT
``


Теперь включите изменения, перезапустив ufw.

``
$ sudo ufw disable && sudo ufw enable
``


## ВПЕРЕД

Для переадресации портов просто сделайте что-нибудь вроде этого.

``
# Правила таблицы NAT
* нац
: PREROUTING ACCEPT [0: 0]
: ПРИНЯТИЕ ПОСТРОУТИРОВКИ [0: 0]

# Портовые переадресации
-A PREROUTING -i eth0 -p tcp --dport 22 -j DNAT --to-destination 192.168.1.10

# Перенаправлять трафик через eth0 - Измените в соответствии с вашим интерфейсом
-A POSTROUTING -s 192.168.1.0/24 -o eth0 -j МАСКАРАД

# не удаляйте строку 'COMMIT', иначе эти правила нат-таблицы не будут
# быть обработанным
COMMIT
``
  