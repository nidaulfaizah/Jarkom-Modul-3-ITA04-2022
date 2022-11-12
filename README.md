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

## Soal 3
Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155 (3)

### Jawab
Pertama, perlu dilakukan konfigurasi pada Ostania sebagai DHCP Relay dengan melakukan edit file /etc/default/isc-dhcp-relay dengan konfigurasi berikut

```
# What servers should the DHCP relay forward requests to?
SERVERS=\"192.212.2.4\"
# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES=\"eth1 eth3 eth2\"
# Additional options that are passed to the DHCP relay daemon?
OPTIONS=\"\"
```

Selanjutnya pada Westalis sebagai DHCP Server, dilakukan konfigurasi pada file /etc/default/isc-dhcp-server sebagai berikut:

```
# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
#       Separate multiple interfaces with spaces, e.g. \"eth0 eth1\".
INTERFACES=\"eth0\"
```

Kemudian dilakukan restart DHCP server dengan `service isc-dhcp-server restart`. Setelah itu dilakukan konfigurasi untuk rentang IP yang akan diberikan pada file /etc/dhcp/dhcpd.conf dengan menulis seperti dibawah ini:

```
subnet 192.212.2.0 netmask 255.255.255.0 {
}
subnet 192.212.1.0 netmask 255.255.255.0 {
    range  192.212.1.50 192.212.1.88;
    range  192.212.1.120 192.212.1.155;
    option routers 192.212.1.1;
    option broadcast-address 192.212.1.255;
    option domain-name-servers 192.212.2.2;
    default-lease-time 300;
    max-lease-time 6900;
}
```

## Soal 4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85 (4)

### Jawab

Sama seperti sebelumnya, tambahkan konfigurasi untuk rentang IP yang akan diberikan pada file /etc/dhcp/dhcpd.conf dengan konfigurasi pada berikut ini:

```
subnet 192.212.3.0 netmask 255.255.255.0 {
    range  192.212.3.60 192.212.3.85;
    option routers 192.212.3.1;
    option broadcast-address 192.212.3.255;
    option domain-name-servers 192.212.2.2;
    default-lease-time 600;
    max-lease-time 6900;
}
```
