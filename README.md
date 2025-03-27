# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [X] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [X] Commit: `Create Subscriber model struct.`
    -   [X] Commit: `Create Notification model struct.`
    -   [X] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [X] Commit: `Implement add function in Subscriber repository.`
    -   [X] Commit: `Implement list_all function in Subscriber repository.`
    -   [X] Commit: `Implement delete function in Subscriber repository.`
    -   [X] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [X] Commit: `Create Notification service struct skeleton.`
    -   [X] Commit: `Implement subscribe function in Notification service.`
    -   [X] Commit: `Implement subscribe function in Notification controller.`
    -   [X] Commit: `Implement unsubscribe function in Notification service.`
    -   [X] Commit: `Implement unsubscribe function in Notification controller.`
    -   [X] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [X] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [X] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [X] Commit: `Implement publish function in Program service and Program controller.`
    -   [X] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [X] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

Dalam pola desain Observer, penggunaan interfac penting untuk mendukung fleksibilitas dan skalabilitas. Interface memungkinkan kita untuk mendefinisikan kontrak yang harus diimplementasikan oleh semua observer, sehingga kita dapat menambahkan berbagai jenis observer di masa depan tanpa mengubah kode Publisher.

Dalam kasus BambangShop, jika kita hanya memiliki satu jenis Subscriber dengan perilaku yang tidak akan berubah atau berkembang, maka penggunaan satu Model struct saja sudah cukup. Tetapi jika ada kemungkinan di masa depan untuk memiliki berbagai jenis Subscriber dengan perilaku berbeda, maka lebih baik menggunakan trait untuk mendukung extensibility.

2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Untuk memastikan bahwa id pada Program dan url pada Subscriber bersifat unik, penggunaan DashMap (hashmap) lebih cocok dibandingkan Vec (list). DashMap adalah struktur data berbasis map yang secara bawaan menjamin keunikan kunci karena dia sudah dihash sedemikian rupa tidak ada yang overlap, sehingga kita tidak perlu melakukan pemeriksaan manual untuk memastikan tidak ada duplikasi. Selain itu, DashMap memiliki performa yang lebih baik untuk operasi pencarian, penambahan, dan penghapusan karena kompleksitasnya rata-rata O(1) seperti hashmap dengan me-hash objectnya dan dijadiin key dalam lookup, sedangkan Vec membutuhkan pencarian linear dengan kompleksitas O(n).

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

kita tetap memerlukan DashMap. DashMap dirancang khusus untuk mendukung akses bersamaan dengan performa tinggi, sehingga cocok untuk aplikasi multi-threaded. Sementara itu, pola singleton hanya memastikan bahwa ada satu instance dari suatu objek, tetapi tidak otomatis menyediakan thread safety. Jika kita menggunakan singleton tanpa mekanisme tambahan seperti mutex atau lock, kita tetap harus menangani masalah akses bersamaan secara manual, yang bisa menjadi kompleks dan rawan error. Jadi, DashMap adalah pilihan yang lebih praktis dan efisien karena sudah menyediakan thread safety bawaan, tanpa perlu menambahkan logika tambahan untuk mengelola akses bersamaan.

#### Reflection Publisher-2

1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Dalam pola MVC, Model biasanya mengatur penyimpanan data sekaligus logika bisnis. Tapi, kalau kita gabungkan semuanya di Model, kode bisa jadi berantakan dan sulit dikelola. Dengan memisahkan Service dan Repository, kode kita bisa lebih terorganisir. Repository fokus pada interaksi dengan database, seperti mengambil atau menyimpan data, sementara Service menangani logika bisnis, seperti aturan atau proses yang harus dijalankan. Pemisahan ini bikin kode lebih modular, gampang diuji, dan lebih fleksibel jika nantinya tedapat perubahan.

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Kalau kita cuma pakai Model tanpa memisahkan Service dan Repository, kode bakal jadi lebih rumit dan susah dikelola. Semua logika bisnis, penyimpanan data, dan interaksi antar model diletakkan di satu tempat. Interaksi antar model bakal bikin kode saling bergantung, jadi kalau ada perubahan di satu model, model lain bisa ikut kena dampaknya. Misal, kalau Program harus mengirim notifikasi ke Subscriber, semua logika itu harus ditulis di Model, yang bikin kode jadi panjang dan susah dibaca. Selain itu, debugging juga bakal lebih ribet karena semuanya bercampur di satu tempat. Dengan memisahkan Service dan Repository, kita bisa bikin kode lebih modular, gampang diuji, dan lebih fleksibel buat perubahan di masa depan.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Postman menurut saya sangat membantu buat ngetes project kita, terutama kalau lagi bikin atau nge-debug API. Dengan Postman, kita bisa kirim request HTTP ke server kita, dengan method seperti GET, POST, PUT, atau DELETE, dengan langsung melihat responsnya. Jadi, kita bisa tahu apakah API kita udah jalan sesuai ekspektasi atau belum. Salah satu fitur yang paling berguna menurutku adalah kemampuan buat simpan request dalam bentuk collection. Ini bikin kita nggak perlu ngetik ulang request yang sama berulang kali. Untuk proyek kelompok atau masa depan, fitur automated testing-nya juga akan sangat berguna, karena kita dapat membuat script untuk mengecek apakah semua endpoint masih jalan setelah ada perubahan kode.


#### Reflection Publisher-3

1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

pada tutorial ini, kita menggunakan Push model. Di Push model, publisher langsung mengirimkan data ke subscribers setiap kali ada perubahan atau event tertentu. Jadi, ketika ada produk baru atau perubahan pada produk, publisher akan langsung mengirimkan notifikasi ke semua subscribers yang terdaftar. Model ini cocok untuk kasus seperti ini karena subscribers tidak perlu terus-menerus memeriksa (pull) ke publisher untuk mendapatkan update, sehingga lebih efisien dan responsif.

2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

Kalau kita menggunakan pull model di pada kasus tutorial ini, ada beberapa kelebihan dan kekurangan. Keuntungannya, subscribers punya kontrol penuh untuk mengambil data dari publisher kapan pun mereka butuh, jadi mereka nggak akan dibanjiri notifikasi yang mungkin nggak relevan. Ini juga bisa mengurangi beban publisher karena dia nggak perlu terus-menerus mengirim data ke semua subscribers. Tapi, kekurangannya adalah subscribers harus terus-menerus ngecek(ngepull) ke publisher untuk update, yang bisa bikin sistem jadi kurang efisien, terutama kalau update jarang terjadi. Selain itu, Pull model bisa bikin respons sistem jadi lebih lambat karena subscribers harus inisiatif untuk ngecek, dibandingkan Push model yang langsung kasih update secara real-time. 

3. Explain what will happen to the program if we decide to not use multi-threading in the notification process.

Kalau kita tidak pakai multi-threading di proses notifikasi, program akan jalan lebih lambat, terutama kalau jumlah subscribers banyak. Karena publisher harus kirim notifikasi ke setiap subscriber satu per satu secara berurutan. Jadi, kalau ada banyak subscribers, waktu yang dibutuhkan buat menyelesaikan proses notifikasi akan lebih lama. Selain itu, selama proses notifikasi berjalan, publisher tidak bisa menangani request lain, yang membuat aplikasi menjadi kurang responsif.