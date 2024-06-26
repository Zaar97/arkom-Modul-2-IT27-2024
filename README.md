# Jarkom-Modul-2-IT27-2024

| Nama | NRP |
| ---------------------- | ---------- |
| Azzahra Sekar Rahmadina | 5027221035 |
| Zulfa Hafizh Kusuma | 5027221038 |

## List of Contents
- Official Report
  - [List of Contents](#list-of-contents)
  - [Topology](#topology)
  - [Network Configurations](#network-configurations)
  - [Prerequisite](#prerequisite)
- [Soal 1](#soal-1)
- [Soal 2](#soal-2)
- [Soal 3](#soal-3)
- [Soal 4](#soal-4)
- [Soal 5](#soal-5)
- [Soal 6](#soal-6)
- [Soal 7](#soal-7)
- [Soal 8](#soal-8)
- [Soal 9](#soal-9)
- [Soal 10](#soal-10)
- [Soal 11](#soal-11)
- [Soal 12](#soal-12)
- [Soal 13](#soal-13)
- [Soal 14](#soal-14)
- [Soal 15](#soal-15)
- [Soal 16](#soal-16)
- [Soal 17](#soal-17)
- [Soal 18](#soal-18)
- [Soal 19](#soal-19)
- [Soal 20](#soal-20)

## Topology
**Topology No. 2**
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/38703ba6-d5ed-4483-bb93-ec49c3f948b9)


## Network Configuration
- **Router**
  - Erangel
      ```bash
    auto eth0
    iface eth0 inet dhcp

    auto eth1
    iface eth1 inet static
        address 10.77.1.1
        netmask 255.255.255.0

    auto eth2
    iface eth2 inet static
        address 10.77.2.1
        netmask 255.255.255.0

    auto eth3
    iface eth3 inet static
        address 10.77.3.1
        netmask 255.255.255.0
      ```
- **Web Server**
  - Lipovka
    ```bash
    auto eth0
    iface eth0 inet static
        address 10.77.1.2
        netmask 255.255.255.0
        gateway 10.77.1.1
    ```
  - Stalber
    ```bash
    auto eth0
    iface eth0 inet static
        address 10.77.1.3
        netmask 255.255.255.0
        gateway 10.77.1.1
    ```
  - Severny
    ```bash
    auto eth0
    iface eth0 inet static
        address 10.77.1.4
        netmask 255.255.255.0
        gateway 10.77.1.1
    ```
- **Client**
  - Apartment
    ```bash
    auto eth0
    iface eth0 inet static
 	    address 10.77.2.5
 	    netmask 255.255.255.0
 	    gateway 10.77.2.1
    ```
  - Ruins
    ```bash
    auto eth0
    iface eth0 inet static
 	    address 10.77.2.2
 	    netmask 255.255.255.0
 	    gateway 10.77.2.1
    ```
- **Load Balancer**
  - Mylta
    ```bash
    auto eth0
    iface eth0 inet static
 	    address 10.77.2.4
 	    netmask 255.255.255.0
 	    gateway 10.77.2.1
    ```
- **DNS**
  - Slave Georgopol
    ```bash
    auto eth0
    iface eth0 inet static
 	    address 10.77.2.3
 	    netmask 255.255.255.0
 	    gateway 10.77.2.1
    ```
  - Master Pochinki
    ```bash
    auto eth0
    iface eth0 inet static
 	    address 10.77.3.2
 	    netmask 255.255.255.0
 	    gateway 10.77.3.1
    ```
- **Notes of Config**
  ```bash
  Erangel : 10.77.1.1 (Switch 1)
  Lipovka : 10.77.1.2
  Stalber : 10.77.1.3
  Severny : 10.77.1.4
  Erangel : 10.77.2.1 (Switch 2)
  Apartement : 10.77.2.2
  Ruins : 10.77.2.3
  Mylta : 10.77.2.4
  Georgopol : 10.77.2.5
  Erangel : 10.77.3.1
  Pochinki : 10.77.3.2
  ```

## Prerequisite
- Router (Erangel)
  ```bash
      iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.77.0.0/16
      echo 'nameserver 10.77.3.2' > /etc/resolv.conf
  ```
- Master & Slave
  ```bash
      echo 'nameserver 10.77.3.2' > /etc/resolv.conf
      apt-get update
      apt-get install bind9 -y   
  ```
- Client
    ```bash
      apt-get update -y
      apt-get install dnsutils -y
      apt-get install lynx -y

      echo nameserver 10.77.3.2 > /etc/resolv.conf
      echo nameserver 10.77.2.5 >> /etc/resolv.conf
  ```
  

## Soal 1

Sebelum memulai pengerjaan, langkah awal yang perlu dilakukan adalah melakukan setup. Tahap selanjutnya adalah melakukan pengujian terhadap semua node yang ada. Pada tahap ini, pengujian dilakukan pada kedua client, yakni `Apartment` dan `Ruins`.

### Script
```bash
ping google.com -c 5
```

### Result
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/65fb48b5-faa4-4cc6-9b50-2cad72d3c6a8)
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/1846abe1-8157-43f3-a9c5-0262fc83345a)

## Soal 2
> Karena para pasukan membutuhkan koordinasi untuk mengambil airdrop, maka buatlah sebuah domain yang mengarah ke Stalber dengan alamat airdrop.xxxx.com dengan alias www.airdrop.xxxx.com dimana xxxx merupakan kode kelompok. Kelompok IT27 : airdrop.it27.com

**Script**
Pada node DNS Master, kita perlu melakukan setup terlebih dahulu sebagai berikut

***Pochinki***
```bash
echo 'zone "airdrop.it27.com" {
        type master;
        file "/etc/bind/jarkom/airdrop.it27.com";
        allow-transfer { 10.77.1.3; }; // IP Stalber
};' > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/airdrop.it27.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     airdrop.it27.com. root.airdrop.it27.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      airdrop.it27.com.
@       IN      A       10.77.3.2     ; IP Pochinki
@       IN      A       10.77.1.3     ; IP Stalber
www     IN      CNAME   airdrop.it27.com.' > /etc/bind/jarkom/airdrop.it27.com

service bind9 restart
```
***Stelber***
```bash
echo nameserver 10.77.3.2 > /etc/resolv.conf
ping airdrop.it27.com -c 5
ping www.airdrop.it27.com -c 5
```

**Result**
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/2e84f8c7-7925-4855-b761-db009ec8c412)


## Soal 3
> Para pasukan juga perlu mengetahui mana titik yang sedang di bombardir artileri, sehingga dibutuhkan domain lain yaitu redzone.xxxx.com dengan alias www.redzone.com yang mengarah ke Severny

**Script**
Pada node DNS Master, kita perlu melakukan setup terlebih dahulu sebagai berikut
***Pochinki***
```bash
echo 'zone "airdrop.it27.com" {
        type master;
        file "/etc/bind/jarkom/airdrop.it27.com";
        allow-transfer { 10.77.1.3; }; // IP Stalber
};

zone "redzone.it27.com" {
        type master;
        file "/etc/bind/jarkom/redzone.it27.com";
        allow-transfer { 10.77.1.4; }; // IP Severny
};' > /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/redzone.it27.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it27.com. root.redzone.it27.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      redzone.it27.com.
@       IN      A       10.77.3.2     ; IP Pochinki
@       IN      A       10.77.1.4     ; IP Severny
www     IN      CNAME   redzone.it27.com.' > /etc/bind/jarkom/redzone.it27.com

service bind9 restart
```

***Severny***
```bash
echo nameserver 10.77.3.2 > /etc/resolv.conf
ping redzone.it27.com -c 5
ping www.redzone.it27.com -c 5
```

**Result**
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/0fafbec2-64c8-4187-a3c8-e135d5507cf6)

## Soal 4
> Markas pusat meminta dibuatnya domain khusus untuk menaruh informasi persenjataan dan suplai yang tersebar. Informasi persenjataan dan suplai tersebut mengarah ke Mylta dan domain yang ingin digunakan adalah loot.xxxx.com dengan alias www.loot.xxxx.com

**Script**
Pada node DNS Master, kita perlu melakukan setup terlebih dahulu sebagai berikut
***Pochinki***
```bash
echo 'zone "airdrop.it27.com" {
        type master;
        file "/etc/bind/jarkom/airdrop.it27.com";
        allow-transfer { 10.77.1.3; }; // IP Stalber
};

zone "loot.it27.com" {
        type master;
        file "/etc/bind/jarkom/loot.it27.com";
        allow-transfer { 10.77.2.4; }; // IP Mylta
};' > /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/loot.it27.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     loot.it27.com. root.loot.it27.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      loot.it27.com.
@       IN      A       10.77.3.2     ; IP Pochinki
@       IN      A       10.77.2.4     ; IP Mylta
www     IN      CNAME   loot.it27.com.' > /etc/bind/jarkom/loot.it27.com

service bind9 restart
```

***Mylta***
```bash
echo nameserver 10.77.3.2 > /etc/resolv.conf
ping loot.it27.com -c 5
ping www.loot.it27.com -c 5
```

**Result** <br>
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/136430870/9b6b8f9b-c453-44f2-ba6f-05be29f7fe73)


## Soal 5
> Pastikan domain-domain tersebut dapat diakses oleh seluruh komputer (client) yang berada di Erangel
Dapat dilakukakn dengan melakukan ping untuk setiap domain yang telah di buat sebelumnya pada console Erangel

**Result** 
![Screenshot 2024-05-03 153259](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/65c9592f-f963-461b-bcdd-cbc677a3bc8d)

![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/c561f2e1-85ce-4d5f-98fb-29bbc72d8a27)

## Soal 6
> Beberapa daerah memiliki keterbatasan yang menyebabkan hanya dapat mengakses domain secara langsung melalui alamat IP domain tersebut. Karena daerah tersebut tidak diketahui secara spesifik, pastikan semua komputer (client) dapat mengakses domain redzone.xxxx.com melalui alamat IP Severny

**Script**
Pada node DNS Master, kita perlu melakukan setup terlebih dahulu sebagai berikut
***Pochinki***
```bash
echo 'zone "redzone.it27.com" {
        type master;
        file "/etc/bind/jarkom/redzone.it27.com";
        
zone "1.77.10.in-addr.arpa"   {
        type master;
        file "/etc/bind/jarkom/1.77.10.in-addr.arpa";
        };' > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/1.77.10.in-addr.arpa

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it27.com. root.redzone.it27.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
1.77.10.in-addr.arpa       IN      NS      redzone.it27.com.
4                          IN      PTR     redzone.it27.com.' > /etc/bind/jarkom/1.77.10.in-addr.arpa

service bind9 restart
```
**Result**<br>
#### Ruins
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/136430870/83256982-05d6-47fa-ba6a-fee0e54cc025)
#### Apartments
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/136430870/3c9845e6-6f48-4f3a-84e2-11b38d9bc79f)


## Soal 7
> Akhir-akhir ini seringkali terjadi serangan siber ke DNS Server Utama, sebagai tindakan antisipasi kamu diperintahkan untuk membuat DNS Slave di Georgopol untuk semua domain yang sudah dibuat sebelumnya

**Script**
***Pochinki***
Pada `DNS Master` diperlukan setup `also-notify` dan `allow-transfer` agar memberikan izin kepada IP yang dituju

```bash
echo 'zone "airdrop.it27.com" {
        type master;
        also-notify { 10.77.2.3; };
        allow-transfer { 10.77.2.3; };
        file "/etc/bind/main/airdrop.it27.com";
};

zone "redzone.it27.com" {
        type master;
        also-notify { 10.77.2.3; };
        allow-transfer { 10.77.2.3; };
        file "/etc/bind/main/redzone.it27.com";
};

zone "loot.it27.com" {
        type master;
        also-notify { 10.77.2.3; };
        allow-transfer { 10.77.2.3; };
        file "/etc/bind/jarkom/loot.it27.com";
};' > /etc/bind/named.conf.local

service bind9 restart
service bind9 stop
```

***Georgopol***
```bash
echo 'zone "airdrop.it27.com" {
    type slave;
    masters { 10.77.3.2; }; 
    file "/var/lib/bind/airdrop.it27.com";
};

zone "redzone.it27.com" {
    type slave;
    masters { 10.77.3.2; }; 
    file "/var/lib/bind/redzone.it27.com";
};

zone "loot.it27.com" {
    type slave;
    masters { 10.77.3.2; }; 
    file "/var/lib/bind/loot.it27.com";
};' >> /etc/bind/named.conf.local

service bind9 restart
```

Setelah itu, untuk membuktikan Slave berhasil atau tidak, perlu ditambahkan IP Georgopol pada /etc/resolv.conf didalam Node Client
```bash
echo nameserver 10.77.2.3 > /etc/resolv.conf
```

Jika sudah selesai, pengujian dapat dilakukan dengan melakukan ping pada domain yang telah dibuat
```bash
ping airdrop.it27.com -c 5
ping www.redzone.it27.com -c 5
ping loot.it27.com -c 5
```

**Result**
Stop Bind9 Pochinki
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/843e3858-8201-4fe0-a1b9-0a1baf0b8fa4)

Start Bind9 Georgopol
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/82515a9e-b2c2-4556-8bf8-fa0991060f07)

Test Domain airdrop<br>
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/136430870/12ac679e-ef0a-4689-84c5-30c5dc40c5ed)


Test Domain redzone<br>
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/136430870/07d90b9c-21f8-41bb-9ddd-b11aefb38298)


Test Domain loot
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/21a66af5-02b2-497e-ab63-74da3d486387)


## Soal 8
> Kamu juga diperintahkan untuk membuat subdomain khusus melacak airdrop berisi peralatan medis dengan subdomain medkit.airdrop.xxxx.com yang mengarah ke Lipovka

```bash
echo 'zone "airdrop.it27.com" {
        type master;
        file "/etc/bind/jarkom/airdrop.it27.com";
        allow-transfer { 10.77.1.3; }; // IP Stalber
};' > /etc/bind/named.conf.local

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     airdrop.it27.com. root.airdrop.it27.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      airdrop.it27.com.
@       IN      A       10.77.3.2     ; IP Pochinki
@       IN      A       10.77.1.3     ; IP Stalber
www     IN      CNAME   airdrop.it27.com.
medkit  IN      A       10.77.1.2     ; IP Lipovka' > /etc/bind/jarkom/airdrop.it27.com

service bind9 restart
```

**Result**
Lakukan ping medkit.airdrop.it27.com pada client
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/128958228/c560953a-2d76-4529-815f-8073d8f740fe)


## Soal 9
> Terkadang red zone yang pada umumnya di bombardir artileri akan dijatuhi bom oleh pesawat tempur. Untuk melindungi warga, kita diperlukan untuk membuat sistem peringatan air raid dan memasukkannya ke domain siren.redzone.xxxx.com dalam folder siren dan pastikan dapat diakses secara mudah dengan menambahkan alias www.siren.redzone.xxxx.com dan mendelegasikan subdomain tersebut ke Georgopol dengan alamat IP menuju radar di Severny

**Script**

***Pochinki***
Pada DNS Master kita perlu menambahkan ns1     IN      A       10.77.2.3     ; IP Georgopol agar mendapatkan authoritative terhadap Georgopol. Kita juga perlu mengaktifkan allow-query { any; }; pada DNS Master
```bash
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it27.com. root.redzone.it27.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      redzone.it27.com.
@       IN      A       10.77.3.2     ; IP Pochinki
www     IN      CNAME   redzone.it27.com.
ns1     IN      A       10.77.2.3     ; IP Georgopol
siren   IN      NS      ns1' > /etc/bind/jarkom/redzone.it27.com

echo '
options {
        directory \"/var/cache/bind\";
        //dnssec-validation auto;

        allow-query { any; };
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
' > /etc/bind/named.conf.options

service bind9 restart
```

***Georgopol***

Pada `DNS Slave` Kita perlu untuk mengarahkan zone ke `DNS Master` agar authoritative tadi dapat jalan. Kita juga perlu mengaktifkan `allow-query { any; };` pada DNS Slave

```bash
echo "
options {
        directory \"/var/cache/bind\";
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
" > /etc/bind/named.conf.options

echo 'zone "siren.redzone.it27.com"{
        type master;
        file "/etc/bind/siren/siren.redzone.it27.com";
};'>> /etc/bind/named.conf.local

mkdir -p /etc/bind/siren

echo "
$TTL    604800
@       IN      SOA     siren.redzone.it27.com. root.siren.redzone.it27.com. (
                        2023101101      ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@               IN      NS      siren.redzone.it27.com.
@               IN      A       10.77.1.4       ; IP Severny
www             IN      CNAME   siren.redzone.it27.com.
" > /etc/bind/siren/siren.redzone.it27.com

service bind9 restart
```

## Soal 10
> Markas juga meminta catatan kapan saja pesawat tempur tersebut menjatuhkan bom, maka buatlah subdomain baru di subdomain siren yaitu log.siren.redzone.xxxx.com serta aliasnya www.log.siren.redzone.xxxx.com yang juga mengarah ke Severny

**Script**

***GEorgopol***
```bash
echo "
$TTL    604800
@       IN      SOA     siren.redzone.it27.com. root.siren.redzone.it27.com. (
                        2023101101      ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@               IN      NS      siren.redzone.it27.com.
@               IN      A       10.77.1.4       ; IP Severny
www             IN      CNAME   siren.redzone.it27.com.
log             IN      A       10.77.1.4      ;
www.log         IN      CNAME   log.siren.redzone.IT17.com.
" > /etc/bind/siren/siren.redzone.it27.com

service bind9 restart
```
## Soal 11
> Setelah pertempuran mereda, warga Erangel dapat kembali mengakses jaringan luar, tetapi hanya warga Pochinki saja yang dapat mengakses jaringan luar secara langsung. Buatlah konfigurasi agar warga Erangel yang berada diluar Pochinki dapat mengakses jaringan luar melalui DNS Server Pochinki

**Pochinki**
```bash 
options {
        directory "/var/cache/bind";

        forwarders {
            192.168.122.1
        }

        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```

## Soal 12
> Karena pusat ingin sebuah website yang ingin digunakan untuk memantau kondisi markas lainnya maka deploy lah webiste ini (cek resource yg lb) pada severny menggunakan apache

- Install Dependencies
```
apt-get update
apt-get install lynx
apt-get install apache2
apt-get install php
apt-get install libapache2-mod-php7.0
```
- Buat konfigurasi baru dengan mengcopy  /etc/apache2/sites-available/000-default.conf
```
cp 000-default.conf default-8080.conf
```
- Ganti port menjadi 8080
```
<VirtualHost *:8080>
```
- Tambahkan ke /etc/apache2/ports.conf
```
Listen 8080
```
- Aktifkan konfigurasi
```
a2ensite default-8080.conf
```
- Restart Apache Service
- Buat index.php dalam /var/www/html/
- Jalankan lynx http://10.77.1.4:8080 <br>
  ![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/136430870/093fd60e-e77d-4725-9486-27e895a947fd)

- RESULT <br>
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/136430870/616260cd-1a64-4772-8b09-f8e2382cd9f1)


## Soal 13
> Tapi pusat merasa tidak puas dengan performanya karena traffic yag tinggi maka pusat meminta kita memasang load balancer pada web nya, dengan Severny, Stalber, Lipovka sebagai worker dan Mylta sebagai Load Balancer menggunakan apache sebagai web server nya dan load balancernya

**Mylta**
- Aktifkan modul yang diperlukan, jangan lupa restart service apache dengan command `service apache2 restart`
```
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_balancer
a2enmod lbmethod_bytraffic
```
- Tambahkan konfigurasi berikut pada file /etc/apache2/sites-available/default-8080.conf
```
<VirtualHost *:8080>
        <proxy balancer://itbalancer>
                BalancerMember http://10.76.2.2:8080
                BalancerMember http://10.76.2.3:8080
                BalancerMember http://10.76.2.5:8080
                ProxySet lbmethod=bytraffic
        </proxy>
        ProxyPreserveHost On
        ProxyPass / balancer://itbalancer/
        ProxyPassReverse / balancer://itbalancer/
</VirtualHost>
```
- Restart lagi server apache

- Result <br>
![image](https://github.com/Zaar97/Jarkom-Modul-2-IT27-2024/assets/136430870/f940182a-7192-4487-8bd4-435be580f558)

## Soal 14
> Mereka juga belum merasa puas jadi pusat meminta agar web servernya dan load balancer nya diubah menjadi nginx

## Soal 16
> Karena dirasa kurang aman karena masih memakai IP markas ingin akses ke mylta memakai mylta.xxx.com dengan alias www.mylta.xxx.com (sesuai web server terbaik hasil analisis kalian)

## Soal 17
> Agar aman, buatlah konfigurasi agar mylta.xxx.com hanya dapat diakses melalui port 14000 dan 14400.

## Soal 18
> Apa bila ada yang mencoba mengakses IP mylta akan secara otomatis dialihkan ke www.mylta.xxx.com

## Soal 19
> Karena probset sudah kehabisan ide masuk ke salah satu worker buatkan akses direktori listing yang mengarah ke resource worker2

## Soal 20
> Worker tersebut harus dapat di akses dengan tamat.xxx.com dengan alias www.tamat.xxx.com

