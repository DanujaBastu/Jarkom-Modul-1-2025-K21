# Jarkom-Modul-1-2025-K21
Member :
1. Danuja Prasasta Bastu (5027241037)
2. Naila Cahyarani Idelia (5027241063)

<div align=center>

# Soal Modul 1

</div>

1. Untuk mempersiapkan pembuatan entitas selain mereka, **Eru** yang berperan sebagai **Router** membuat dua **Switch/Gateway**. Dimana Switch 1 akan menuju ke dua Ainur yaitu **Melkor** dan **Manwe**. Sedangkan Switch 2 akan menuju ke dua Ainur lainnya yaitu **Varda** dan **Ulmo**. Keempat Ainur tersebut diberi perintah oleh **Eru** untuk menjadi **Client**.

<img width="1215" height="673" alt="Screenshot 2025-09-30 223031" src="https://github.com/user-attachments/assets/1395f368-6219-4391-8acf-bb1565a518e2" />

2. Karena menurut **Eru** pada saat itu Arda (Bumi) masih terisolasi dengan dunia luar, maka buat agar **Eru** dapat tersambung ke internet.

* Tes awal dengan cek koneksi & akses internet (tes awal)

Telnet ke node (contoh Eru):

```bash
telnet 10.15.43.32 [port]
```

<img width="1062" height="253" alt="Screenshot 2025-09-30 224540" src="https://github.com/user-attachments/assets/a9990434-8210-41a1-a5f8-cec4106a5c40" />

Di console node: cek interface dan ping internet:

```bash
ping google.com -c 5
```

<img width="1194" height="307" alt="Screenshot 2025-09-30 224555" src="https://github.com/user-attachments/assets/af7d3895-50c0-4609-9f54-c8ea6db2e855" />

Jika ping google.com berhasil, node punya akses internet.

3. Sekarang pastikan agar setiap Ainur (**Client**) dapat terhubung satu sama lain.

* Set konfigurasi interface

**Eru**

```bash
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address [Prefix IP].1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address [Prefix IP].2.1
	netmask 255.255.255.0
```

**Melkor**

```bash
auto eth0
iface eth0 inet static
	address [Prefix IP].1.2
	netmask 255.255.255.0
	gateway [Prefix IP].1.1
```

**Manwe**

```bash
auto eth0
iface eth0 inet static
	address [Prefix IP].1.3
	netmask 255.255.255.0
	gateway [Prefix IP].1.1
```

**Varda**

```bash
auto eth0
iface eth0 inet static
	address [Prefix IP].2.2
	netmask 255.255.255.0
	gateway [Prefix IP].2.1
```

**Ulmo**

```bash
auto eth0
iface eth0 inet static
	address [Prefix IP].2.3
	netmask 255.255.255.0
	gateway [Prefix IP].2.1
```

Setelah edit: Apply → restart node (mastiinnya pake `telnet 10.15.43.32 [port]` & `ip a`)

<img width="1233" height="772" alt="Screenshot 2025-09-30 230740" src="https://github.com/user-attachments/assets/cacf34e8-4b44-4de5-bd33-6c9c040cd028" />

<img width="1538" height="754" alt="Screenshot 2025-09-30 231046" src="https://github.com/user-attachments/assets/62a47074-2ce1-46e8-a058-e9b3d7bcdb37" />

4. Setelah berhasil terhubung, sekarang **Eru** ingin agar setiap Ainur (**Client**) dapat mandiri. Oleh karena itu pastikan agar setiap **Client** dapat tersambung ke internet.

* Persisten konfigurasi & NAT (supaya client dapat ke internet)

Di Eru buat NAT agar subnet klien bisa melewati interface eth0 (asumsi eth0 ke Internet):

```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s [Prefix IP].0.0/16

cat /etc/resolv.conf
```

<img width="1144" height="84" alt="Screenshot 2025-09-30 233159" src="https://github.com/user-attachments/assets/b53ce3ca-fd65-46a5-89b7-18e246407074" />

Verifikasi DNS di client (Melkor, dll):

```bash
echo nameserver 192.168.122.1 > /etc/resolv.conf

cat /etc/resolv.conf
```

Ping internet:

```bash
ping google.com -c 5
```

<img width="1285" height="407" alt="Screenshot 2025-09-30 233822" src="https://github.com/user-attachments/assets/2a7f4f70-a6b7-4be4-ac70-520be43d62c4" />

5. Ainur terkuat **Melkor** tetap berusaha untuk menanamkan kejahatan ke dalam Arda (Bumi). Sebelum terjadi kerusakan, **Eru** dan para Ainur lainnya meminta agar semua konfigurasi tidak hilang saat semua node di restart.

```bash
sudo nano /root/.bashrc
```

Isi nano /root/.bashrc

```bash
# ~/.bashrc: executed by bash(1) for non-login shells.

# Note: PS1 is set in /etc/profile, and the default umask is defined
# in /etc/login.defs. You should not need this unless you want different
# defaults for root.
# PS1='${debian_chroot:+($debian_chroot)}\h:\w\$ '
# umask 022

# You may uncomment the following lines if you want `ls' to be colorized:
# export LS_OPTIONS='--color=auto'
# eval "$(dircolors)"
# alias ls='ls $LS_OPTIONS'
# alias ll='ls $LS_OPTIONS -l'
# alias l='ls $LS_OPTIONS -lA'
#
# Some more alias to avoid making mistakes:
# alias rm='rm -i'
# alias cp='cp -i'
# alias mv='mv -i'

iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s [Prefix IP].0.0/16
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

6. Setelah semua Ainur terhubung ke internet, **Melkor** mencoba menyusup ke dalam komunikasi antara **Manwe** dan **Eru**. Jalankan file berikut (link file) lalu lakukan packet sniffing menggunakan **Wireshark** pada koneksi antara **Manwe** dan **Eru**, lalu terapkan display filter untuk menampilkan semua paket yang berasal dari atau menuju ke **IP Address Manwe**. Simpan hasil capture tersebut sebagai bukti.

* Buat skrip traffic singkat (Manwe)

Simpan file traffic.sh di /root/ node Manwe dan beri executable:

```bash
root@Manwe:~# nano traffic.sh

root@Manwe:~# chmod +x traffic.sh

root@Manwe:~# ./traffic.sh
```

Isi nano traffic.sh ada di link file zip

* Packet capture (Wireshark)

Jalankan Wireshark di GNS3 Web/Client dan capture link antara Manwe ↔ Eru. Terapkan display filter untuk menampilkan traffic berasal dari atau menuju IP Manwe. Simpan hasil capture

<img width="898" height="159" alt="Screenshot 2025-10-01 223437" src="https://github.com/user-attachments/assets/6bd7caec-edd7-4f48-a86e-e9d3e1dde3a2" />

7. Untuk meningkatkan keamanan, **Eru** memutuskan untuk membuat sebuah **FTP Server** di node miliknya. Lakukan konfigurasi **FTP Server** pada node **Eru**. Buat dua user baru: ainur dengan hak akses **write&read** dan **melkor** tanpa hak akses sama sekali ke direktori **shared**. Buktikan hasil tersebut dengan membuat file teks sederhana kemudian akses file tersebut menggunakan kedua user.

* Uji FTP

Instal vsftpd

```bash
apt-get update

apt-get install vsftpd
```

Buat directory & menamba user

```bash
mkdir -p /var/ftp/shared

adduser ainur

adduser melkor

nano /etc/vsftpd.conf
```

Pastikan baris-baris berikut ada dan tidak diawali tanda #:

```bash
local_enable=YES
write_enable=YES
chroot_local_user=YES
allow_writeable_chroot=YES
local_root=/var/ftp/shared
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
nano /etc/vsftpd.userlist(isi dengan ainur)
```

Membuat grup khusus untuk ainur

```bash
addgroup ftpaccess

adduser ainur ftpaccess

chgrp ftpaccess /var/ftp/shared/

chmod 775 /var/ftp/shared/
```

Menjalankan vstpd

```bash
service vsftpd restart
```

Install ftp di node

```bash
apt-get update

apt-get install ftp

echo "Ini adalah file bukti dari Ainur" > file_bukti.txt
```

Login ke user ainur

```bash
ftp 10.74.1.1

put file_bukti.txt

ftp> ls

ftp> quit

ftp 10.74.1.1

Malkor
```

8. **Ulmo**, sebagai penjaga perairan, perlu mengirimkan data ramalan cuaca ke node **Eru**. Lakukan koneksi sebagai client dari node **Ulmo** ke FTP Server **Eru** menggunakan user **ainur**. **Upload** sebuah file berikut (link file). Analisis proses ini menggunakan **Wireshark** dan identifikasi perintah FTP yang digunakan untuk proses upload.

* Upload & download bukti, dan ubah hak akses

```bash
wget --no-check-certificate 'https://drive.google.com/uc?export=download&id=11ra_yTV_adsPIXeIPMSt0vrxCBZu0r33' -O cuaca.zip
```

Ke gns3 client, lalu membuka wireshark dari situ dengan klik kanan lalu capture di connection antara switch dengan ulmo
Kembali ke terminal lalu masuk ke ftp `ftp 10.74.2.1`.

Login ke Ainur, lalu jalankan. `put cuaca.zip`

Setelah itu kembali ke wireshark dan melakukan display filter yaitu `ftp || ftp-data`
Berikut adalah hasilnya

<img width="893" height="113" alt="image" src="https://github.com/user-attachments/assets/00e26d68-048e-46b7-a25e-c961f90ec1bf" />

Untuk double check, bisa kembali ke terimal dan ke bagian eru lalu jalankan 

```bash
ls -l /var/ftp/shared
```

9. **Eru** ingin membagikan "Kitab Penciptaan" di (link file) kepada **Manwe**. Dari FTP Server **Eru**, **download** file tersebut ke node **Manwe**. Karena **Eru** merasa Kitab tersebut sangat penting maka ia mengubah akses user **ainur** menjadi **read-only**. Gunakan Wireshark untuk memonitor koneksi, identifikasi perintah FTP yang digunakan, dan uji akses user **ainur**.

```bash
wget --no-check-certificate 'https://drive.google.com/uc?export=download&id=11ua2KgBu3MnHEIjhBnzqqv2RMEiJsILY' -O

/var/ftp/shared/Kitab_Penciptaan.zip

sudo chmod 755 /var/ftp/shared/
```

Lalu melakukan capture dari gns client dengan node dari manwe ke switch

Selanjutnya melakukan login ke ainur melalui ftp

```bash
ftp 10.74.1.1

ftp> get Kitab_Penciptaan.zip
```

Lalu ke wireshark dan melakukan display filter dengan filter `(ftp || ftp-data)`. Setelah itu akan mendapatkan

<img width="898" height="119" alt="Screenshot 2025-10-03 095036" src="https://github.com/user-attachments/assets/13bd1e4e-4302-4b02-b12e-ad6c2574220e" />

Untuk mengetest ainur di read only, kita menggunakan 

```bash
sudo chmod 755 /var/ftp/shared/upload/
```

Lalu kita bisa check dengan `ftp> get Kitab_Penciptaan.txt` di ftp ainur seperti yang ada di bawah ini

<img width="450" height="184" alt="Screenshot 2025-10-03 095322" src="https://github.com/user-attachments/assets/af03f8d2-9fac-4493-ae49-abd44e98b1d1" />

10. **Melkor** yang marah karena tidak diberi akses, mencoba melakukan serangan dengan mengirimkan banyak sekali request ke server **Eru**. Gunakan command **ping** dari node **Melkor** ke node **Eru** dengan jumlah paket yang tidak biasa (spam ping misalnya 100 paket). Amati hasilnya, apakah ada packet loss? Catat average round trip time untuk melihat apakah serangan tersebut mempengaruhi kinerja Eru.

```bash
ping -c 100 10.74.1.1 #di manwe
```

11. Sebelum era koneksi aman, Eru sering menyelinap masuk ke wilayah Melkor. Eru perlu masuk ke node tersebut untuk memeriksa konfigurasi, namun ia tahu Melkor mungkin sedang memantau jaringan. Buktikan kelemahan protokol Telnet dengan membuat akun dan password baru di node Melkor kemudian menangkap sesi login Eru ke node Melkor menggunakan Wireshark. Tunjukkan bagaimana username dan password dapat terlihat sebagai plain text.

```bash
apt-get update

apt-get install telnetd -y
```

Buat user `Adduser`, lalu mulai capture

```bash
telnet <IP_Address_Melkor>
```

Login ke user baru, setelah itu stop capture

Di Wireshark, gunakan display filter

```bash
telnet
```

![WhatsApp Image 2025-10-04 at 23 51 37_0ee09453](https://github.com/user-attachments/assets/f968ae0a-1ee9-4809-80e4-bf36cbd5e399)


12. **Eru** mencurigai **Melkor** menjalankan beberapa layanan terlarang di node-nya. Lakukan pemindaian port sederhana dari node Eru ke node **Melkor** menggunakan **Netcat (nc)** untuk memeriksa port **21**, **80**, dalam keadaan terbuka dan port rahasia **666** dalam keadaan tertutup.

Pastikan nc (netcat) terpasang , jika belum ada:

```bash
apt-get update
apt-get install netcat -y
```

Buka port 21 (FTP) & port 80 (HTTP)

```bash
nc -l -p 21 &

nc -l -p 80 &
```

Cek port 21 (open), 80 (open), 666 (closed)

```bash
nc -zv [IP Prefix_Melkor] 21

nc -zv [IP Prefix_Melkor] 80

nc -zv [IP Prefix_Melkor] 666
```

13. Setelah insiden penyadapan Telnet, **Eru** memerintahkan semua koneksi administratif harus menggunakan **SSH (Secure Shell)** untuk mengamankan jaringan. Lakukan koneksi SSH dari node **Varda** ke **Eru**. Tangkap sesi tersebut menggunakan **Wireshark**. Analisis dan jelaskan mengapa username dan password tidak dapat dilihat seperti pada **sesi Telnet**. Tunjukkan paket-paket terenkripsi dalam hasil capture sebagai bukti keamanan SSH.

Install SSH di Eru dan Varda

Di Eru

```bash
apt-get update
apt-get install openssh-server -y
service ssh start
```

Di Varda

```bash
apt-get update
apt-get install openssh-client -y
```

Buat user untuk testing 

```bash
adduser test

echo "test:[password]" | chpasswd
```

Lakukan koneksi SSH dari Varda ke Eru

```bash
ssh test@[IP Prefix_Eru] #Pake IP Prefix_Eru di sisi Switch2, karena Varda terhubung ke Switch2
```

<img width="1692" height="467" alt="image" src="https://github.com/user-attachments/assets/00eb9979-bc83-4d4d-96e9-544275dfb0bc" />

<img width="1919" height="737" alt="Screenshot 2025-10-04 222759" src="https://github.com/user-attachments/assets/edd37e7a-f907-4db2-b32f-00458e9a5e67" />

<img width="1917" height="735" alt="Screenshot 2025-10-04 222814" src="https://github.com/user-attachments/assets/20dd86bb-aa4e-489f-937b-f938efc23e51" />

Di Wireshark, gunakan display filter

```bash
ssh || tcp.port == 22
```

<img width="1919" height="737" alt="image" src="https://github.com/user-attachments/assets/814fbc16-7a49-49bc-9478-4a108086106a" />

Setelah di analisis perbedaan telnet dan SSH di enkripsi. Jika di telnet tidak ada (plain text), sedangkan di SSH ada (encrypted).

Username dan password SSH tidak dapat dilihat seperti pada sesi Telnet karena menggunakan enkripsi dengan langkah:

* Key Exchange: Client dan server bertukar kunci enkripsi
* Authentication: Username dan password dikirim dalam bentuk terenkripsi
* Session: Semua data komunikasi dienkripsi end-to-end
