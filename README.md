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

