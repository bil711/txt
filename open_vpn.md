



 

 ### настройка OpenVPN
 [ man, описание параметров](https://www.lissyara.su/doc/man/safety/openvpn/)  
 
 была обнаружена непонятная логика систем юнитов:    
` systemctl status openvpn-server@.service `  - вот он запущин !!!!  
` systemctl status openvpn-server@server.service ` - но вот он упровляет     
 
 [тут оригинал ](https://bozza.ru/art-269.html)
 
 есть конвигурационный фаил для генирации ключей ` /etc/openvpn/easy-rsa/vars` он нужен для подстановке ответов при генирации елюча  
 используй его чтобы не писать в консоле. также в этом файле можно указать дерикторию куда генерировать ключи.  
  
 для генерации ключей нужна утилита easyrsa она должна была установиться вместе с open vpn  
 лежит она тут `/usr/share/easy-rsa/3.0.8` (возможны другие пути)  
1. выполняем ` ./easyrsa init-pki ` - создаст папку для ключей pki в тойже дериктории где сам easyrsa  
 **важно!** - ./easyrsa init-pki если выполнить в папке с симлинком на easyrsa то там и появится папка pki и фаил vars тоже должен лижать рядом
 
2. `./easyrsa build-ca nopass` - nopass, без пароля. = pki/ca.crt  /pki/private/ca.key  
3. ` openvpn --genkey --secret pki/ta.key ` = pki/ta.key  файл HMAC, для дополнительной верификации клиента и сервера
4. `./easyrsa gen-req server nopass` = /pki/reqs/server.req  /pki/private/server.key это закрытый ключ для сервера и файл запроса сертификата  
5. `./easyrsa gen-dh ` ключ Диффи-Хелмана = /pki/dh.pem
6. `./easyrsa gen-crl` (*не обезательный п.*) = pki/crl.pem список отозванных сертификатов

### Для клиента ###  

1. `./easyrsa gen-req client1 nopass`  
2. `./easyrsa sign-req client client1`  

**Для клиента 4 файла:**  
client1.crt;  
client1.key;  
ca.crt;  
ta.key;   

### config  cервер: ###  
```
port 1194
proto udp
dev tun

ca /etc/openvpn/easy-rsa/pki/ca.crt
cert /etc/openvpn/easy-rsa/pki/issued/server.crt
key /etc/openvpn/easy-rsa/pki/private/server.key
dh /etc/openvpn/easy-rsa/pki/dh.pem

# Проверка, не отозван ли сертификат клиента
# crl-verify crl.pem

server 10.8.12.0 255.255.255.0
ifconfig-pool-persist ipp.txt

# Пусть весь трафик клиента идет через VPN
push "redirect-gateway def1"

# DNS хостинга сервера (или иные, по вашему усмотрению):
push "dhcp-option DNS 8.8.8.8"

keepalive 10 120

tls-server
tls-auth /etc/openvpn/easy-rsa/pki/ta.key 0
tls-timeout 120
auth MD5

cipher BF-CBC

comp-lzo

max-clients 10

user nobody
group nobody

persist-key
persist-tun

status openvpn-status.log

log /var/log/openvpn.log

# 0 is silent, except for fatal errors
# 4 is reasonable for general usage
# 5 and 6 can help to debug connection problems
# 9 is extremely verbose
verb 4



```

### config клиент: ###
```
client
dev tun
proto udp
remote 95.107.80.8 1194
resolv-retry infinite
nobind
persist-key
persist-tun
mute-replay-warnings
ns-cert-type server
tls-auth ta.key 1
auth MD5

ca ca.crt
cert client1.crt
key client1.key

data-ciphers BF-CBC
comp-lzo
verb 3
```

 
 
+ `ca.crt` — файл открытой части сертификата CA, который используется сервером и клиентом OpenVPN, чтобы информировать друг друга о том, что они входят в единую сеть доверия и что между ними отсутствует потенциальный злоумышленник в качестве посредника. В связи с этим, копия файла ca.crt потребуется для вашего сервера и для всех ваших клиентов.
+ `ca.key` — закрытый ключ CA, используемый для подписания ключей и сертификатов серверов и клиентов. Если злоумышленник получит доступ к CA и файлу ca.key, он сможет подписывать запросы сертификатов и получать доступ к вашей VPN, что нарушит ее безопасность. Поэтому файл ca.key должен храниться только на компьютере CA, и для дополнительной безопасности компьютер CA следует выключать, когда он не используется для подписывания запросов сертификатов  


### Пример конфига TAP
* клиент:   
тест на windows, подключилось через интерфейс тап. 
```
client
dev tap
proto udp
remote 109.61.199.70 1194
resolv-retry infinite
nobind
persist-key
persist-tun
mute-replay-warnings


ns-cert-type server
tls-auth ta.key 1
auth MD5

ca ca.crt
cert client1.crt
key client1.key

data-ciphers BF-CBC
comp-lzo
verb 3

``` 

<hr> 


* сервер :  
сетевой интерфейс настроен как bridge   
 > вот конфиг vilana ( /etc/sysconfig/network-scripts/ifcfg-br0 )   
 
 ```
DEVICE=br0
NAME=br0
TYPE=Bridge
IPV6INIT=yes
#BROADCAST=192.168.1.255
DNS1=8.8.8.8
DNS2=8.8.4.4
BOOTPROTO=static
#MTU=1500
IPADDR=192.168.1.200
NETMASK=255.255.255.0
#DEFROUTE=yes
NETWORK=192.168.1.0
IPV4_FAILURE_FATAL=yes
ONBOOT=yes
NM_CONTROLLED=no
STP=no
DELAY=5
``` 

> вилан настроен на физ. интерфейсе, вот конфиг:    
```
NAME=enp6s0
UUID=eA0f6dd7-dd54-4eae-b87c-22ade71301f1
DEVICE=enp6s0
ONBOOT=yes
BOOTPROTO=static
NM_CONTROLLED=no
BRIDGE=br0
STP=no
#VLAN=yes
```
  

``` 
port 1194
proto udp
dev tap0

ca /etc/openvpn/easy-rsa/pki/ca.crt
cert /etc/openvpn/easy-rsa/pki/issued/server.crt
key /etc/openvpn/easy-rsa/pki/private/server.key
dh /etc/openvpn/easy-rsa/pki/dh.pem

# Проверка, не отозван ли сертификат клиента
# crl-verify crl.pem



server-bridge 192.168.1.151 255.255.255.0 192.168.1.152 192.168.1.160  #Характерно для соеденинения в режиме бриджа.

ifconfig 192.168.1.151 255.255.255.0

# ifconfig-pool-persist ipp.txt

# Пусть весь трафик клиента идет через VPN

# push "redirect-gateway 10.0.2.1"
# push "route 192.168.1.200 255.255.255.0"
 push "redirect-gateway def1"

# DNS хостинга сервера (или иные, по вашему усмотрению):
 push "dhcp-option DNS 8.8.8.8"

client-to-client

keepalive 10 120

tls-server
tls-auth /etc/openvpn/easy-rsa/pki/ta.key 0
tls-timeout 120
auth MD5

cipher BF-CBC

comp-lzo

max-clients 10

user nobody
group nobody

persist-key
persist-tun

status openvpn-status.log

log /var/log/openvpn.log

# 0 is silent, except for fatal errors
# 4 is reasonable for general usage
# 5 and 6 can help to debug connection problems
# 9 is extremely verbose
verb 4

# Уровень безопасности. Как минимум нужна возможность запускать скрипты при старте
# script-security 2
script-security 2

# Скрипт, запускаемый при поднятии интерфейса
up /etc/openvpn/server/server_up.sh

```  
<p style="font-size: 36px; font-family: Times, serif; color: red;">Hello, world!</p>
 Внимание! после старта опен впн, запускаеться скрипт в конце конфига /etc/openvpn/server/server_up.sh   
 вот он :   
 ```
#!/bin/bash
# Переводим интерфейс в состояние "up"
/sbin/ifconfig tap0 up
# Добавляем его в br0
/usr/sbin/brctl addif br0 tap0

 
 ```



