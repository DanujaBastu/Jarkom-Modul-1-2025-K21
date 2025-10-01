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
