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


### No. 1

Soal : 

Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Aura menggunakan iptables, tetapi tidak ingin menggunakan MASQUERADE.

Jawab :

Agar bisa topologi bisa mengakses keluar tanpa menggunakan MASQUERADE. Kita perlu setting sebagai berikut di Aura.

```
# Get the local (private) IP address of eth0
local_ip=$(ifconfig eth0 | grep -oP 'inet addr:\K(\S+)' | awk '{print $1}')

# Add iptables rule for SNAT using the obtained local IP
iptables -t nat -A POSTROUTING -o eth0 -s 192.205.0.0/16 -j SNAT --to-source $local_ip
```

Testing :

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/13032dce-90af-4142-9c70-350e79696a32)


### No. 2

Soal : 

Kalian diminta untuk melakukan drop semua TCP dan UDP kecuali port 8080 pada TCP.

Jawab : 

Untuk melakukan drop semua TCP dan UDP kecuali port 8080 pada TCP, dapat kita setting sebagai berikut di Sein.

```
apt-get install netcat -y

# Menghapus aturan iptables yang sudah ada
iptables -F

# Mengizinkan koneksi loopback
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# Mengizinkan koneksi pada port 8080 TCP
iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 8080 -j ACCEPT

# Drop Semua input selain 8080
iptables -A INPUT -p tcp -j DROP
iptables -A INPUT -p udp -j DROP

# Menolak semua koneksi UDP
iptables -A INPUT -p udp -j DROP
```

Kemudian kita install netcat pada GrobeForest dengan command `apt-get install netcat -y` untuk melakukan testing.

Testing : 

Pada Sein ketika kita uji dengan port 8080 koneksi TCP :

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/85e4492e-7093-467d-8041-7ac43c10d829)


Pada GrobeForest ketika kita uji dengan port 8080 koneksi TCP :

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/59e77d60-d8a9-4847-b872-6c2cbdb3407b)

Pada Sein ketika uji dengan port 8000 koneksi TCP : 

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/8a2c1be8-3be5-43ee-8526-b760fc80103b)

Pada GrobeForest ketika kita uji dengan port 8000 koneksi TCP :

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/a9419a00-2bbf-45e2-989d-45d96e043b4e)


Test UDP Pada Sein : 

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/81de82aa-7b8e-4280-bc0a-8b411b17c755)

Test UDP Pada GrobeForest :

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/28050606-e263-4993-a0ed-586fde9ca84b)



### No. 3

Soal : 

Kepala Suku North Area meminta kalian untuk membatasi DHCP dan DNS Server hanya dapat dilakukan ping oleh maksimal 3 device secara bersamaan, selebihnya akan di drop.

Jawab : 

Untuk dapat memenuhi permintaan tersebut kita dapat melakukan setting ip table di Revolte dan Richter sebagai berikut : 

```
# Allow connection yang sudah ada di establish
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Limit ping 3
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

![Uploading image.png…]()




