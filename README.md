# Tutorial 9 - High Level Networking

> #### Raquel Nayyara Aulia - 2206826583

## Reflection
1. ***What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?***
    - `Unary RPC` merupakan salah satu bentuk paling sederhana dalam Remote Procedure Call (RPC), di mana klien mengirimkan permintaan ke server dan menunggu menerima respons dari server. Umumnya digunakan untuk skenario permintaan-respons sederhana di mana tidak ada interaksi tambahan dengan data permintaan yang diperlukan, seperti pengambilan data tertentu dari server
    - `Server streaming RPC` terjadi ketika klien mengirimkan permintaan ke server, yang kemudian merespons dengan serangkaian pesan yang berurutan hingga tidak ada lagi pesan yang diperlukan. Skenario ini umumnya digunakan ketika klien membutuhkan pengembalian data yang besar dari server, seperti pengambilan data dalam jumlah besar dari database
    - `Bi-directional streaming RPC` terjadi ketika klien dan server saling bertukar pesan menggunakan `read-write` dalam arah yang sama atau berlawanan secara bersamaan. Biasanya digunakan ketika klien dan server memerlukan interaksi langsung untuk mengirim dan menerima data secara real-time, seperti dalam layanan obrolan (chat service)

2. ***What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?***
    - `Authentication`, gRPC menyediakan dukungan untuk berbagai jenis mekanisme autentikasi, seperti TLS (Transport Layer Security), OAuth2, autentikasi berbasis token, dan juga opsi paling sederhana yaitu penggunaan username dan password
    - `Authorization` dapat diimplementasikan dengan menggunakan middleware atau interceptor pada server gRPC, yang memungkinkan untuk memeriksa izin akses klien sebelum memproses permintaan yang diterima
    - `Data Encryption`, gRPC secara default menggunakan TLS untuk mengenkripsi data yang dikirimkan antara klien dan server, sehingga data yang dikirimkan tidak dapat dibaca oleh pihak ketiga yang tidak berwenang

3. ***What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?***
Beberapa masalah yang mungkin timbul adalah dalam hal `Error Handling` saat menggunakan `read-write stream`, `Connection Management` seperti mengatasi pemutusan koneksi yang tak terduga dan merespons dengan cepat karena klien dapat terhubung dan memutuskan kapan pun, dan juga `Concurrency Management` karena banyak klien dapat mengirim dan menerima pesan secara simultan, yang memerlukan manajemen respons yang tepat dari klien yang berbeda

4. ***What are the advantages and disadvantages of using the `tokio_stream::wrappers::ReceiverStream` for streaming responses in Rust gRPC services?***
    - #### Advantages:
        - `Simple`: `ReceiverStream` memungkinkan konversi mudah dari `Receiver` ke `Stream`, yang langsung dapat digunakan dalam gRPC
        - `Kompatibilitas`: Terintegrasi dengan baik dengan `trait Stream` yang secara luas digunakan dalam Rust, memungkinkan `ReceiverStream` bekerja dengan fungsi dan perpustakaan yang berbasis `Stream`
        - `Mengelola Backpressure`: `ReceiverStream` memberikan penanganan backpressure secara otomatis. Saat kapasitas maksimal tercapai, proses pengiriman akan dihentikan hingga tersedia ruang yang cukup, mencegah kelebihan data
    - `Disadvantages`:
        - `Error Handling`: `ReceiverStream` tidak memiliki mekanisme bawaan untuk menangani kesalahan yang mungkin muncul selama penerimaan pesan
        - `Komunikasi Satu Arah`: Fitur ini hanya mendukung komunikasi dari pengirim ke penerima tanpa kemungkinan balasan atau dua arah
        - `Kontrol Terbatas atas Polling`: Penggunaan `ReceiverStream` memberikan kontrol yang terbatas terhadap waktu dan cara `Receiver` dipoll, yang dapat membatasi fleksibilitas penggunaannya

5. ***In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?***
Untuk meningkatkan modularitas dan memfasilitasi penggunaan kembali kode dalam pengembangan Rust gRPC, beberapa langkah dapat diambil. Pertama, memisahkan logika bisnis dari implementasi gRPC membantu mencapai modularitas yang lebih tinggi, memungkinkan penggunaan kembali kode dalam berbagai konteks tanpa keterikatan pada infrastruktur spesifik. Kedua, menggunakan Protobuf untuk mendefinisikan service contracts memungkinkan implementasi yang independen dan menciptakan antarmuka yang jelas antara services yang berbeda. Ketiga, memanfaatkan Traits dalam Rust menyediakan abstraksi yang solid antara services dan implementasi konkret mereka, memudahkan pemisahan logika bisnis dari infrastruktur gRPC. Selain itu, strategi modularisasi kode, penerapan dependency injection, dan pengoptimalan manajemen error handling semuanya berkontribusi untuk meningkatkan keandalan dan kemudahan dalam pemeliharaan dan ekspansi kode gRPC di masa depan

6. ***In the `MyPaymentService` implementation, what additional steps might be necessary to handle more complex payment processing logic?***

    Additional Step:
    - `Validasi Request`: Periksa apakah `PaymentRequest` mengandung semua informasi yang diperlukan dan valid, seperti memastikan keberadaan ID pengguna dan memverifikasi bahwa jumlah pembayaran lebih dari nol
    - Validasi Request: Periksa apakah PaymentRequest mengandung semua informasi yang diperlukan dan valid, seperti memastikan keberadaan ID pengguna dan memverifikasi bahwa jumlah pembayaran lebih dari nol
    - Handle Error: Penting untuk mengelola kesalahan yang mungkin terjadi, dengan mengembalikan status gRPC yang sesuai dan menangani kesalahan yang terjadi, baik itu terkait database atau validasi
    - Interaksi dengan Database: Interaksi ini mungkin meliputi pemeriksaan saldo pengguna, pembaruan saldo setelah pembayaran, dan pencatatan transaksi. Gunakan library database yang mendukung operasi asinkron untuk langsung menangani error database yang mungkin muncul

7. ***What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?***
Penggunaan gRPC sebagai `communication protocol` memiliki dampak signifikan pada arsitektur dan desain sistem terdistribusi, terutama dalam hal interoperabilitas dengan teknologi dan platform lain. gRPC memungkinkan interoperabilitas antar layanan yang ditulis dalam berbagai bahasa pemrograman, meningkatkan efisiensi dengan menggunakan Protocol Buffers (protobuf) untuk format pesan. Ini mendukung streaming dan desain berbasis kontrak yang memperkuat interaksi antara klien dan server. Namun, kompleksitas dalam pengaturan dan keterbatasan browser dalam mendukung gRPC bisa menjadi hambatan, terutama ketika diperlukan interaksi dengan sistem yang hanya mendukung REST

8. ***What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?***

    - `Advantages`:
        - `Multiplexing`: HTTP/2 memungkinkan pengiriman dan penerimaan banyak permintaan dan respons secara bersamaan melalui satu koneksi TCP, yang meningkatkan efisiensi komunikasi
        - `Kompresi Header`: Menggunakan kompresi HPACK untuk header, HTTP/2 mengurangi overhead yang biasa terjadi pada permintaan dan respons dengan header yang besar atau berulang-ulang
        - `Server Push`: Fitur server push di HTTP/2 memungkinkan server mengirimkan sumber daya ke klien secara proaktif, yang dapat mengurangi waktu tunggu untuk permintaan tambahan dan meningkatkan performa
    - `Disadvantages`:
        - `Kompleksitas`: HTTP/2 memiliki struktur yang lebih kompleks dibandingkan dengan HTTP/1.1, sehingga membuatnya lebih sulit untuk diimplementasikan dan di-debug
        - `Penggunaan Sumber Daya yang Lebih Tinggi`: HTTP/2 cenderung memakan lebih banyak sumber daya server, seperti CPU dan memori, terutama karena overhead yang disebabkan oleh multiplexing dan kompresi header
        - `Penggunaan Cache`: Pemanfaatan multiplexing dan server push bisa mempersulit strategi caching karena dapat mengurangi efektivitas caching pihak ketiga atau caching yang terdistribusi, yang lebih efektif di HTTP/1.1

9. ***How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?***
Model REST API yang berbasis request-response berbeda secara signifikan dengan kemampuan streaming bidirectional dari gRPC, terutama dalam hal komunikasi dan responsif waktu nyata. REST API biasanya mengikuti pola di mana klien mengirimkan permintaan dan menunggu server merespons dengan data yang diminta. Sebaliknya, gRPC mendukung streaming dua arah, di mana klien dan server dapat saling bertukar pesan secara asinkron melalui satu koneksi, memungkinkan pertukaran data real-time tanpa harus menunggu pasangan komunikasi mereka merespons. Ini menjadikan gRPC sebagai pilihan yang lebih responsif dan ideal untuk aplikasi yang membutuhkan pertukaran data yang terus-menerus dan update cepat

10. ***What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?***
Pendekatan berbasis skema dari gRPC, yang menggunakan Protocol Buffers, memiliki beberapa implikasi ketika dibandingkan dengan sifat lebih fleksibel dan tanpa skema dari JSON dalam payload REST API. Menggunakan Protocol Buffers dalam gRPC memberikan validasi data yang lebih ketat, otomatisasi kode, dan kesesuaian yang kuat antara klien dan server. Ini menciptakan kontrak interface yang konsisten dan memudahkan komunikasi antara berbagai sistem dengan mengurangi risiko kesalahan.