![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/b0a560a6-ec4b-4965-a55b-c31d38171fa3)# Jarkom-Modul-5-D28-2023

## Konfigurasi GNS

Pertama kita perlu membuat topologi seperti berikut ini.

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/87447ee1-65ae-4a52-9c3a-babb459877eb)

Kemudian konfigurasi pada masing-masing router dan client seperti berikut.

Revolte
```
auto eth0
iface eth0 inet static
address 192.205.0.2
netmask 255.255.255.252
gateway 192.205.0.1
```

Richter 
```
# Ke Fern
auto eth0
iface eth0 inet static
address 192.205.0.6
netmask 255.255.255.252
gateway address 192.205.0.5
```

Fern
```
# Ke Richter
auto eth1
iface eth1 inet static
address 192.205.0.5
netmask 255.255.255.252

# Ke Revolte
auto eth2
iface eth2 inet static
address 192.205.0.1
netmask 255.255.255.252

# Ke Himmel 
auto eth0
iface eth0 inet static
address 192.205.0.130
netmask 255.255.255.128
gateway 192.205.0.129
```

Himmel
```
# Ke Frieren
auto eth0
iface eth0 inet static
address 192.205.0.10
netmask 255.255.255.252
gateway 192.205.0.9

# Ke LaubHills
auto eth1
iface eth1 inet static
address 192.205.2.1
netmask 255.255.254.0

# Ke A3
auto eth2
iface eth2 inet static
address 192.205.0.129
netmask 255.255.255.128


up route add -net 192.205.0.0 netmask 255.255.255.252 gw 192.205.0.130
up route add -net 192.205.0.4 netmask 255.255.255.252 gw 192.205.0.130
```

Stark
```
# Ke Frieren
auto eth0
iface eth0 inet static
address 192.205.0.14
netmask 255.255.255.252
gateway 192.205.0.13
```

Frieren
```
# Ke Aura
auto eth0
iface eth0 inet static
address 192.205.0.18
netmask 255.255.255.252
gateway 192.205.0.17

# Ke Himmel
auto eth2
iface eth2 inet static
address 192.205.0.9
netmask 255.255.255.252

# Ke Stark
auto eth1
iface eth1 inet static
address 192.205.0.13
netmask 255.255.255.252

up route add -net 192.205.0.0 netmask 255.255.255.252 gw 192.205.0.10
up route add -net 192.205.0.4 netmask 255.255.255.252 gw 192.205.0.10
up route add -net 192.205.0.128 netmask 255.255.255.128 gw 192.205.0.10
up route add -net 192.205.2.0 netmask 255.255.254.0 gw 192.205.0.10
```

Aura
```
auto eth0
iface eth0 inet dhcp

# Ke Frieren
auto eth2
iface eth2 inet static
address 192.205.0.17
netmask 255.255.255.252

# Ke Heiter
auto eth1
iface eth1 inet static
address 192.205.0.21
netmask 255.255.255.252

up route add -net 192.205.0.0 netmask 255.255.255.252 gw 192.205.0.18
up route add -net 192.205.0.4 netmask 255.255.255.252 gw 192.205.0.18
up route add -net 192.205.0.128 netmask 255.255.255.128 gw 192.205.0.18
up route add -net 192.205.2.0 netmask 255.255.254.0 gw 192.205.0.18
up route add -net 192.205.0.8 netmask 255.255.255.252 gw 192.205.0.18
up route add -net 192.205.0.12 netmask 255.255.255.252 gw 192.205.0.18

up route add -net 192.205.8.0 netmask 255.255.248.0 gw 192.205.0.22
up route add -net 192.205.4.0 netmask 255.255.252.0 gw 192.205.0.22
```


Heiter
```
# Ke Aura
auto eth0
iface eth0 inet static
address 192.205.0.22
netmask 255.255.255.252
gateway 192.205.0.21

# Ke TurkRegion
auto eth1
iface eth1 inet static
address 192.205.8.1
netmask 255.255.248.0

# Ke A10
auto eth2
iface eth2 inet static
address 192.205.4.1
netmask 255.255.252.0
```

Di SchwerMountain, LaubHills, TurkRegion, dan GrobeForest
```
auto eth0
iface eth0 inet dhcp
```




