# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia
---
### Rakabima Ghaniendra Rusdianto - Advanced Programming A - 2306228472
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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
##### 1. Use of RwLock<> vs. Mutex<>
In the receiver app, we use `RwLock<Vec<Notification>>` to synchronize access to our notifications collection.
- **Why RwLock?**  
  `RwLock` allows multiple readers to access the notifications concurrently when there are no writers, which is beneficial if reading notifications is a frequent operation. In our design, methods like `list_all_as_string()` can run concurrently without blocking each other.
- **Why not Mutex?**  
  A `Mutex` would lock access for every operation—both read and write—even when no modifications are being made. This leads to unnecessary contention and lower performance, especially when many threads try to read the notifications simultaneously.

##### 2. Use of lazy_static for Static Variables in Rust
Rust does not allow direct mutation of static variables because they are immutable by default to ensure thread safety at compile time.
- **Why lazy_static?**  
  The `lazy_static` crate provides a safe way to initialize static variables that require runtime computation (like our `Vec` of notifications or a `DashMap`). These static variables are wrapped in thread-safe types (e.g., `RwLock`) to allow mutable access safely across threads.
- **Contrast with Java:**  
  In Java, a static variable is mutable by default and can be modified via static methods. However, thread safety must be managed explicitly (for example, by using synchronized blocks). Rust, on the other hand, enforces thread-safety at compile time, so mutable global state must be explicitly wrapped in a concurrency-safe primitive (like `RwLock`), ensuring that all accesses are safe without extra runtime checks.

#### Reflection Subscriber-2
##### 1. Exploration Beyond the Tutorial (src/lib.rs)
Yes, I explored parts of the project outside the core tutorial steps, particularly the `src/lib.rs` file. From this file, I learned how to:
- **Initialize Global Configuration and Clients:**  
  The use of `lazy_static!` to create singletons like `REQWEST_CLIENT` and `APP_CONFIG` provides a thread-safe, centralized way to access configuration and HTTP client instances across the application.
  ```rust
  lazy_static! {
      pub static ref REQWEST_CLIENT: Client = ClientBuilder::new().build().unwrap();
      pub static ref APP_CONFIG: AppConfig = AppConfig::generate();
  }
  ```
  - **Environment Configuration:** 
    - The integration of dotenvy and Figment to load environment variables and merge them with default settings demonstrates a robust approach to configuration management in Rust.

  - **Error Handling Abstraction:**
    - The custom error type and compose_error_response function show how to standardize error responses, which improves consistency and readability across the application.

##### 2. Observer Pattern and Scalability of the Notification System
- **Observer Pattern Ease for Subscribers:**
Our notification system uses the Observer pattern in a push model, where the publisher (NotificationService) actively pushes notifications to each subscriber. This decoupling makes it straightforward to add new subscribers because:
    - Each new subscriber only needs to implement the expected interface (or in our case, the Subscriber model), and the publisher automatically handles sending notifications. 
    - The code in NotificationService::notify iterates over a list of subscribers and spawns a new thread for each, making it highly scalable.
```rust
for subscriber in subscribers {
let payload_clone = payload.clone();
let subscriber_clone = subscriber.clone();
thread::spawn(move || subscriber_clone.update(payload_clone));
}
```
- **Scaling the Main App:**
Spawning more than one instance of the Main app (publisher) is also manageable. The Observer pattern decouples publishers and subscribers, so multiple publisher instances can coexist. Each instance can publish notifications independently without affecting the subscription mechanism, although coordination (e.g., using a message broker) may be required in a distributed setup.

##### 3. Use of Tests and Postman Collection Enhancements
- **Writing Additional Tests:**
  - I have extended the testing suite by writing my own tests, both unit and integration tests, to validate different parts of the application. This includes testing the notification flow, data handling in repositories, and error responses. These tests help catch edge cases early and ensure changes do not break existing functionality.

- **Enhancing Postman Documentation:**
  - I enhanced my Postman collection by adding detailed descriptions, example payloads, and response validations. This makes it easier for other people to understand and use the API, streamlining integration efforts for group projects and future software development.

- **Overall Utility:**
  - Both custom tests and an enhanced Postman collection have proven invaluable by providing clear, automated feedback on API behavior and system integration. This ensures reliability and facilitates smoother collaboration in group projects.