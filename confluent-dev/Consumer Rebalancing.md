# Consumer Rebalancing

## Cara rebalancing bekerja

Saat terjadi penyeimbangan ulang:

1. Kafka memberi tahu setiap consumer dalam grup dengan mengirimkan pesan GroupCoordinator.

2. Setiap consumer kemudian menanggapi dengan pesan JoinGroup, yang menunjukkan kesediaannya untuk berpartisipasi dalam penyeimbangan ulang.

3. Kafka kemudian menggunakan strategi penugasan partisi yang dipilih untuk menetapkan partisi kepada setiap konsumen dalam grup.

Selama penyeimbangan ulang, Kafka mungkin perlu menghentikan konsumsi data untuk sementara, untuk memastikan semua consumer memiliki tampilan terkini tentang penugasan partisi sebelum mengonsumsi ulang data.

## Apa yang memicu penyeimbangan kembali Kafka?

1. Consumer join atau pergi

Heartbeat adalah sinyal yang menunjukkan bahwa consumer masih hidup dan berpartisipasi aktif dalam grup. Jika konsumen gagal mengirimkan heartbeat dalam interval yang ditentukan, consumer dianggap mati dan koordinator grup memulai penyeimbangan ulang untuk menetapkan kembali partisinya kepada anggota lain.

2. Consumer Gagal untuk sementara
Jika consumer gagal, kafka akan menghapusnya dari consumer group sampai, memicu rebalance. Jika consumer tersebut kembali online, ia akan kembali termasuk dalam group, sehingga akan rebalance lagi.

3. Consumer idle terlalu lama
Jika consumer idle terlalu lama, Kafka menganggapnya sebagai consumer yang gagal dan mengeluarkannya dari grup. Hal ini memicu penyeimbangan ulang konsumer untuk mendistribusikan ulang partisi ke konsumen aktif yang tersisa dalam grup.

4. Partisi topik ditambahkan
Jika partisi baru ditambahkan ke suatu topik, Kafka memulai penyeimbangan ulang untuk mendistribusikan partisi baru di antara konsumer dalam grup.

## Side effect rebalancing kafka

- Latency meningkat
Proses penyeimbangan kembali melibatkan pemindahan partisi dari satu broker atau konsumen ke broker atau konsumer lain, yang dapat mengakibatkan beberapa data tidak tersedia atau tertunda untuk sementara.

- Throughput berkurang
Penyeimbangan kembali Kafka menyebabkan beberapa broker atau konsumen menjadi kelebihan beban atau kurang diutilisasi, yang menyebabkan pemrosesan data menjadi lebih lambat selama proses rebalancing.

- Peningkatan penggunaan sumber daya

- Potensi duplikasi dan kehilangan data

- Meningkatnya kompleksitas

## Cara preventasi supaya mengurangi rebalance

- Meningkatkan batas waktu sesi
- Kurangi partisi per topik
- Increase poll interval time

**Incremental cooperative rebalance:** Protokol penyeimbangan ulang kooperatif inkremental membagi penyeimbangan ulang menjadi subtask yang lebih kecil, dan konsumen terus mengonsumsi data saat subtask ini selesai. Hasilnya, penyeimbangan ulang terjadi lebih cepat dan dengan lebih sedikit gangguan pada pemrosesan data.

**Static group membership**: Consumer dalam grup konsumen secara eksplisit meminta broker Kafka untuk menetapkan partisi tertentu kepada mereka dengan menentukan ID partisi dalam konfigurasi mereka.

## Strategi assignment partisi

1. Range Assignor
Strategi ini pertama-tama akan menempatkan semua consumer dalam urutan leksikografis menggunakan member_id yang ditetapkan oleh koordinator broker. Kemudian, akan menempatkan partisi-topik yang tersedia dalam urutan numerik. Terakhir, untuk setiap topik, partisi ditetapkan mulai dari konsumen pertama.

![image](https://github.com/user-attachments/assets/ea993704-3c92-4ed5-a523-0c8f4e65f1b0)


2. Round Robin Assignor
Seperti sebelumnya, pemberi tugas akan menempatkan partisi dan konsumen dalam urutan leksikografis sebelum menetapkan setiap partisi, tetapi RoundRobinAssignor dapat digunakan untuk mendistribusikan partisi yang tersedia secara merata ke semua anggota.

![image](https://github.com/user-attachments/assets/a9591d77-a035-4521-8104-26db0b44db09)

3. StickyAssignor
Sama seperti Round Robin Assignor, tetapi ia akan mencoba meminimalkan pergerakan partisi antara dua penugasan, sekaligus memastikan distribusi yang seragam.
