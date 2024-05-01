# Module 9 Reflection Notes

1. Unary gRPC adalah protokol komunikasi antara client dan server dimana client mengirimkan satu request ke server dan mendapatkan satu response sebagai balasannya. Cocok digunakan untuk komunikasi dimana client hanya membutuhkan data atau tindakan tunggal dari server. Sementara itu, Server streaming gRPC adalah protokol komunikasi dimana server mengirimkan banyak response dalam bentuk suatu 'aliran'. Cocok digunakan apabila server perlu mengirimkan data ke client secara continuous, misalnya seperti live update harga saham, laporan cuaca, berita, atau file besar dalam beberapa chunk. Bi-directional streaming gRPC adalah protokol komunikasi dimana client dan server sama-sama bisa mengirimkan pesan ke satu sama lain secara continuous, sehingga memungkinkan komunikasi real-time antara client dan server. Sering digunakan pada kasus dimana dibutuhkan adanya komunikasi interaktif secara terus menerus.

2. Dari segi autentikasi dan otorisasi, perlu dipastikan bahwa pada setiap interaksi, identitas setiap pihak selalu saling diverifikasi. Jika terjadi intercept antara interaksi kedua belah pihak oleh seseorang yang tidak bertanggung jawab, terjadinya peretasan lebih lanjut bisa dihindari. Hal ini bisa diimplementasikan dengan menggunakan metode seperti JWT (JSON Web Token). Selain itu, perlu juga diperhatikan apakah _resource_ yang dapat diakses oleh client sesuai dengan apa yang memang seharusnya bisa client tersebut akses sesuai dengan _privilege_-nya. Terakhir, dalam praktiknya data yang dikirim pada interaksi perlu dienkripsi agar terhindar dari potensi adanya pencurian data melalui _intercept_ suatu interaksi. Enkripsi ini bisa dilakukan dengan misalnya menggunakan layer TLS.

3. Dalam skenario seperti aplikasi chat, perlu adanya autentikasi identitas dan enkripsi data, baik dari sisi server maupun client karena dalam kondisi tertentu bisa saja data yang kurang aman dicuri oleh pihak tidak bertanggung jawab yang menyadap interaksi antara server dan client. Selain itu, perlu ada manajemen concurrency yang baik untuk memastikan bahwa setiap chat yang terkirim urutannya sesuai dengan yang seharusnya dan tidak terjadi delay yang berarti antar pesan. Perlu juga adanya error-handling yang baik untuk memastikan setiap kendala yang terjadi dapat diatasi dengan pesan error yang sesuai.

4. Kelebihannya diantaranya:

   - Dapat dilakukannya streaming secara asinkronus
   - Integrasi dengan Tokio yang bisa meningkatkan performa
   - Dapat mencegah _resource exhaustion_ dengan adanya kemampuan untuk mengatasi back-pressure
     Kekurangannya diantaranya:
   - Menambah kompleksitas kode (bukan kompleksitas dari segi jalannya program, tapi dari sisi kode)
   - Memerlukan error-handling yang lebih menyeluruh
   - Memerlukan manajemen channel mpsc yang lebih teliti untuk menghindari kemungkinan kebocoran memori

5. Kode yang ditulis dapat didesain sedemikian rupa agar lebih modular, dalam artian lebih terpisah dalam beberapa modul yang dapat diimpor jika hanya dibutuhkan. Pada approach ini, perlu ada _responsibility handling_ yang baik, sehingga setiap modul memiliki responsibilitynya masing-masing. Selain itu, dapat juga digunakan Traits pada Rust agar memungkinkan abstraksi antara _service_ dan implementasinya, sehingga lebih terbuka terhadap modifikasi atau penambahan tertentu. Dengan ini, setiap modul dapat dianggap sebagai sebuah library sendiri yang dapat digunakan kembali dan dapat dengan mudah di-maintain.

6. Diperlukan adanya pemrosesan request yang lebih menyeluruh, termasuk validasi input dan error-handling terhadap hal-hal yang tidak sesuai atau kendala yang mungkin timbul. Selain itu, untuk sistem pembayaran yang lebih kompleks, proses pembayaran bisa menggunakan sistem eksternal atau third party, contohnya seperti Stripe.

7. gRPC menggunakan protokol HTTP/2 untuk koneksi yang bersifat persistent, sehingga dapat mengurangi latency dan pengeluaran resource tambahan dibandingkan dengan penggunaan REST API biasa. Dari latency dan overhead yang berkurang tersebut, komunikasi yang dilakukan juga bisa menjadi lebih lancar meskipun misalnya berjalan di platform yang berbeda atau komunikasinya antar bahasa pemrograman berbeda. Meskipun gRPC tidak kompatibel secara universal, gRPC juga bisa diintegrasikan dengan berbagai macam teknologi lain, sehingga mengurangi hambatan interoperabilitas antara komponen berbeda di dalam suatu sistem terdistribusi.

8. Kelebihannya diantaranya:

   - Adanya multiplexing yang memungkinkan diprosesnya request secara bersamaan melalui satu koneksi TCP, sehingga mengurangi latensi dan pengeluaran resource tambahan
   - Header binary yang berukuran lebih kecil sehingga meningkatkan efisiensi penggunaan bandwidth dan kecepatan parsing
   - Efisien dalam koneksi karena tidak memerlukan dibukanya koneksi baru untuk setiap request.
     Kekurangannya diantaranya:
   - Memiliki kompleksitas implementasi yang lebih tinggi karena adanya fitur seperti multiplexing
   - Terdapat potensi kendala kompabilitas dengan client browser yang digunakan

9. Dalam penggunaannya, REST API berjalan dalam siklus request-response yang tunggal, dimana ketika ada suatu request, maka client perlu menunggu response dari server. Berbeda dengan bidirectional streaming yang memungkinkan masuk dan keluarnya aliran data secara terus-menerus yang bisa diterima secara bersamaan dalam satu koneksi. Selain itu, REST API cukup terbatas dalam konteks komunikasi real-time karena pertukaran data dilakukan dalam siklus-siklus request-response yang sangat memungkinkan terjadinya delay. Tidak seperti bidirectional streaming yang memungkinkan pertukaran data secara terus-menerus dengan delay yang minimal. Dari segi responsivitas, sistem berbasis REST API akan bergantung pada waktu pemrosesan dan latency jaringan, sementara bidirectional streaming gRPC lebih bersifat responsif, sebagai contoh apabila misalnya ada suatu update di dalam server, client bisa langsung menerimanya saat itu juga.

10. gRPC dengan Protocol Buffers yang berbasis schema menghasilkan kompabilitas dan efisiensi tinggi berkat struktur data yang sudah disusun sedemikian rupa, dengan sudah dipastikannya validitas dan konsistensi data. Sementara itu, dalam JSON yang digunakan dalam payload REST API, struktur datanya akan lebih fleksibel dan memiliki kelebihan karena dapat dibaca secara lintas bahasa ataupun lintas platform, hanya saja memiliki resiko kesalahan pengelolaan seperti misalnya kesalahan tipe data.
