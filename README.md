# Subscriber

LINK GDRIVE GAMBAR LAPORAN:
https://drive.google.com/drive/folders/1i5fZG1KSFshurlHVGl3xys4egQD3bWch?usp=sharing 

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


## Simulasi Slow Subscriber


Pada tahap ini, saya mensimulasikan subscriber yang lambat dengan mengaktifkan kode berikut pada `main.rs` subscriber:

```rust
thread::sleep(ten_millis);
```

Kode tersebut membuat subscriber menunggu selama 1 detik sebelum memproses setiap message yang diterima dari RabbitMQ. Dengan demikian, proses konsumsi message menjadi lebih lambat dibandingkan sebelumnya.

Setelah itu, saya menjalankan publisher beberapa kali secara cepat menggunakan command:

```bash
cargo run
```

Dalam satu kali run, publisher mengirim 5 message ke queue `user_created`. Karena publisher dijalankan beberapa kali secara cepat, jumlah message yang masuk ke RabbitMQ meningkat lebih cepat daripada kemampuan subscriber memprosesnya.

Pada mesin saya, jumlah queue sempat meningkat hingga sekitar 10 message sebelum akhirnya turun kembali menjadi 0. Hal ini terjadi karena subscriber tetap memproses message satu per satu secara bertahap.

Grafik pada RabbitMQ menunjukkan bahwa queue meningkat ketika publisher mengirim banyak message dalam waktu singkat, lalu perlahan turun kembali setelah subscriber selesai memproses semua message tersebut.

Menurut saya, hal ini menunjukkan manfaat message broker dalam event-driven architecture. Ketika subscriber sedang lambat, message tidak langsung hilang atau menyebabkan sistem crash. RabbitMQ tetap menyimpan message di queue sampai subscriber siap memprosesnya satu per satu.

### Simulasi pada Cloud Environment

Jika simulasi slow subscriber dijalankan pada cloud environment, maka queue dapat meningkat lebih besar dibandingkan pada local machine karena publisher dan subscriber dapat berjalan pada beberapa instance atau virtual machine yang berbeda.

Selain itu, latency jaringan juga dapat mempengaruhi kecepatan pengiriman dan pemrosesan message. Oleh karena itu, konfigurasi firewall, resource server, dan monitoring RabbitMQ menjadi lebih penting pada cloud deployment dibandingkan ketika dijalankan secara lokal.

## Menjalankan Minimal Tiga Subscriber


Pada tahap ini, saya menjalankan tiga subscriber secara bersamaan pada terminal yang berbeda. Semua subscriber mendengarkan queue yang sama, yaitu `user_created`.

Setelah itu, saya menjalankan publisher beberapa kali secara cepat menggunakan command:

```bash
cargo run
```

Dalam satu kali run, publisher mengirim 5 message. Karena publisher dijalankan beberapa kali, RabbitMQ menerima banyak message dalam waktu singkat.

Namun berbeda dengan sebelumnya, sekarang terdapat tiga subscriber yang aktif secara bersamaan. Hal ini terlihat pada dashboard RabbitMQ, di mana terdapat:

```text
Connections: 3
Channels: 3
Consumers: 3
```

Karena terdapat beberapa subscriber, RabbitMQ dapat membagi message ke beberapa subscriber sekaligus. Akibatnya, queue diproses lebih cepat dan spike pada grafik menjadi lebih kecil dibandingkan ketika hanya menggunakan satu subscriber.

Menurut saya, hal ini menunjukkan salah satu keuntungan event-driven architecture. Ketika jumlah request meningkat, sistem dapat diskalakan dengan menambahkan subscriber baru sehingga beban kerja tidak hanya diproses oleh satu subscriber saja.

Beberapa hal yang menurut saya masih bisa ditingkatkan dari kode ini adalah:

1. Menambahkan logging yang lebih rapi agar monitoring lebih mudah dilakukan.
2. Menambahkan retry mechanism jika subscriber gagal memproses message.
3. Menambahkan environment variable untuk konfigurasi RabbitMQ agar tidak hardcoded di source code.
4. Menambahkan dead-letter queue untuk menangani message yang gagal diproses.


