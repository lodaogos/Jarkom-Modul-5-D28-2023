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

Testing : 

Testing dengan ping ke Richter.

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/ae1f6b8e-23ab-43e3-a1d3-d398a3e48d62)

Bisa dilihat pada saat kita ping menggunakan SchwerMountains, tidak didapatkan feedback dari Richter sehingga ping tersebut fail.

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/893f5f60-997f-4a8c-b9bc-115849adf8a8)



### No. 4

Soal : 

Lakukan pembatasan sehingga koneksi SSH pada Web Server hanya dapat dilakukan oleh masyarakat yang berada pada GrobeForest

Jawab : 

Kita bisa menggunakan rule iptable sebagai berikut di masing-masing webserver.

```
iptables -A INPUT -p tcp --dport 22 -s 192.205.4.0/22 -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -j DROP
```

Testing :

Menggunakan GrobeForest

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/fc571fe1-b01f-48a4-90fa-a73e21379f46)

Menggunakan TurkRegion :

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/55218461-a65f-448c-bd62-7d7e9a0544c8)


### No. 5

Soal : 

Selain itu, akses menuju WebServer hanya diperbolehkan saat jam kerja yaitu Senin-Jumat pada pukul 08.00-16.00.

Jawab : 

Agar webserver hanya bisa diakses saat jam kerja, kita dapat melakukan seting sebagai berikut.

```
iptables -A INPUT -m time --timestart 08:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -j REJECT
```

Saat hari minggu : 

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/39a2dbf7-13fc-414f-85d2-8d8287b7839d)

Ketika kita coba ping akan error : 

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/2cb4fc2b-ab82-46a1-8623-93e59cccc103)


Saat hari senin jam 12.00 : 

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/863f9942-f4af-4a53-b870-b398cc4ecbf9)

Ketika kita coba ping akan mendapatkan feedback : 

![image](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/115076652/048acf84-7164-409d-ad1e-88ce4ce9b1c5)


### No. 6

Soal :

Rule bahwa akses pada hari Senin - Kamis pada jam 12.00 - 13.00 dilarang (istirahat maksi cuy) dan akses di hari Jumat pada jam 11.00 - 13.00 juga dilarang (maklum, Jumatan rek)

Jawab :

Mirip seperti no. 5, kita bisa menambahkan rule berikut:
```
iptables -A INPUT -m time --timestart 08:00 --timestop 10:59 --weekdays Fri -j ACCEPT
iptables -A INPUT -m time --timestart 08:00 --timestop 11:59 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -m time --timestart 13:01 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -j REJECT
```

Berikut ini testing-nya date hari Jumat jam 11:30 (jumatan).

![no 6](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/34641833/ed501dd9-6f35-401a-a8a8-99205ff2aeb3)

![no 6 v2](https://github.com/lodaogos/Jarkom-Modul-5-D28-2023/assets/34641833/eeafbd4f-b3c8-44d4-bf78-5b06ee8f8b9b)

### No. 7

Soal :

Karena terdapat 2 WebServer, kalian diminta agar setiap client yang mengakses Sein dengan Port 80 akan didistribusikan secara bergantian pada Sein dan Stark secara berurutan dan request dari client yang mengakses Stark dengan port 443 akan didistribusikan secara bergantian pada Sein dan Stark secara berurutan.

Jawab :

Untuk melakukan hal ini kita bisa melakukan prerouting. Kita bisa manipulasi paket network sehingga mengikuti yang diinginkan soal.

```
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 192.205.4.2 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.205.0.14:80
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 192.205.0.14 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.205.4.2:443
```

### No. 8

Soal : 

Karena berbeda koalisi politik, maka subnet dengan masyarakat yang berada pada Revolte dilarang keras mengakses WebServer hingga masa pencoblosan pemilu kepala suku 2024 berakhir. Masa pemilu (hingga pemungutan dan penghitungan suara selesai) kepala suku bersamaan dengan masa pemilu Presiden dan Wakil Presiden Indonesia 2024.

Jawab :

Pada router Fern bisa ditambahkan command berikut:

```
iptables -A FORWARD -a 192.205.0.1 -d 192.205.4.2/30 -m time --weekdays Mon,Tue,Wed,Thu,Fri,Sat,Sun -j REJECT
iptables -A FORWARD -a 192.205.0.1 -d 192.205.0.14/30 -m time --weekdays Mon,Tue,Wed,Thu,Fri,Sat,Sun -j REJECT
```

di mana 192.205.0.1 adalah Gateway dari Revolte. 192.205.4.2 IP dari Sein dan 192.205.0.14 IP dari Stark.

### No. 10

Soal :

Karena kepala suku ingin tau paket apa saja yang di-drop, maka di setiap node server dan router ditambahkan logging paket yang di-drop dengan standard syslog level.

Jawab :

Pada node server tambahkan:
```
iptables -A INPUT -p tcp -j LOG --log-level warning --log-prefix "Dropped Packets: "
iptables -A INPUT -p udp -j LOG --log-level warning --log-prefix "Dropped Packets: "
```

Pada router tambahkan:
```
iptables -A FORWARD -p tcp -j LOG --log-level warning --log-prefix "Dropped Packets: "
iptables -A FORWARD -p udp -j LOG --log-level warning --log-prefix "Dropped Packets: "
```

Dengan command tersebut, segala paket yang lewat akan direkam.
