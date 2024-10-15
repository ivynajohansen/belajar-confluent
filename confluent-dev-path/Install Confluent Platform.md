# Install Confluent Platform

![image](https://github.com/user-attachments/assets/d1124703-f1c5-4e99-a056-b4e954d440ff)

Gambar ini menggambarkan komponen-komponen Confluent Platform, yang dapat diinstal di satu atau beberapa mesin. Dalam pengembangan, semua komponen sering dijalankan di satu mesin, sedangkan dalam produksi, tiap komponen biasanya berjalan di klusternya masing-masing.

Menginstal Confluent Platform melalui arsip TAR membuat pengaturan di environment pengembangan lokal menjadi lebih cepat, sedangkan manajer paket seperti _apt_ dan _yum_ adalah cara paling andal untuk menginstal server kelas produksi dengan memudahkan update dan secara otomatis menangani banyak konfigurasi sistem, seperti pengaturan layanan systemd dan peningkatan batas file handle yang terbuka.

## Fresh Install

Untuk menginstall Confluent Platform dengan instalasi tarball, buka lab environment dan jalankan command berikut:

```
sudo apt-get install openjdk-11-jre-headless
```

Download dan extract archive TAR Confluent Platform 7.1.1:

```
curl -O http://packages.confluent.io/archive/7.1/confluent-7.1.1.tar.gz
tar xzf confluent-7.1.1.tar.gz
```

![image](https://github.com/user-attachments/assets/81b684ad-074d-46e3-9f9d-708ab8b3b065)

## Konfigurasi CLI Confluent

Set variabel CONFLUENT_HOME ke lokasi instalasi dan tambahkan ke .bashrc.

```
export CONFLUENT_HOME=${HOME}/confluent-7.1.1 \
    && echo "export CONFLUENT_HOME=$CONFLUENT_HOME" >> ~/.bashrc
```

Tambahkan binary Confluent Platform (yang mencakup Confluent CLI) ke variabel PATH.

```
echo "export PATH=$CONFLUENT_HOME/bin:${PATH}" >> ~/.bashrc
```

Aktifkan Bash completion untuk CLI yang menyatu dan sumberkan .bashrc agar semua perubahan berlaku:

```
~/confluent-7.1.1/bin/confluent completion bash | sudo tee /etc/bash_completion.d/confluent \
    && echo "source /etc/bash_completion.d/confluent" >> ~/.bashrc \
    && source ~/.bashrc
```

Sekarang CLI konfluen tersedia di PATH dan memiliki pelengkapan otomatis Tab.

## Start Service Confluent Platform

Jalankan confluent platform

```
confluent local services start
```
