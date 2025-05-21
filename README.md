# Experiment 1.2: Understanding how it works

Berikut adalah gambar output dari eksekusi program yang mungkin Anda jalankan:

![Hasil Eksekusi Awal](images/Cursor_EW2eeNbsgO.png)

## Modifikasi dan Penjelasan

Mari kita pertimbangkan apa yang terjadi jika kita menambahkan sebuah kalimat cetak baru tepat setelah `spawner.spawn(...)` di `main.rs`, misalnya:

```rust
// ...
    spawner.spawn(async {
        println!("Husin's Computer: howdy!");
        TimerFuture::new(Duration::new(2, 0)).await;
        println!("Husin's Computer: done!");
    });

    println!("Husin's Computer: hey hey");
// ...
```

### Penjelasan Hasil Output:

Meskipun pemanggilan `println!("Husin's Computer: hey hey");` dilakukan setelah pemanggilan `spawner.spawn(async { ... });`, string `"hey hey"` dicetak sebelum `"howdy!"`.

Perilaku ini terjadi karena fungsi asynchronous (`async`) beroperasi secara independen dari alur eksekusi utama. Akibatnya, program utama tidak menunggu selesainya fungsi asynchronous sebelum melanjutkan eksekusinya.

Pernyataan `println!("Husin's Computer: hey hey");` diposisikan di luar fungsi asynchronous dan segera dieksekusi oleh program utama. Sementara itu, fungsi asynchronous mungkin sedang menunggu hasil dari *future* (dalam kasus ini, `TimerFuture`) atau menunggu untuk diambil dan dijalankan oleh executor.

Sebagai hasilnya, pesan dari thread utama dicetak terlebih dahulu, diikuti oleh eksekusi tugas asynchronous yang dikelola oleh executor, yang kemudian mencetak `"howdy!"` dan akhirnya `"done!"` setelah timer selesai. 