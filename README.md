# Laporan Resmi Praktikum Jaringan Komputer Kelompok T05
## Dibuat Oleh : 
- M. Mikail Dwi K. – 05311840000028
- Dimas Pramudya H. – 05311840000037

## Teknis Pengerjaan
1. Perhatikan penempatan konfigurasi ```IPTABLES``` yang telah ditentukan pada soal. Apabila tidak ditetapkan di soal, maka diperbolehkan mengatur ```IPTABLES``` dimana saja.

## Soal
Setelah kalian mempelajari semua modul yang telah diberikan, Bibah ingin meminta bantuan untuk
terakhir kalinya kepada kalian. Dan kalian dengan senang hati mau membantu Bibah.

#### (A) Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Bibah seperti dibawah ini :
![TOPOLOGI SHIFT](https://user-images.githubusercontent.com/55182072/103220392-bf444380-4952-11eb-9726-9f84645d1aa1.PNG)

**Keterangan :**

- **SURABAYA** diberikan ```IP TUNTAP```
- **MALANG** merupakan DNS Server diberikan ```IP DMZ```
- **MOJOKERTO** merupakan ```DHCP Server``` diberikan ```IP DMZ```
- **MADIUN** dan **PROBOLINGGO** merupakan ```WEB Server```
- Setiap Server diberikan memory sebesar ```128M```
- Client dan Router diberikan memori sebesar ```96M```
- Jumlah host pada subnet **SIDOARJO** ```200 Host```
- Jumlah host pada subnet **GRESIK** ```210 Host```

**(B) karena kalian telah mempelajari Subnetting dan Routing, Bibah meminta kalian untuk membuat
topologi tersebut menggunakan teknik CIDR atau VLSM. Setelah melakukan subnetting,**

**(C) kalian juga diharuskan melakukan routing agar setiap perangkat pada jaringan tersebut dapat terhubung.**

**(D) Tugas berikutnya adalah memberikan ip pada subnet SIDOARJO dan GRESIK secara dinamis menggunakan bantuan DHCP SERVER (Selain subnet tersebut menggunakan ip static). Kemudian kalian mengingat bahwa kalian harus setting DHCP RELAY pada router yang menghubungkannya, seperti yang kalian telah pelajari di masa lalu.**

**(1) Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi
SURABAYA menggunakan iptables, namun Bibah tidak ingin kalian menggunakan
MASQUERADE.**

**(2) Kalian diminta untuk mendrop semua akses SSH dari luar Topologi (UML) Kalian pada server
yang memiliki ip DMZ (DHCP dan DNS SERVER) pada SURABAYA demi menjaga keamanan.**

**(3) Karena tim kalian maksimal terdiri dari 3 orang, Bibah meminta kalian untuk membatasi DHCP
dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari
mana saja menggunakan iptables pada masing masing server, selebihnya akan di DROP.**

Kemudian kalian diminta untuk membatasi akses ke MALANG yang berasal dari SUBNET
SIDOARJO dan SUBNET GRESIK dengan peraturan sebagai berikut:

● **(4) Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin
sampai Jumat.**

● **(5) Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap
harinya. Selain itu paket akan di REJECT.**

Karena kita memiliki 2 buah WEB Server, 

**(6) Bibah ingin SURABAYA disetting sehingga setiap
request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada
PROBOLINGGO port 80 dan MADIUN port 80.**

**(7) Bibah ingin agar semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap
UML yang memiliki aturan drop.
Bibah berterima kasih kepada kalian karena telah mau membantunya. Bibah juga mengingatkan agar
semua aturan iptables harus disimpan pada sistem atau paling tidak kalian menyediakan script sebagai
backup.**

## Jawaban :
**A).** Membuat file topologi baru dengan mengketikkan ```nano topologi.sh``` seperti gambar berikut

![NANO TOPOLOGI SH](https://user-images.githubusercontent.com/55182072/103221597-9c675e80-4955-11eb-8a37-0e883d85b092.PNG)

**B).** Teknik ```CIDR (Classless Inter Domain Routing)```

- Buat pembagian subnet kemudian hitung subnet pada tiap bagian sebagai gambar dibawah ini :
![PEMB SUBNET](https://user-images.githubusercontent.com/55182072/103223138-2f08fd00-4958-11eb-9d5d-e25f16df5264.PNG)

- Hitung IP Address yang dibutuhkan (Jumlah Host, Router, dan Server). Pada soal ini ada kurang lebih 422 IP Address maka subnet yang dipakai untuk membuat pohon IP yaitu subnet 21.

![PENGHITUNGAN IP ADDR](https://user-images.githubusercontent.com/55182072/103221953-5eb70580-4956-11eb-90fe-6784fc491bc4.PNG)

- Buat pohon IP berdasarkan pembagian subnet yang ada pada topologi seperti gambar dibawah ini:
![POHON IP](https://user-images.githubusercontent.com/55182072/103223942-a3906b80-4959-11eb-8ebe-1042c8cc94c8.PNG)

**1).** Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi
SURABAYA menggunakan iptables, namun Bibah tidak ingin kalian menggunakan
MASQUERADE. Setting pada UML Surabaya dengan syntax sebagai berikut :

```iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j SNAT --to-source 10.151.72.85```
![nomer 1](https://user-images.githubusercontent.com/55182072/103225439-7e512c80-495c-11eb-8d11-3e6791eff497.PNG)


**2).** Kalian diminta untuk mendrop semua akses SSH dari luar Topologi (UML) Kalian pada server
yang memiliki ip DMZ (DHCP dan DNS SERVER) pada SURABAYA demi menjaga keamanan. Pada UML Surabaya, ketikkan syntax berikut :

```iptables -A FORWARD -d 10.151.73.168/29 -s 10.151.72.85 -p tcp --dport 22 -j DROP```
![nomer 2](https://user-images.githubusercontent.com/55182072/103225894-82ca1500-495d-11eb-952b-2962301c3c0f.PNG)

**3).** Karena tim kalian maksimal terdiri dari 3 orang, Bibah meminta kalian untuk membatasi DHCP
dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari
mana saja menggunakan iptables pada masing masing server, selebihnya akan di DROP.

```iptables -A INPUT -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP```

**4).** Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin
sampai Jumat.

```iptables -A INPUT -s 192.168.0.0/24 -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
iptables -A INPUT -s 192.168.0.0/24 -m time --timestart 17:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
iptables -A INPUT -s 192.168.0.0/24 -m time --weekdays Sat,Sun -j REJECT
```
**5).** Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap
harinya. Selain itu paket akan di REJECT.

```iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:01 --timestop 16:59 -j REJECT```
