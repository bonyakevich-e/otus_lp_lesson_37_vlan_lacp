### OTUS Linux Professional Lesson #37 | Subject: Сетевые пакеты. VLAN'ы. LACP

#### ЦЕЛЬ:
Научиться настраивать VLAN и LACP.

#### ОПИСАНИЕ ДЗ:
В Office1 в тестовой internal сети есть сервера с доп интерфейсами и адресами: 
- testClient1 - 10.10.10.254
- testClient2 - 10.10.10.254
- testServer1- 10.10.10.1 
- testServer2- 10.10.10.1

Равести вланами:
testClient1 <-> testServer1
testClient2 <-> testServer2

Между centralRouter и inetRouter "пробросить" 2 линка (общая inernal сеть) и объединить их в бонд, проверить работу c отключением интерфейсов

По итогу выполнения домашнего задания у нас должна получиться следующая топология сети:

![55555](https://github.com/user-attachments/assets/735201d2-7beb-4fd0-a328-9aba47d841bb)

#### ОПИСАНИЕ ВЫПОЛНЕНИЯ ДЗ:
1. Устанавливаем дополнительные пакеты `vim` `traceroute` `tcpdump` `net-tools` 
2. Настраиваем VLAN на хостах.
__На REDHAT-based системах (на примере хоста testClient1).__
Создаём файл /etc/sysconfig/network-scripts/ifcfg-vlan1 со следующим параметрами:
```
VLAN=yes
#Тип интерфейса - VLAN
TYPE=Vlan
#Указываем физическое устройство, через которые будет работать VLAN
PHYSDEV=eth1
#Указываем номер VLAN (VLAN_ID)
VLAN_ID=1
VLAN_NAME_TYPE=DEV_PLUS_VID_NO_PAD
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=none
#Указываем IP-адрес интерфейса
IPADDR=10.10.10.254
#Указываем префикс (маску) подсети
PREFIX=24
#Указываем имя vlan
NAME=vlan1
#Указываем имя подинтерфейса
DEVICE=eth1.1
ONBOOT=yes
```



