# Kafka Security

Untuk meningkatkan keamanan data dalam Kafka, ada beberapa fitur yang dapat digunakan:

## Autentikasi

![image](https://github.com/ivynajohansen/belajar-confluent/assets/83331802/fd152fc7-fbcc-4a19-a4a9-1b5b4dafe86b)

Pada gambar di atas, client external dan client internal mencoba untuk berkomunikasi dengan broker-broker dalam cluster. Untuk mengamankan komunikasi ini, dapat mengaktifkan autentikasi antara **broker dan client**.

Selanjutnya, zookeeper adalah komponen penting yang menyimpan semua metadata seperti offset, sehingga penting untuk autentikasi atau enkripsi data antara **zookeeper dan broker**.

### SSL

Saat menggunakan SSL untuk enkripsi data, performa kafka akan menurun. Autentikasi 1-way menurunkan performa secara sedikit namun autentikasi 2-way membuat performa menurun 50%. Ada 3 jenis enkripsi SSL dalam Kafka, yaitu:
- Antar broker dan client
- Antar broker
- Antar broker dan tool lainnya

Enkripsi antar broker digunakan saat menggunakan replikasi, sehingga data harus direplikasi dari satu broker ke broker yang lainnya.
