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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. Dalam pola Observer, Subscriber mewakili entitas yang mengamati atau mendengarkan perubahan pada subjek yang diminatinya. Secara tradisional, sebuah antarmuka atau trait digunakan untuk mendefinisikan kontrak yang harus dipatuhi oleh subscriber konkret. Namun, dalam Rust, trait biasanya digunakan untuk mendefinisikan perilaku daripada hanya struktur data. Dalam kasus BambangShop, karena tidak ada perilaku yang terkait dengan subscriber selain operasi dasar CRUD, sebuah struct tunggal cukup. Struct Subscriber menyimpan data yang diperlukan (url dan nama) untuk seorang subscriber, dan digunakan dalam repository untuk mengelola subscriber.

2. Apakah akan menggunakan Vec atau DashMap tergantung pada kebutuhan spesifik dari aplikasi tersebut. Jika keunikan ID atau URL penting dan pencarian yang efisien diperlukan, maka DashMap (atau jenis peta/dictionary lainnya) adalah pilihan yang lebih baik karena memberikan kompleksitas waktu pencarian O(1). Menggunakan Vec akan memerlukan iterasi melalui daftar untuk menemukan item, yang menghasilkan kompleksitas O(n) untuk operasi pencarian, yang mungkin tidak efisien untuk kumpulan data besar atau pencarian yang sering.

3. Mengenai keamanan thread, kompiler Rust memastikan keamanan memori dan konkurensi tanpa adanya data race melalui sistem ownership dan borrowing. Namun, untuk akses bersama ke data bersama seperti peta SUBSCRIBERS, mekanisme sinkronisasi tambahan diperlukan. DashMap dipilih di sini karena menyediakan mutabilitas interior dengan keamanan thread, memungkinkan beberapa utas untuk mengakses dan memodifikasi peta secara bersamaan. Meskipun mengimplementasikan pola Singleton bisa memastikan bahwa hanya satu instans dari peta yang ada, itu tidak secara inheren menyediakan keamanan thread. Jadi, bahkan dengan pola Singleton, Anda masih memerlukan mekanisme sinkronisasi seperti mutex atau kunci untuk memastikan keamanan thread, yang telah disediakan oleh DashMap secara langsung. Oleh karena itu, menggunakan DashMap dalam konteks ini tampaknya sesuai untuk memastikan keamanan thread dan akses bersama yang efisien ke data subscriber.

#### Reflection Publisher-2
1. Dalam pola Model-View-Controller (MVC), memisahkan lapisan "Service" dan "Repository" dari Model sejalan dengan Single Responsibility Principle (SRP) dan Separation of Concern. Model secara utama mewakili data dan strukturnya, termasuk logika bisnis, tetapi memisahkan Service memungkinkan untuk mengkapsulasi logika aplikasi spesifik dan operasi terkait data. Hal ini mempromosikan modularitas, keberlanjutan, dan pengujian, karena perubahan terhadap logika bisnis atau akses data dapat diisolasi dalam lapisan mereka masing-masing tanpa memengaruhi bagian lain dari aplikasi. Repository, di sisi lain, mengabstraksi akses data dan mekanisme penyimpanan, memungkinkan decoupling aplikasi dari sumber data yang mendasarinya dan memfasilitasi peralihan atau ekspansi teknologi penyimpanan data tanpa memengaruhi bagian lain dari aplikasi.

2. Jika kita hanya menggunakan Model tanpa memisahkan kepentingan ke dalam Service dan Repository, kompleksitas kode untuk setiap model kemungkinan akan meningkat secara signifikan. Setiap model akan perlu menangani logika bisnis dan operasi akses data secara langsung, menyebabkan kode yang membengkak dan saling terkait. Sebagai contoh, dalam kode yang diberikan, jika logika bisnis untuk operasi langganan dan pembatalan langganan diimplementasikan langsung dalam model Notifikasi, itu tidak hanya akan meningkatkan kompleksitas model Notifikasi tetapi juga membuatnya sulit untuk dipelihara dan diuji. Selain itu, coupling antara model-model akan meningkat, karena setiap model akan perlu berinteraksi langsung dengan yang lain untuk melakukan operasi terkait data, mengakibatkan ketergantungan yang kompleks dan fleksibilitas yang berkurang.

3. Postman merupakan tools yang dapat mengirimkan pengiriman API dan memeriksanya apakah fungsi yang dibuat itu sudah sesuai dengan harapan kita atau belum. Pada tutorial ini, kita mencoba beberapa API request seperti GET, POST, dan DELETE untuk melakukan pengecekan apakah fungsi yang dibuat dalam bambangshop ini berjalan dengan baik atau tidak. Tentunya hal ini akan memudahkan kita untuk melakukan pengecekan terhadap pengerjaan tugas kelompok karena kita dapat melakukan pengetesan fungsi secara akurat tanpa memikirkan frontendnya terlebih dahulu. 

#### Reflection Publisher-3
1. Dalam kasus tutorial ini, kita menggunakan variasi pertama, yaitu Push Model yang dimana publisher mendorong data ke subscribers.

2. Keuntungan dari menggunakan variasi model Pull dalam kasus tutorial ini adalah bahwa subscriber memiliki kontrol lebih besar atas kapan mereka menerima pembaruan, karena mereka secara aktif menarik data dari publisher. Ini dapat mengurangi beban pada publisher karena tidak perlu secara proaktif mengirimkan pembaruan kepada semua subscriber. Namun, kerugiannya adalah subscriber perlu secara teratur memeriksa publisher untuk memperbarui data, yang dapat menghasilkan kinerja yang buruk jika ada banyak subscriber yang harus diperbarui secara serentak.

3. Jika kita memutuskan untuk tidak menggunakan multi-threading dalam proses notifikasi, program akan mengalami penundaan yang signifikan ketika melakukan operasi notifikasi, terutama jika ada banyak pelanggan yang harus diberitahu. Tanpa multi-threading, proses notifikasi akan dieksekusi secara berurutan dalam satu thread, yang dapat mengakibatkan waktu respon yang lambat dan memblokir eksekusi tugas lain dalam aplikasi, seperti operasi HTTP. Ini dapat mengurangi responsifitas dan kinerja keseluruhan aplikasi, terutama dalam skenario beban tinggi di mana banyak notifikasi harus dikirim secara bersamaan. Dengan multi-threading, proses notifikasi dapat dieksekusi secara paralel dalam thread-thread terpisah, meningkatkan kinerja dan memungkinkan aplikasi untuk tetap responsif terhadap permintaan lain.