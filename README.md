# Subscriber

## Penjelasan Singkat

Repository ini berisi program subscriber untuk Tutorial A Event-Driven Architecture.

Subscriber adalah program yang menerima event atau message dari message broker. Pada tutorial ini, message broker yang digunakan adalah RabbitMQ. Subscriber akan mendengarkan queue bernama `user_created`, lalu memproses setiap message yang dikirim oleh publisher.

## Reflection 1: Subscriber dan Message Broker

### 1. Apa itu AMQP?

AMQP adalah singkatan dari Advanced Message Queuing Protocol. AMQP merupakan protokol yang digunakan untuk komunikasi antara aplikasi dengan message broker.

Dalam tutorial ini, RabbitMQ menggunakan AMQP agar publisher dapat mengirim message dan subscriber dapat menerima message melalui queue. Dengan AMQP, komunikasi antar program tidak harus dilakukan secara langsung. Publisher cukup mengirim message ke RabbitMQ, lalu subscriber mengambil message tersebut dari queue.

### 2. Apa arti `guest:guest@localhost:5672`?

URL yang digunakan pada program adalah:

```text
amqp://guest:guest@localhost:5672
```

Penjelasannya:

- `amqp://` menunjukkan bahwa program menggunakan protokol AMQP.
- `guest` pertama adalah username untuk login ke RabbitMQ.
- `guest` kedua adalah password untuk login ke RabbitMQ.
- `localhost` berarti RabbitMQ dijalankan di komputer lokal saya sendiri.
- `5672` adalah port default RabbitMQ untuk komunikasi AMQP.

Jadi, `guest:guest@localhost:5672` berarti program subscriber akan terhubung ke RabbitMQ yang berjalan di komputer lokal menggunakan username `guest`, password `guest`, dan port `5672`.