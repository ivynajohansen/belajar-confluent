# Produce dan Consume menggunakan Schema Registry

## Produce dan Consume menggunakan Schema Registry di Java

Pertama, pastikan status schema registry pada vm confluent sudah running dan port 8081 bisa di-telnet dari client:

```
sudo systemctl status confluent-schema-registry
```

![image](https://github.com/user-attachments/assets/0ba14b8e-575d-495e-bb43-097ed45a34e7)

Pada file `pom.xml`, tambahkan library berikut di bagian Dependency:

```
<!-- Confluent Kafka Avro Serializer -->
<dependency>
        <groupId>io.confluent</groupId>
        <artifactId>kafka-avro-serializer</artifactId>
        <version>7.0.0</version>
</dependency>

<!-- Avro -->
<dependency>
    <groupId>org.apache.avro</groupId>
    <artifactId>avro</artifactId>
    <version>1.10.2</version>
</dependency>
```
