# Jarkom_Modul3_Lapres_C02
Laporan Resmi Modul 2 Praktikum Jaringan Komputer
## Kelompok C02
* Aulia Ihza Hendradi (05111840000089)
* Excel Deo Cornelius (05111840000117)


## Soal 1

Sintaks untuk **topo.sh**

```
# Switch
uml_switch -unix switch1 > /dev/null < /dev/null &
uml_switch -unix switch2 > /dev/null < /dev/null &
uml_switch -unix switch3 > /dev/null < /dev/null &

# Router
xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.76.13 eth1=daemon,,,switch1 eth2=daemon,,,switch3 eth3=daemon,,,switch2 mem=256M &

# Server
xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=160M &
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
xterm -T TUBAN -e linux ubd0=TUBAN,jarkom umid=TUBAN eth0=daemon,,,switch2 mem=128M &

# Klien 
xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch1 mem=64M &
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch1 mem=64M &
xterm -T BANYUWANGI -e linux ubd0=BANYUWANGI,jarkom umid=BANYUWANGI eth0=daemon,,,switch3 mem=64M &
xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch3 mem=64M &
```

![bonus2](https://user-images.githubusercontent.com/52096462/100544474-6f595a80-3288-11eb-923a-72e75be5c69d.png)

Lalu  jalankan `bash topo.sh` pada *putty* dan masukkan *username* dan *password* default. Pada router **SURABAYA** lakukan setting `sysctl` dengan mengetikkan perintah `nano /etc/sysctl.conf`, dan tambahkan `net.ipv4.conf.all.accept_source_route = 1`.

![1](https://user-images.githubusercontent.com/52096462/100542193-4bdbe300-327b-11eb-99d9-0acfe3607066.PNG)

Lalu setting IP pada setiap *interfaces* uml dengan mengetikkan `nano /etc/network/interfaces` sebagai berikut:

**SURABAYA (Sebagai Router)**

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.76.14
netmask 255.255.255.252
gateway 10.151.76.13

auto eth1
iface eth1 inet static
address 192.168.0.1
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 192.168.1.1
netmask 255.255.255.0

auto eth3
iface eth3 inet static
address 10.151.77.25
netmask 255.255.255.248
```

![2](https://user-images.githubusercontent.com/52096462/100542194-4d0d1000-327b-11eb-9e6f-baf380c35400.PNG)

**MALANG (Sebagai DNS Server)**

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.77.26
netmask 255.255.255.248
gateway 10.151.77.25
```

![3](https://user-images.githubusercontent.com/52096462/100542196-4da5a680-327b-11eb-8675-197792885ef0.PNG)

**MOJOKERTO (Sebagai Web Server)**

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.77.27
netmask 255.255.255.248
gateway 10.151.77.25
```

![4](https://user-images.githubusercontent.com/52096462/100542197-4e3e3d00-327b-11eb-8a0d-2daf4fb54b36.PNG)

**TUBAN (Sebagai Web Server)**

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.77.28
netmask 255.255.255.248
gateway 10.151.77.25
```

![5](https://user-images.githubusercontent.com/52096462/100542198-4ed6d380-327b-11eb-8ac0-7746fdd14e74.PNG)

**SIDOARJO (Sebagai Klien)**

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.2
netmask 255.255.255.0
gateway 192.168.0.1
```

![6](https://user-images.githubusercontent.com/52096462/100542199-4f6f6a00-327b-11eb-8096-36db5a140aa7.PNG)

**GRESIK (Sebagai Klien)**

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.3
netmask 255.255.255.0
gateway 192.168.0.1
```

![7](https://user-images.githubusercontent.com/52096462/100542201-50080080-327b-11eb-9889-43d272797259.PNG)

**BANYUWANGI (Sebagai Klien)**

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.1.2
netmask 255.255.255.0
gateway 192.168.1.1
```

![8](https://user-images.githubusercontent.com/52096462/100542202-50080080-327b-11eb-96a2-5af8c782975e.PNG)

**MADIUN (Sebagai Klien)**

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.1.3
netmask 255.255.255.0
gateway 192.168.1.1
```

![9](https://user-images.githubusercontent.com/52096462/100542203-50a09700-327b-11eb-9390-368b653f1da7.PNG)

Restart network dengan mengetikkan `service networking restart` di setiap UML. Ketikkan `iptables –t nat –A POSTROUTING –o eth0 –j MASQUERADE –s 192.168.0.0/16` dan `iptables –t nat –A POSTROUTING –o eth0 –j MASQUERADE –s 192.168.1.0/16` pada router **SURABAYA**. IP Tables dapat dimasukkan ke dalam *script*, dalam hal ini, nama script berupa `table.sh`.

## Soal 2

* Meng-install `dhcp server` di **TUBAN** dengan cara `apt-get update` lalu `apt-get install isc-dhcp-server`. Setelah itu setting pada **TUBAN** dengan mengetikkan

```
nano /etc/default/isc-dhcp-server
```

![10](https://user-images.githubusercontent.com/52096462/100542204-51392d80-327b-11eb-9b32-6c3f7bc351aa.PNG)

* Meng-install `dhcp relay` di **SURABAYA** dengan cara `apt-get update` lalu `apt-get install isc-dhcp-relay`. Setelah itu setting pada **SURABAYA** dengan mengetikkan

```
nano /etc/default/isc-dhcp-relay
```

![11](https://user-images.githubusercontent.com/52096462/100542205-51d1c400-327b-11eb-8928-79c6ae2339a7.PNG)

Lalu pastikan seluruh *client* menggunakan konfigurasi IP DHCP.

![12](https://user-images.githubusercontent.com/52096462/100542207-526a5a80-327b-11eb-9947-c85d8676b1b7.PNG)
![13](https://user-images.githubusercontent.com/52096462/100542208-5302f100-327b-11eb-8342-48900f4987bf.PNG)
![14](https://user-images.githubusercontent.com/52096462/100542210-5302f100-327b-11eb-80cc-a543f69dd2fb.PNG)
![15](https://user-images.githubusercontent.com/52096462/100542212-539b8780-327b-11eb-97ef-04c5f28f638e.PNG)

## Soal 3

Pada UML **TUBAN** buka file *dhcpd.conf* dengan mengetikkan `nano /etc/dhcp/dhcpd.conf`, dan tambahkan range pada script sesuai soal

![16](https://user-images.githubusercontent.com/52096462/100542214-54341e00-327b-11eb-910a-d3096a2cace9.PNG)

Lalu pada client subnet 1 dapat mencoba `ifconfig` untuk cek IP

![17](https://user-images.githubusercontent.com/52096462/100542215-54ccb480-327b-11eb-922c-27037870dfe0.PNG)
![18](https://user-images.githubusercontent.com/52096462/100542216-55654b00-327b-11eb-94c5-da294c8ec189.PNG)

## Soal 4

Pada UML **TUBAN** buka file *dhcpd.conf* dengan menetikkan `nano /etc/dhcp/dhcpd.conf`, dan tambahkan range pada script sesuai soal

![19](https://user-images.githubusercontent.com/52096462/100542217-55fde180-327b-11eb-857e-ea86fc6d43ff.PNG)

Lalu pada client subnet 3 dapat mencoba `ifconfig` untuk cek IP

![20](https://user-images.githubusercontent.com/52096462/100542218-56967800-327b-11eb-9910-dac3431ffa57.PNG)
![21](https://user-images.githubusercontent.com/52096462/100542219-56967800-327b-11eb-8123-efe26e1dfc2d.PNG)

## Soal 5

Pada UML **TUBAN** buka file *dhcpd.conf* dengan menetikkan `nano /etc/dhcp/dhcpd.conf`, dan tambahkan pada script di bagian `option  domain-name-servers` sesuai soal yaitu **DNS MALANG** dan **DNS 202.46.129.2**

```
option domain-name-servers 10.151.77.26, 202.46.129.2;
```

![22](https://user-images.githubusercontent.com/52096462/100542221-572f0e80-327b-11eb-9e30-1f040bc1e232.PNG)

Setlah itu lakukan restart dhcp-relay pada **SURABAYA** dengan mengetikkan 

```
service isc-dhcp-relay restart
```

dan dhcp-server pada **TUBAN** dengan mengetikkan 

```
service isc-dhcp-server restart
```

Ketikkan `service networking restart` pada setiap UML Client dan masukkan `ifconfig`. Periksa /etc/resolv.conf dengan menggunakan perintah

```
cat /etc/resolv.conf
```


![23](https://user-images.githubusercontent.com/52096462/100542222-57c7a500-327b-11eb-9b28-c015a9cb7281.PNG)
![24](https://user-images.githubusercontent.com/52096462/100542223-58603b80-327b-11eb-899f-5effad83e3d5.PNG)
![25](https://user-images.githubusercontent.com/52096462/100542224-58f8d200-327b-11eb-9ef9-e739c33dfa13.PNG)
![26](https://user-images.githubusercontent.com/52096462/100542225-59916880-327b-11eb-8a46-fee135a902b9.PNG)

## Soal 6

Pada UML **TUBAN** buka file *dhcpd.conf* dengan mengetikkan `nano /etc/dhcp/dhcpd.conf`, dan tambahkan pada script di bagian `default-lease-time` sesuai soal yaitu client di subnet 1 selama 5 menit dan client di subnet 3 selama 10 menit.

* 5 menit = `60 s * 5` = 300
* 10 menit = `60 s * 10` = 600

![27 tambahan](https://user-images.githubusercontent.com/52096462/100542227-59916880-327b-11eb-9bee-f7d1f14ca0a8.PNG)
![27](https://user-images.githubusercontent.com/52096462/100542228-5a29ff00-327b-11eb-93e1-efde92b92656.PNG)

## Proxy (Soal 7 - 12)

* Konfigurasi `janganlupa-ta.yyy.pw` menjadi `janganlupa-ta.c02.pw` sesuai dengan nama kelompok.
* Konfigurasi `userta_yyy` menjadi `userta_c02` sesuai dengan nama kelompok.
* Konfigurasi ` inipassw0rdta_yyy` menjadi ` inipassw0rdta_c02` sesuai dengan nama kelompok.
* Susunan file config keseluruhan

![28](https://user-images.githubusercontent.com/52096462/100542231-5b5b2c00-327b-11eb-9470-3397a0313c91.PNG)
![28 tambahan](https://user-images.githubusercontent.com/52096462/100542229-5ac29580-327b-11eb-8bfc-6cf82a920f4f.PNG)

## Soal 7

Meng-install `squid` di **MOJOKERTO** dengan cara `apt-get update` lalu 

```
apt-get install isc-dhcp-server
```

dan meng-install `apache2-utils`

```
apt-get install apache2-utils
```

Backup terlebih dahulu file konfigurasi default yang disediakan Squid.

```
mv /etc/squid/squid.conf /etc/squid/squid.conf.bak
```

Buat konfigurasi baru dengan mengetikkan

```
nano /etc/squid/squid.conf
```

Kemudian, pada file config yang baru, ketikkan script:

```
http_port 8080
visible_hostname mojokerto
```

Setelah itu buat user dengan nama **userta_c02** dan password  pada **MOJOKERTO** dengan mengetikkan:

```
htpasswd -c /etc/squid/passwd userta_c02
```

Edit konfigurasi squid menjadi:

```
http_port 8080
visible_hostname mojokerto

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS
```

Restart squid `service squid restart`, dan berikutnya ubah **proxy** pada *web browser* ataupun *OS*

![29](https://user-images.githubusercontent.com/52096462/100542232-5bf3c280-327b-11eb-9942-39351e91f40b.PNG)

Selanjutnya dapat mencoba untuk mengakses situs tertentu seperti `google.com`

![30](https://user-images.githubusercontent.com/52096462/100542233-5c8c5900-327b-11eb-9e05-f673b6b5b92e.PNG)

## Soal 8

Pada UML **MOJOKERTO** buat file konfigurasi dengan mengetikkan

```
nano /etc/squid/acl.conf
```
dan tambahkan

```
acl AW1 time TW 13:00-18:00
```

![31](https://user-images.githubusercontent.com/52096462/100542235-5d24ef80-327b-11eb-902f-338b5c9204fc.PNG)

Buka kembali file `squid.conf` dengan mengetikkan

```
include /etc/squid/acl.conf

http_port 8080
http_access allow USERS AW1
http_access deny all
visible_hostname mojokerto
```

Simpan file tersebut. Kemudian `service squid restart`. Lalu conba akses situs apapun, contoh `monta.if.its.ac.id`, jika sesuai dengan jam yang ditentukan, maka situs `detik.com` akan terbuka, jika tidak sesuai dengan jam yang telah ditentukan, maka situs tersebut tidak dapat diakses.

![32](https://user-images.githubusercontent.com/52096462/100542236-5e561c80-327b-11eb-9a71-6da471f18e15.PNG)

## Soal 9

Sama halnya dengan nomor 8. Pada UML **MOJOKERTO** buat file konfigurasi dengan mengetikkan

```
nano /etc/squid/acl.conf
```
dan tambahkan

```
acl AW2 time TWH 21:00-23:59
acl AW3 time WHF 00:00-09:00
```

![33](https://user-images.githubusercontent.com/52096462/100542239-61e9a380-327b-11eb-9421-6bfca26ad410.PNG)

Buka kembali file `squid.conf` dengan mengetikkan

```
include /etc/squid/acl.conf

http_port 8080
http_access allow USERS AW2
http_access allow USERS AW3
http_access deny all
visible_hostname mojokerto
```

Simpan file tersebut. Kemudian `service squid restart`. Lalu coba akses situs apapun, contoh `monta.if.its.ac.id`, jika sesuai dengan jam yang ditentukan, maka situs `detik.com` akan terbuka, jika tidak sesuai dengan jam yang telah ditentukan, maka situs tersebut tidak dapat diakses.

![34](https://user-images.githubusercontent.com/52096462/100542240-62823a00-327b-11eb-84a1-f4a7f98a1c96.PNG)


## Soal 10

Buka file `restrict-sites.acl` dengan mengetikkan

```
nano /etc/squid/restrict-sites.acl
```

Lalu tambahkan pada file tersebut `google.com`. hal ini dimaksudkan dengan soal yaitu jika *user* mengetikkan `http://google.com` maka akan ter-*redirect* ke `monta.if.its.ac.id`.

![35](https://user-images.githubusercontent.com/52096462/100542241-6615c100-327b-11eb-930f-fc5f34feff0a.PNG)

Buka file `squid.conf` dengan mengetikkan `nano /etc/squid/squid.conf`, dan tambahkan script berikut:

```
acl BLACKLISTS dstdomain "/etc/squid/restrict-sites.acl"
deny_info http://monta.if.its.ac.id/ BLACKLISTS
http_access deny BLACKLISTS
http_access allow AW1 USERS !BLACKLISTS
http_access allow AW2 USERS !BLACKLISTS
http_access allow AW3 USERS !BLACKLISTS
http_access deny all
```

Restart squid `service squid restart`, lalu masukkan `http://google.com` pada search bar, maka akan teralihkan ke `monta.if.its.ac.id`. (Berlaku jika sesuai dengan jam yang telah ditetapkan).

![36](https://user-images.githubusercontent.com/52096462/100542243-66ae5780-327b-11eb-9ad9-6cd4a2254811.PNG)

## Soal 11

Buka folder `cd /usr/share/squid/errors/en` dan download error page dengan cara `wget 10.151.36.202/ERR_ACCESS_DENIED`. Lalu tampilannya akan sebagai berikut dengan `ls`

![37](https://user-images.githubusercontent.com/52096462/100542244-6746ee00-327b-11eb-9997-b262d8915911.PNG)

Ganti file `ERR_ACCESSS_DENIED` dan rename file yang baru saja didownload yaitu `ERR_ACCESSS_DENIED.1` menjadi `ERR_ACCESSS_DENIED`, dengan cara:

```
rm ERR_ACCESSS_DENIED
mv ERR_ACCESSS_DENIED.1 ERR_ACCESSS_DENIED
```

Buka kembali konfigurasi `squid.conf` dengan mengetikkan `nano /etc/squid/squid.conf`. Ubah file konfigurasi squid menjadi seperti berikut ini. Untuk menambah situs sampel yang diblokir didalam squid.conf dengan menambahkan line 

```
acl blek dstdomain ajk.if.its.ac.id
http_access allow AW1 USERS !BLACKLISTS !blek
http_access allow AW2 USERS !BLACKLISTS !blek
http_access allow AW3 USERS !BLACKLISTS !blek
http_access deny all
```

![38](https://user-images.githubusercontent.com/52096462/100542245-67df8480-327b-11eb-89b5-1ac7882118ed.PNG)



Restart squid `service squid restart` dan jalankan alamat URL yang telah ditambahkan sebelumnya diconfig yaitu `ajk.if.its.ac.id`.

![39dan40digabung](https://user-images.githubusercontent.com/52096462/100542246-68781b00-327b-11eb-9b47-36c6fc42bb87.PNG)

## Soal 12

Buka **MALANG** dan update package lists dengan menjalankan command:

```
apt-get update
```

Setelah melakukan update silahkan install aplikasi `bind9` pada **MALANG** dengan perintah:

```
apt-get install bind9 -y
```

Lakukan perintah pada **MALANG**. Isikan seperti berikut:

```
nano /etc/bind/named.conf.local
```

Isikan konfigurasi domain janganlupa-ta.c02.pw sesuai dengan syntax berikut:
```
zone "janganlupa-ta.c02.pw" {
 	type master;
	file "/etc/bind/jarkom/janganlupa-ta.c02.pw";
};
```
![41](https://user-images.githubusercontent.com/52096462/100542249-6c0ba200-327b-11eb-85c9-bf7de7e4bbb9.PNG)

Buat folder jarkom di dalam `/etc/bind`
```
mkdir /etc/bind/jarkom
```

Copykan file db.local pada path /etc/bind ke dalam folder jarkom yang baru saja dibuat dan ubah namanya menjadi janganlupa-ta.c02.pw
```
cp /etc/bind/db.local /etc/bind/jarkom/janganlupa-ta.c02.pw
```

Kemudian buka file janganlupa-ta.c02.pw dan edit seperti gambar berikut dengan IP MOJOKERTO masing-masing kelompok:
```
nano /etc/bind/jarkom/janganlupa-ta.c02.pw
```

![42](https://user-images.githubusercontent.com/52096462/100542250-6d3ccf00-327b-11eb-8b7a-17300f5354ab.PNG)

Restart bind9 dengan perintah
```
service bind9 restart
```

Ganti proxy pada *web browser* atau *OS* yang sebelumnya `10.151.77.27` menjadi `janganlupa-ta.c02.pw`, dengan port yaitu 8080.

![43](https://user-images.githubusercontent.com/52096462/100542251-6dd56580-327b-11eb-8f2b-388ddf2ccd7d.PNG)

Lalu coba periksa proxy yang telah diubah tersebut, contohnya dengan mengakses *website* apapun, seperti `yahoo.com`

![44](https://user-images.githubusercontent.com/52096462/100542253-6e6dfc00-327b-11eb-9980-f1cbb9c60fef.PNG)

atau lakukan `ping janganlupa-ta.c02.pw` pada *Command Prompt*

![bonus](https://user-images.githubusercontent.com/52096462/100544472-6d8f9700-3288-11eb-84c3-67f099b6e432.png)

