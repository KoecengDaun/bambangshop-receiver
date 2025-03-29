# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

##### **1. Mengapa saya perlu RwLock<Vec<Notification>>, dan mengapa tidak pakai Mutex?**  
Saya menggunakan `RwLock` karena saya sering melakukan baca (*read*) terhadap daftar notifikasi. Dengan `RwLock`, banyak *reader* dapat berjalan sekaligus (selama tidak ada *writer*), sehingga akses bacanya lebih efisien. Jika saya memakai `Mutex`, setiap akses (baca maupun tulis) akan saling memblokir secara keseluruhan, sehingga semua thread lain harus menunggu, bahkan sekadar untuk *read*.  

##### **2. Mengapa Rust tidak mengizinkan mutasi variabel statis secara langsung seperti di Java?**  
Rust memiliki aturan keselamatan (*safety*) yang ketat terkait *threading* dan *memory ownership*. Membiarkan variabel `static` bermutasi bebas akan menimbulkan banyak risiko balapan data (*data race*). Dengan menggunakan `lazy_static` (dan pengaman seperti `RwLock` atau `DashMap`), saya memastikan setiap akses bersifat *thread-safe*. Di Java, mengakses atau memodifikasi *static* bisa dilakukan kapan saja, tetapi Rust mengharuskan saya secara eksplisit menyatakan penanganan *concurrency* agar terhindar dari *data race* dan pelanggaran aturan kepemilikan data.

#### Reflection Subscriber-2

##### **1. Apakah saya menjelajahi bagian di luar langkah-langkah tutorial (mis. `src/lib.rs`)?**  
Saya hanya fokus pada petunjuk utama di tutorial, sehingga belum banyak menelusuri detail tambahan di `src/lib.rs`. Alasan utamanya karena waktu yang terbatas dan ingin segera menyelesaikan fitur utama sesuai spesifikasi. Jika ke depannya memiliki waktu lebih, saya akan menengok `src/lib.rs` atau bagian lain untuk memahami struktur proyek secara lebih menyeluruh.

##### **2. Bagaimana *Observer Pattern* memudahkan saya menambah *subscriber* baru, dan bagaimana jika ada lebih dari satu instance Main app?**  
Dengan *Observer Pattern*, saya hanya perlu menambahkan *subscriber* (Receiver) dan melakukan *subscribe* ke *publisher*. Begitu *subscriber* baru terdaftar, *publisher* akan otomatis mengirim notifikasi ketika ada peristiwa tertentu, tanpa saya harus menulis logika baru pada *publisher*.  
Jika saya menjalankan banyak instance Main app, secara konsep masih bisa diterapkan Observer. Namun, saya mungkin perlu mekanisme sinkronisasi agar tiap *Main app* bisa berbagi daftar *subscriber* (jika memang diinginkan satu data terpusat) atau tiap instance punya list *subscriber*-nya sendiri. Ini tidak serumit menulis ulang logika notifikasi, tetapi perlu mempertimbangkan integrasi antar-instance.

##### **3. Apakah saya membuat tes sendiri atau memperkaya dokumentasi Postman?**  
Saya sudah menggunakan koleksi Postman bawaan tutorial, belum sempat menambahkan tes otomatis atau mendokumentasikan lebih rinci. Meski begitu, fitur Postman untuk menambahkan *test script* pada setiap request sangat berguna—misalnya untuk memeriksa status code dan respons JSON. Hal ini juga akan bermanfaat bagi proyek kelompok dalam memastikan bahwa setiap perubahan fitur tidak merusak fungsionalitas yang sudah ada.
