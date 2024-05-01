# gRPC Tutorial
## Reflection
> What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

Perbedaan utama dari *unary*, *server streaming*, dan *bi-directional streaming* adalah pada bagaimana *request* akan dikirim dan *response* akan diterima. Pada metode *unary*, client mengirimkan sebuah *request* dan akan menunggu hingga menerima sebuah *response* dari server. Pada metode *server streaming*, client mengirimkan sebuah *request* dan dapat menerima beberapa *response* dari server. Pada metode *bi-directional streaming*, client dapat mengirimkan beberapa *request* secara bersamaan dalam satu koneksi lalu menerima beberapa *response* dari server. Metode *unary* cocok untuk hal-hal seperti autentikasi, mengambil suatu *item* dari *database*, atau melakukan suatu kalkulasi. Metode *server streaming* cocok untuk hal-hal dimana server harus mengirimkan kembali jumlah data yang besar, seperti jika client ingin mengetahui berita terkini, *update* cuaca, atau harga saham terkini. Metode *bi-directional streaming* cocok untuk hal-hal yang membutuhkan komunikasi *real-time*, seperti aplikasi *chatting* atau *real-time analytics*.

> What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Dalam mengimplementasi servis gRPC di Rust, kita harus menjamin bahwa setiap *request* yang dikirimkan oleh client memiliki autentikasi dan otorisasi yang sesuai. Ini berarti untuk setiap *request* yang didapatkan, kita harus melakukan validasi apakah client memiliki autentikasi dan autorisasi yang sesuai. Setiap *request* juga harus di enkripsi agar tidak *vulnerable* terhadap *man-in-the-middle attack*, sehingga server harus mendekripsi dan mengenkripsi data untuk setiap *request*. Ini juga berarti ada pemanggilan fungsi tambahan yang harus dilakukan dalam memproses setiap *request*.

> What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Potensi permasalahan yang muncul dalam menggunakan *bi-directional streaming* adalah *race-condition*. Dalam kasus aplikasi *chatting*, *race-condition* bisa berarti server menerima dua buah *request* dalam waktu yang hampir bersamaan, sehingga server bingung harus menjawab yang mana terlebih dahulu. Oleh karena itu, dibutuhkan sinkronisasi data setelah tiap *request* dan melakukan *request handling* yang *thread-safe*.

> What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

Kelebihan dalam menggunakan `tokio_stream::wrappers::ReceiverStream` untuk melakukan *streaming response* adalah diberikannya abstraksi terhadap cara melakukan *streaming*, sehingga kita hanya perlu menggunakannya tanpa harus memikirkan bagaimana mengimplementasi *streaming*. Dengan menggunakan *library* ini juga, kita tidak perlu memikirkan tentang sinkronisasi data karena sudah diimplementasikan. Namun, kekurangan dalam menggunakan *library* ini adalah penggunaan *asynchronous programming* yang dapat membuat pemakaian lebih membingungkan dan kompleks.

> In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Kode gRPC Rust dapat distrukturkan dengan membuat *traits* atau *interface* di suatu file `.proto` sehingga di kode Rustnya tinggal membuat suatu *struct* lalu diimplementasikan. Setiap servis yang ada juga dapat dipisahkan ke file masing-masing sehingga tidak tergabung dalam suatu file besar. File-file tersebut juga kemudian dapat dipisahkan lagi ke beberapa folder, misalnya ada folder `unary`, `server streaming`, dan `bi-directional streaming`, yang mengandung servis yang mengimplementasikan metode komunikasi yang sesuai dengan nama foldernya.

> In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Jika terdapat logic untuk memproses payment yang lebih kompleks, akan ada baiknya apabila diterapkan *multi-threading* agar servis dapat menerima beberapa *request* dari client-client yang berbeda dan dapat memprosesnya tanpa harus menunggu-nunggu.

> What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

Dengan adanya gRPC, komunikasi antar platform dan teknologi menjadi lebih mudah karena client tidak harus membuka koneksi baru setiap kali ingin mengirimkan suatu *request*, melainkan bisa hanya membuka satu koneksi ke server lalu bisa memulai komunikasi. gRPC juga secara umum akan menggunakan Protobuf sebagai alat bantu pendefinisian servis dan serialisasi data. Ini sangat membantu *interoperability* karena teknologi atau platform lain hanya perlu diberikan file Protobuf yang sama dan selama mereka memiliki *library* untuk menghandle definisi Protobuf, mereka tidak perlu membuat definisi dan serialisasi data sendiri, yang tentu saja mempermudah komunikasi.

> What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

Keuntungan dari menggunakan HTTP/2 dibanding HTTP/1.1 atau HTTP/1.1 dengan WebSocket adalah dengan HTTP/2, client hanya perlu membuat 1 koneksi dengan server dan kemudian mereka bisa melakukan komunikasi sebanyak-banyaknya hingga salah satu menutup koneksi. Selain itu, dalam HTTP/2, antrian *request* - *response* diterapkan secara paralel, tidak secara seri, sehingga *request* akan diproses secara paralel, yang akan menghindari pemblokiran pada antrian karena waktu proses suatu *request* yang sangat lama. Di lain sisi, kekurangan dari HTTP/2 dibanding HTTP/1.1 atau HTTP/1.1 dengan WebSocket adalah implementasinya yang lebih kompleks karena harus mengurus semua *request* dan *response* dalam satu koneksi dan menjamin tidak terjadinya *race condition*. HTTP/2 juga menjadikan semua *request* dan *response* berada pada satu koneksi saja yang dapat lebih lambat ketika koneksi internet client atau server tidak stabil atau sedang lambat, karena mereka mengirimkan beberapa *request* atau *response* dalam waktu yang sama.

> How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

Model *request-response* dari REST API tentu akan terasa kurang responsif jika dibandingkan dengan model *bi-directional streaming* milik gRPC. Hal ini dikarenakan pada REST, satu koneksi hanya dapat digunakan untuk satu *request* dan *response*, sehingga untuk kasus *real-time*, kita harus membuka koneksi berkali-kali untuk mengirimkan *request* dan mendapatkan *response*, yang tentu saja menambahkan *overhead* karena pembukaan koneksi perlu melalui beberapa tahapan juga.

> What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Dengan penggunaan Protocol Buffers di gRPC, kita dapat menjamin bahwa data yang dikirimkan dari client maupun server sesuai dengan spesifikasi yang sudah didefinisikan dengan Protocol Buffers. Di lain sisi, dalam penggunaan JSON, antara client dan server harus memiliki persetujuan mengenai bentuk JSON yang akan diterima ketika proses berhasil atau gagal dan kedua belah pihak juga harus tidak melanggar persetujuan tersebut. Ini sangat berpegang kepada *diligence* dari client dan server sehingga secara keseluruhan tidak dapat dipastikan mereka akan mengirimkan JSON dengan format yang telah disetujui
