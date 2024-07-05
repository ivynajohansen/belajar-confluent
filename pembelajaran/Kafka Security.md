![image](https://github.com/ivynajohansen/belajar-confluent/assets/83331802/15ba4eac-fb8e-4dc2-9e91-00e66d3a2e36)# Kafka Security

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

Cara menyiapkan sertifikat SSL:
1. Generate sertifikat (X509) untuk setiap broker kafka
2. Generate certificate authority (CA) untuk sign
3. Sign semua sertifikat broker dengan CA tersebut
4. Import CA dan sertifikat yang telah di-sign ke key store broker
5. Import CA ke trust store client dan trust store broker
6. Untuk autentikasi 2-way, broker harus mempercayai client

**Keystore** menyimpan identitas Anda (private key dan sertifikat), sedangkan **Truststore** menyimpan identitas orang lain yang dipercayai (sertifikat tepercaya)

#### 1. Generate sertifikat (X509) untuk setiap broker kafka

```
keytool -keystore server.keystore.jks -alias localhost -keyalg RSA -validity 365 -genkey
```

`-keyalg` dapat digantikan dengan RSA, DSA, EC.

![image](https://github.com/ivynajohansen/belajar-confluent/assets/83331802/2d687f83-997b-477b-8693-8084be38f007)

#### 2. Generate certificate authority (CA) untuk sign

Tanpa sign CA, orang lain dapat membuat sertifikat X509 di step sebelumnya jika mengisi credential yang sama. Karena itu, buat CA:

```
openssl req -new -x509 -keyout ca-key -out ca-cert -days 365
```

Setelah itu akan diminta PEM pass phrase, isi PEM pass phrase dengan password saat membuat sertifikat x509.

#### 3. Sign semua sertifikat broker dengan CA tersebut

```
keytool -keystore server.keystore.jks -alias localhost -certreq -file cert-file
```

Command ini generasi Certificate Signing Request (CSR) untuk pasangan key yang disimpan di keystore keystore.keystore.jks dengan alias localhost. CSR disimpan dalam file bernama cert-file. Setelah jalankan command diatas, file bernama `cert-file` akan muncul.

```
openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin p
ass:security
```

Command ini menggunakan OpenSSL untuk sign CSR dengan sertifikat CA (ca-cert) dan private key CA (ca-key). Sertifikat yang sign disimpan dalam file bernama cert-signed.

## Autorisasi Read & Write (Access Control List)

Ketika suatu cluster digunakan oleh banyak user, penting untuk mengatur akses user dalam read atau write topic dan host.

## Ports

Kafka mendukung port-port berikut untuk keamanan:
- Plain Text: Tidak ada enkripsi maupun autentikasi
- SSL: Enkripsi dan autentikasi
- SASL: Autentikasi Kerberos
- SSL + SASL: SSL untuk enkripsi dan SASL untuk autentikasi
  
**Enkripsi:** Mengubah text biasa menjadi cipher text yang hanya bisa di decode oleh pihak yang memiliki autoritas

**Autentikasi:** Cara bagaimana suatu user membuktikan identitas mereka, contohnya menggunakan email dan password

**Autorisasi:** Tahap setelah autentikasi yang menunjukkan apa yang suatu user dapat lakukan, atau permission apa yang suatu user punya, contohnya 'Root user'.



