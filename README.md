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
На хосте testServer1 создаем идентичный файл с другим IP-адресом (10.10.10.1).

Перезапускаем сеть:
```
systemctl restart NetworkManager
```
Проверяем корректность настройки:
```
[vagrant@testClient1 ~]$ ip a
[vagrant@testClient1 ~]$ ping 10.10.10.254
```
__Настройка VLAN на Ubuntu (на примере testClient2 )__
Требуется создать файл /etc/netplan/50-cloud-init.yaml со следующим параметрами:
```
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    version: 2
    ethernets:
        enp0s3:
            dhcp4: true
        #В разделе ethernets добавляем порт, на котором будем настраивать VLAN
        enp0s8: {}
    #Настройка VLAN
    vlans:
        #Имя VLANа
        vlan2:
          #Указываем номер VLAN`а
          id: 2
          #Имя физического интерфейса
          link: enp0s8
          #Отключение DHCP-клиента
          dhcp4: no
          #Указываем ip-адрес
          addresses: [10.10.10.254/24]
```
На хосте testServer2 создаём идентичный файл с другим IP-адресом (10.10.10.1).

После создания файлов нужно перезапустить сеть на обоих хостах: 
```
netplan apply
```
После настройки второго VLAN`а ping должен работать между хостами testClient1, testServer1 и между хостами testClient2, testServer2.

3. Настройка LACP между хостами inetRouter и centralRouter
