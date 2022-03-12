1 . Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP
```
telnet route-views.routeviews.org
Username: rviews
show ip route x.x.x.x/32
show bgp x.x.x.x/32
```
```
route-views>show ip route 94.242.x.x
Routing entry for 94.242.0.0/19
  Known via "bgp 6447", distance 20, metric 0
  Tag 6939, type external
  Last update from 64.71.137.241 1w3d ago
  Routing Descriptor Blocks:
  * 64.71.137.241, from 64.71.137.241, 1w3d ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 6939
      MPLS label: none
route-views>
```

2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.

Запуск модуля
```
echo "dummy" > /etc/modules-load.d/dummy.conf
echo "options dummy numdummies=2" > /etc/modprobe.d/dummy.conf
```
Настройка интерфейса через systemd
```
cat << "EOF" >> /etc/systemd/network/10-dummy0.netdev
[NetDev]
Name=dummy0
Kind=dummy
EOF
cat << "EOF" >> /etc/systemd/network/20-dummy0.network
[Match]
Name=dummy0

[Network]
Address=10.0.8.1/24
EOF
```
Рестарт сети
```
# systemctl restart systemd-networkd
```
Добавлен маршрут, конфигурация через netplan
```
    ens4:
      optional: true
      addresses:
        - 10.0.0.3/24
      routes:
        - to: 10.0.4.0/24
          via: 10.0.0.2
```		  
Таблица маршрутизации, в ней один статический маршрут, метка static:
```
vagrant@vagrant:~$ ip r
default via 192.168.255.1 dev bond0 proto dhcp src 192.168.255.15 metric 100
10.0.0.0/24 dev ens4 proto kernel scope link src 10.0.0.3
10.0.1.0/24 dev vlan1001 proto kernel scope link src 10.0.1.3
10.0.4.0/24 via 10.0.0.2 dev ens4 proto static
10.0.8.0/24 dev dummy0 proto kernel scope link src 10.0.8.1
192.168.255.0/24 dev bond0 proto kernel scope link src 192.168.255.15
192.168.255.1 dev bond0 proto dhcp scope link src 192.168.255.15 metric 100

vagrant@vagrant: $ ip r | grep stat
10.0.4.0/24 via 10.0.0.2 dev ens4 proto static
```
3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.
```
vagrant@vagrant:~$ vagrant@vagrant:~$ ss -tnlp
State         Recv-Q        Send-Q               Local Address:Port               Peer Address:Port       Process
LISTEN        0             4096                       0.0.0.0:111                     0.0.0.0:*
LISTEN        0             4096                 127.0.0.53%lo:53                      0.0.0.0:*
LISTEN        0             128                        0.0.0.0:22                      0.0.0.0:*
LISTEN        0             4096                          [::]:111                        [::]:*
LISTEN        0             128                           [::]:22                         [::]:*
```
53 порт - это DNS.

22 порт - это SSH.

4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?
```
vagrant@vagrant:~$ ss -unap
State        Recv-Q        Send-Q                Local Address:Port               Peer Address:Port       Process
UNCONN       0             0                     127.0.0.53%lo:53                      0.0.0.0:*
UNCONN       0             0                    10.0.2.15%eth0:68                      0.0.0.0:*
UNCONN       0             0                           0.0.0.0:111                     0.0.0.0:*
UNCONN       0             0                              [::]:111                        [::]:*
```
53 порт - это DNS.

68 порт использует DHCP для отправки сообщений клиентам.

5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.



