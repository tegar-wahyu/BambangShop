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
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. *In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or `trait` in Rust) in this BambangShop case, or a single Model `struct` is enough?*

    >Dalam Observer design patterns, Subscriber (atau Observer) biasanya didefinisikan sebagai intercafe atau trait di Rust untuk memungkinkan beberapa concrete implementations. Hal ini memberikan fleksibilitas dan membuat kode lebih mudah untuk dikembangkan, karena dapat menambahkan jenis subscriber baru tanpa memodifikasi kode yang ada.

    >Dalam kasus BambangShop, single Model `struct` sebenarnya sudah cukup, tapi akan lebih baik untuk mendefinisikan Subscriber sebagai trait. Hal ini karena mungkin BambangShop akan memiliki berbagai jenis subscriber sehingga masing-masing subscriber memerlukan implementasi yang berbeda dalam menangani notifikasi.

2. *`id` in `Program` and `url` in `Subscriber` is intended to be unique. Explain based on your understanding, is using `Vec` (list) sufficient or using `DashMap` (map/dictionary) like we currently use is necessary for this case?*

    >Dalam kasus ini, menggunakan `DashMap` lebih cocok daripada `Vec` untuk menangani `id` unik di `Program` dan `url` di `Subscriber`.
    
    >>`DashMap` adalah `HashMap` yang bersifat concurrent, yang menyediakan cara yang efisien untuk menyimpan pasangan key-value. Hal ini memungkinkan kompleksitas yang konstan yaitu O(1), untuk penambahan, penghapusan, dan pencarian berdasarkan key.
    
    >Sedangkan `Vec` adalah jenis array yang dapat tumbuh secara kontinu. Jika kita menggunakan `Vec` untuk memastikan keunikan, kita perlu mengiterasi seluruh daftar untuk memeriksa duplikat setiap kali menambahkan item baru. Ini menghasilkan kompleksitas O(N), yang kurang efisien daripada `DashMap`, terutama saat ukuran data tergolong besar.
    
    >Oleh karena itu, penggunaan `DashMap` lebih efisien untuk memastikan keunikan `id` dan `url`.

3. *When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (`SUBSCRIBERS`) static variable, we used the `DashMap` external library for `thread safe HashMap`. Explain based on your understanding of design patterns, do we still need `DashMap` or we can implement Singleton pattern instead?*

	>Menurut pemahaman Saya, Singleton pattern menjamin suatu kelas hanya memiliki satu instance dan menyediakan titik akses global ke instance tersbut. Namun, dalam lingkungan multithreaded seperti Rust, Singleton saja tidak cukup untuk memastikan keamanan dari thread. Sehingga diperlukan sinkronisasi akses ke instance Singleton untuk mencegah situasi seperti `race condition`.

	>Seperti yang Saya sebutkan pada nomor 2, `DashMap` adalah `HashMap` bersifat concurrent yang memungkinkan banyak thread membaca dan menulis ke Map secara aman. Penggunaan `DashMap` sebagai implementasi Singleton di `SUBSCRIBERS` dalam kasus BambangShop sudah tepat karena sudah memastikan akses `thread-safe`.

#### Reflection Publisher-2

1. *In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?*

	>Dengan memisahkan "Service" dan "Repository", codebase menjadi lebih clean dan maintainable karena mengimplementasikan (Single Responsibility Principle).

	>"Repository" bertanggung jawab untuk menangani penyimpanan dan pengambilan data. Sedangkan "Service" bertanggung jawab untuk menerapkan business logic, menggunakan Repository untuk mendapatkan data yang diperlukan dan menyimpan hasilnya. 

	>Pemisahan ini memungkinkan setiap bagian untuk dikembangkan, diuji, dan diperbarui secara independen. Hal ini juga membuat kode lebih mudah dipahami karena setiap bagian memiliki tanggung jawab yang jelas.

2. *What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?*

	>Jika kita hanya menggunakan Model, kode dapat menjadi kompleks dan sulit untuk di-maintain. Setiap model harus menangani tidak hanya penyimpanan data, tetapi juga business logic dan manipulasi data. Hal ini dapat menyebabkan kode yang tightly coupled, di mana perubahan dalam satu model dapat memengaruhi yang lain.

	>Misalnya, jika model `Program` bertanggung jawab untuk memberi tahu `Subscriber`, setiap perubahan pada model `Subscriber` dapat memerlukan perubahan pada model `Program`. Begitu juga kalau model `Notification` bertanggung jawab untuk menyimpan notifikasi, itu juga perlu mengetahui struktur model `Program` dan `Subscriber` untuk membuat notifikasi yang sesuai.

3. *Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.*

	>Sebelumnya Saya pertama kali menggunakan Postman untuk mata kuliah PBP, tapi pada saat itu Saya tidak banyak memanfaatkan fitur-fitur yang ada. 
    
    >Setelah Saya mencoba untuk eksplor Postman pada project ini, menurut Saya Postman adalah alat yang sangat powerful untuk melakukan API Testing. Postman memungkinkan saya untuk mengirimkan request HTTP ke server dan memeriksa responnya. Hal ini sangat berguna untuk debugging dan memastikan bahwa endpoint-endpoint saya berfungsi sesuai dengan yang Saya inginkan.

#### Reflection Publisher-3