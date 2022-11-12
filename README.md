# Jarkom-Modul-3-ITA04-2022
Nama Anggota | NRP
------------------- | --------------		
Nida'ul Faizah | 5027201064
Kevin Oktoaria | 5027201046
Najwa Amelia Qorry 'Aina | 5027201001

## Soal 1
Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server (1).

### Jawab
Pertama-tama kita perlu membuat Topologi sesuai soal, Topologi yang telah kami buat adalah sebagai berikut:

![Foto](./img/1a.PNG)

Setelah itu kita perlu melakukan konfigurasi pada setiap node, berikut adalah konfigurasi setiap node:

#### Konfigurasi Ostania
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.212.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.212.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.212.3.1
	netmask 255.255.255.0
```

#### Konfigurasi Client
Client SSS, Garden, NewstownCastle, KemonoPark
```
auto eth0
iface eth0 inet dhcp
```

#### Konfigurasi Client Eden
Pada CLient Eden yang akan mendaptkan alamat yang tetap 192.212.3.13 dilakukan konfigurasi:
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 36:2e:46:e7:f6:90
```

#### Konfigurasi Wise
```
auto eth0
iface eth0 inet static
	address 192.212.2.2
	netmask 255.255.255.0
	gateway 192.212.2.1
```

#### Konfigurasi Berlint
```
auto eth0
iface eth0 inet static
	address 192.212.2.3
	netmask 255.255.255.0
	gateway 192.212.2.1
```

#### Konfigurasi Westalis
```
auto eth0
iface eth0 inet static
	address 192.212.2.4
	netmask 255.255.255.0
	gateway 192.212.2.1
```

WISE sebagai DNS Server
Pada WISE, kami menjalankan beberapa command untuk menjadikannya DNS Server
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```

Westalis sebagai DHCP Server
Selanjutnya pada Westalis yang akan dijadikan DHCP Server dijalankan command berikut
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server -y
```

Berlint sebagai Proxy Server
Lalu pada Berlint sebagai Proxy Server dilakukan install squid
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install libapache2-mod-php7.0 -y
apt-get install squid -y
```

## Soal 2
Ostania sebagai DHCP Relay (2)

### Jawab
Untuk menjadikan Ostania sebagai DHCP Relay perlu dilakukan install isc-dhcp-relay
```
apt-get update
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.212.0.0/16
apt-get install isc-dhcp-relay -y
```
