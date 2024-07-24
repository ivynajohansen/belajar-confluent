![image](https://github.com/user-attachments/assets/b5d42e6a-6fe5-4fdc-b7f4-ee97de7e5a92)# LDAP

## Install LDAP Server

Di VM yang berbeda dari broker confluent, install LDAP dengan command berikut:

```
sudo apt update
sudo apt install slapd ldap-utils
```

Untuk mengubah suffix Directory Information Tree (DIT), jalankan command berikut:

```
sudo dpkg-reconfigure slapd
```

Dalam proses ini, akan ada beberapa pertanyaan yang harus dijawab:

- Omit OpenLDAP server configuration? `No`
- DNS domain name? `confluent-apac-ivy.com`
- Organization name? `confluent-apac`
- Remove the database when slapd is purged? `No`
- Move old database? `Yes`

Untuk mengetahui hostname dan suffixnya, cek dengan:

```
hostname
hostname -f
```

> Ubah hostname dengan edit file /etc/hostname dan menambahkannya ke /etc/hosts


Konfigurasi `/etc/ldap/ldap.conf` berdasarkan hasil yang telah didapatkan dalam step sebelumnya.

```
#
# LDAP Defaults
#

# See ldap.conf(5) for details
# This file should be world readable but not world writable.

BASE    dc=confluent-apac-ivy,dc=com
URI     ldap://ldap.confluent-apac-ivy.com ldap://ldap-provider.confluent-apac-ivy.com:666

#SIZELIMIT      12
#TIMELIMIT      15
#DEREF          never

# TLS certificates (needed for GnuTLS)
TLS_CACERT      /etc/ssl/certs/ca-certificates.crt
```

Sekarang, server LDAP telah dikonfigurasi dan dijalankan. Buka port LDAP di firewall sehingga klien eksternal dapat terhubung:

```
sudo ufw allow ldap
```

Lalu, tes koneksi LDAP kita dengan ldapwhoami, yang akan mengembalikan nama pengguna yang kita sambungkan:

```
ldapwhoami -H ldap:// -x
```

![image](https://github.com/user-attachments/assets/959998f8-287c-4a3d-b6d2-b0a15432a684)

![image](https://github.com/user-attachments/assets/0e046476-a80c-402b-a896-8526cb3c2f2d)

## Install LDAP Account Manager

Untuk mempermudah administrasi LDAP, bisa menggunakan phpLDAPadmin atau LDAP Account Manager. Saya akan coba untuk menggunakan LDAP Account Manager (LAM).

```
sudo apt -y install ldap-account-manager
```

Install dan enable extension PHP-CGI PHP, lalu restart apache2:

```
sudo apt install apache2 php php-cgi libapache2-mod-php php-mbstring php-common php-pear -y
sudo a2enconf php*-cgi
systemctl reload apache2
```

Di browser, navigasi ke `http://confluent-apac-ivy.com/lam`

![image](https://github.com/user-attachments/assets/8cab99fc-ba14-40d3-ba28-743df9d85612)

Pada, pojok kanan atas, klik `LAM configuration` lalu ke `Edit server profiles`.

![image](https://github.com/user-attachments/assets/2c555ca2-d097-4135-916c-d72b0e0e03ee)

Isi dengan password default, yaitu `lam`.

![image](https://github.com/user-attachments/assets/73a678b4-f774-47f9-8a25-72975be07ca6)

Di tab General Settings, ubah konfigurasi menjadi seperti berikut:

![image](https://github.com/user-attachments/assets/8539d007-23bb-4e26-a9bb-1af46f9d9975)

![image](https://github.com/user-attachments/assets/0256a670-11c3-488e-a997-c1cba80af929)

Selanjutnya, pergi ke tab Account Types dan buat 2 OU. OU pertama bernama Department yang menyimpan organization unit seperti IT, HR, dll. OU kedua bernama Groups yang menyimpan groups seperti grp-IT, grp-HR.

![image](https://github.com/user-attachments/assets/8d9e8f13-a35d-45d7-b95d-77a134d0827f)

Sekarang login sebagai admin dan klik create saat prompt muncul.

![image](https://github.com/user-attachments/assets/71cecf6d-47a7-435c-b9a7-984dd462a498)

Pada tab Tools, klik Tree View dan akan muncul seperti ini.

![image](https://github.com/user-attachments/assets/d8095239-a74d-43c0-a9bd-e66d0d14adcb)

Untuk buat organizational unit, pergi ke OU Editor di tab tools.

![image](https://github.com/user-attachments/assets/8c67a7aa-00ad-4e20-a4f7-7d7e0afa93af)

Untuk buat group, klik New Group pada Account >> Groups:

![image](https://github.com/user-attachments/assets/5d85bf36-df94-4584-81c3-3293a941ace6)

Untuk buat user, klik New User pada Account >> Users:

![image](https://github.com/user-attachments/assets/0f8e66e9-9db3-4ca2-9f93-65d8117e1285)

Di sebelah kiri, ada 3 bagian, yaitu Personal, Unix, dan Shadow. 

- Personal: personal information user seperti nama, email, nomor telepon, departemen, alamat, dll.
- Unix: Set username, common name, UID, Primary group, Secondary group, Home directory, dan default Login shell
- Shadow: Bagian untuk menambahkan extension akun Shadow yang berhubungan dengan password aging/expiry

![image](https://github.com/user-attachments/assets/8d4bfede-9e40-4f95-b36e-fa00b267e048)

Setelah itu, set password untuk user dan save user.

![image](https://github.com/user-attachments/assets/65a8ad13-c682-459a-a356-fa9ae8d25743)

## Konfigurasi Firewall

Jalankan command berikut untuk melihat status UFW Firewall:

```
sudo ufw status
```

Jika inactive, enable ufw dan list beberapa port untuk di-allow:

```
sudo ufw enable

sudo ufw allow 80
sudo ufw allow 443
sudo ufw allow 389
```

![image](https://github.com/user-attachments/assets/45d1962b-2da1-442c-8ff4-f1732f07acf7)
