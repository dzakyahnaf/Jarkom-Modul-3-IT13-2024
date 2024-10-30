# Jarkom-Modul-3-IT13-2024

## Anggota Kelompok IT 13

| Nama Lengkap          | NRP        |
| --------------------- | ---------- |
| Muhammad Dzaky Ahnaf  | 5027231039 |
| Muhammad Nafi Firdaus | 5027231045 |

## Pendahuluan

Pulau Paradis dan Marley, sama-sama menghadapi ancaman besar dari satu sama lain. Keduanya membangun infrastruktur pertahanan yang kuat untuk melindungi sistem vital mereka. Dengan strategi yang matang, mereka bersiap menghadapi serangan dan mempertahankan wilayah masing-masing.

**Bangsa Marley**, dipimpin oleh **Zeke**, telah mempersiapkan **Annie**, **Bertholdt**, dan **Reiner** untuk menyerang menggunakan **Laravel Worker**. Di sisi lain, **Klan Eldia** dari Paradis telah mempersiapkan **Armin**, **Eren**, dan **Mikasa** sebagai **PHP Worker** untuk mempertahankan pulau tersebut. **Warhammer** bertindak sebagai **Database Server**, sementara **Beast** dan **Colossal** sebagai **Load Balancer**.

## Buat Topologi

![alt text](TopologiModul3.png)

## Konfigurasi IP

## Paradis Router (DHCP Relay)

```sh
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.70.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.70.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.70.3.1
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 10.70.4.1
	netmask 255.255.255.0
```

## Tybur (DHCP Server)

```sh
auto eth0
iface eth0 inet static
	address 10.70.4.2
	netmask 255.255.255.0
	gateway 10.70.4.1
```

## Fritz (DNS Server)

```sh
auto eth0
iface eth0 inet static
	address 10.70.4.3
	netmask 255.255.255.0
	gateway 10.70.4.1
```

## Warhammer (Database Server)

```sh
auto eth0
iface eth0 inet static
	address 10.70.3.2
	netmask 255.255.255.0
	gateway 10.70.3.1
```

## Beast (Load Balancer Laravel)

```sh
auto eth0
iface eth0 inet static
	address 10.70.3.3
	netmask 255.255.255.0
	gateway 10.70.3.1
```

## Colossal (Load Balancer PHP)

```sh
auto eth0
iface eth0 inet static
	address 10.70.3.4
	netmask 255.255.255.0
	gateway 10.70.3.1
```

## Annie (Laravel Worker)

```sh
auto eth0
iface eth0 inet static
	address 10.70.1.2
	netmask 255.255.255.0
	gateway 10.70.1.1
```

## Bertholdt (Laravel Worker)

```sh
auto eth0
iface eth0 inet static
	address 10.70.1.3
	netmask 255.255.255.0
	gateway 10.70.1.1
```

## Reiner (Laravel Worker)

```sh
auto eth0
iface eth0 inet static
	address 10.70.1.4
	netmask 255.255.255.0
	gateway 10.70.1.1
```

## Armin (PHP Worker)

```sh
auto eth0
iface eth0 inet static
  address 10.70.2.2
  netmask 255.255.255.0
  gateway 10.70.2.1
```

## Eren (PHP Worker)

```sh
auto eth0
iface eth0 inet static
	address 10.70.2.3
	netmask 255.255.255.0
	gateway 10.70.2.1
```

## Mikasa (PHP Worker)

```sh
auto eth0
iface eth0 inet static
	address 10.70.2.4
	netmask 255.255.255.0
	gateway 10.70.2.1
```

## Zeke (Client)

```sh
auto eth0
iface eth0 inet dhcp
```

## Erwin (Client)

```sh
auto eth0
iface eth0 inet dhcp

```

## Script .bashrc

## Paradis Router (DHCP Relay)

```sh
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.70.0.0/16
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
```

## Soal 0

Pulau Paradis telah menjadi tempat yang damai selama 1000 tahun, namun kedamaian tersebut tidak bertahan selamanya. Perang antara kaum Marley dan Eldia telah mencapai puncak. Kaum Marley yang dipimpin oleh Zeke, me-register domain name **marley.yyy.com** untuk worker Laravel mengarah pada **Annie**. Namun ternyata tidak hanya kaum Marley saja yang berinisiasi, kaum Eldia ternyata sudah mendaftarkan domain name **eldia.yyy.com** untuk worker PHP **(0)** mengarah pada **Armin**.

### Konfigurasi pada Fritz (DNS Server) | fritz.sh

```sh
echo 'zone "marley.it13.com" {
    type master;
    file "/etc/bind/sites/marley.it13.com";
};
zone "eldia.it13.com" {
    type master;
    file "/etc/bind/sites/eldia.it13.com";
};' > /etc/bind/named.conf.local

mkdir -p /etc/bind/sites
cp /etc/bind/db.local /etc/bind/sites/marley.it13.com
cp /etc/bind/db.local /etc/bind/sites/eldia.it13.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     marley.it13.com. root.marley.it13.com. (
                        2024102301      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@       IN      NS      marley.it13.com.
@       IN      A       10.70.1.2    ; IP Annie
www     IN      CNAME   marley.it13.com.' > /etc/bind/sites/marley.it13.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     eldia.it13.com. root.eldia.it13.com. (
                            2024102301         ; Serial
                            604800              ; Refresh
                            86400              ; Retry
                            2419200              ; Expire
                            604800 )            ; Negative Cache TTL
;
@       IN      NS      eldia.it13.com.
@       IN      A       10.70.2.2    ; IP Armin
www     IN      CNAME   eldia.it13.com.' > /etc/bind/sites/eldia.it13.com

echo 'options {
    directory "/var/cache/bind";

    forwarders {
        192.168.122.1;
    };

    // dnssec-validation auto;

    allow-query { any; };
    auth-nxdomain no;    # conform to RFC1035
    listen-on-v6 { any; };
};' > /etc/bind/named.conf.options

service bind9 restart
```

## Soal 1

Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.

## Soal 2

Jauh sebelum perang dimulai, ternyata para keluarga bangsawan, Tybur dan Fritz, telah membuat kesepakatan sebagai berikut:

Semua Client harus menggunakan konfigurasi ip address dari keluarga Tybur (dhcp). Client yang melalui bangsa marley mendapatkan range IP dari [prefix IP].1.05 - [prefix IP].1.25 dan [prefix IP].1.50 - [prefix IP].1.100 (2)

### Konfigurasi pada Tybur (DHCP Server) | tybur.sh

```sh
echo '
subnet 10.70.1.0 netmask 255.255.255.0 {
range 10.70.1.5 10.70.1.25;
range 10.70.1.50 10.70.1.100;
}
' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart
```

## Soal 3

Client yang melalui bangsa eldia mendapatkan range IP dari [prefix IP].2.09 - [prefix IP].2.27 dan [prefix IP].2 .81 - [prefix IP].2.243

### Konfigurasi pada Tybur (DHCP Server) | tybur.sh

```sh
echo '
subnet 10.70.1.0 netmask 255.255.255.0 {
	range 10.70.1.5 10.70.1.25;
	range 10.70.1.50 10.70.1.100;
}

subnet 10.70.2.0 netmask 255.255.255.0 {
	range 10.70.2.09 10.70.2.27;
	range 10.70.2.81 10.70.2.243;
}
' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart
```

## Soal 4

Client mendapatkan DNS dari keluarga Fritz dan dapat terhubung dengan internet melalui DNS tersebut.

### Konfigurasi pada Tybur (DHCP Server) | tybur.sh

```sh
echo '
INTERFACESv4="eth0"
INTERFACESv6=""
' > /etc/default/isc-dhcp-server

echo '
subnet 10.70.1.0 netmask 255.255.255.0 {
	range 10.70.1.05 10.70.1.25;
	range 10.70.1.50 10.70.1.100;
	option routers 10.70.1.1;
	option broadcast-address 10.70.1.255;
	option domain-name-servers 10.70.4.3;
}

subnet 10.70.2.0 netmask 255.255.255.0 {
	range 10.70.2.09 10.70.2.27;
	range 10.70.2.81 10.70.2.243;
	option routers 10.70.2.1;
	option broadcast-address 10.70.1.255;
	option domain-name-servers 10.70.4.3;
}

subnet 10.70.3.0 netmask 255.255.255.0 {
	option routers 10.70.3.1;
}

subnet 10.70.4.0 netmask 255.255.255.0 {
	option routers 10.70.4.1;
}
' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart
```

### Konfigurasi pada Paradis (DHCP Relay) | paradis.sh

```sh
echo '
SERVERS="10.70.4.2"
INTERFACES="eth1 eth2 eth3 eth4"
OPTIONS=""
' > /etc/default/isc-dhcp-relay

echo '
net.ipv4.ip_forward=1
' > /etc/sysctl.conf

service isc-dhcp-relay restart
```

## Soal 5

Dikarenakan keluarga Tybur tidak menyukai kaum eldia, maka mereka hanya meminjamkan ip address ke kaum eldia selama 6 menit. Namun untuk kaum marley, keluarga Tybur meminjamkan ip address selama 30 menit. Waktu maksimal dialokasikan untuk peminjaman alamat IP selama 87 menit.

- 6 menit \* 60 detik = 360 detik
- 30 menit \* 60 detik = 1800 detik
- 87 menit \* 60 detik = 5220 detik

### Konfigurasi pada Tybur (DHCP Server) | tybur.sh

```sh
echo '
INTERFACESv4="eth0"
INTERFACESv6=""
' > /etc/default/isc-dhcp-server

echo '
subnet 10.70.1.0 netmask 255.255.255.0 {
	range 10.70.1.5 10.70.1.25;
	range 10.70.1.50 10.70.1.100;
	option routers 10.70.1.1;
	option broadcast-address 10.70.1.255;
	option domain-name-servers 10.70.4.3;
	default-lease-time 360;
	max-lease-time 5220;
}

subnet 10.70.2.0 netmask 255.255.255.0 {
	range 10.70.2.9 10.70.2.27;
	range 10.70.2.81 10.70.2.243;
	option routers 10.70.2.1;
	option broadcast-address 10.70.1.255;
	option domain-name-servers 10.70.4.3;
	default-lease-time 1800;
	max-lease-time 5220;
}

subnet 10.70.3.0 netmask 255.255.255.0 {
	option routers 10.70.3.1;
}

subnet 10.70.4.0 netmask 255.255.255.0 {
	option routers 10.70.4.1;
}
' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart
```

### Bukti Client Connect
