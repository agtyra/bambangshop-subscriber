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
1. Why do we use RwLock<Vec<Notification>> instead of Mutex<Vec<Notification>>?
- In this case, we used RwLock to handle multiple threads accessing the vector of notifications safely. The reason we chose RwLock over Mutex is because RwLock lets multiple threads read from the data at the same time, as long as no one is writing to it. But if a thread needs to write, it gets exclusive access.
If we used a Mutex, only one thread could access the data at all—whether it’s reading or writing. That would slow things down, especially if most of our operations are just reads. So basically, RwLock gives us better performance when there are more reads than writes, which is the case here.

2. Why do we use lazy_static!, and why can't we just mutate a static variable like in Java?
- In Java, it’s common to have static variables that we can change using static methods. But Rust works differently. Static variables in Rust need to be known at compile time and are usually immutable. That’s because Rust puts a strong focus on safety, especially around shared mutable data.
Since we can't directly make a mutable static variable that’s safe, we use lazy_static!. This macro lets us define things like vectors or maps that are initialized when first used and wrapped in something like RwLock to handle concurrency safely. Without lazy_static, we couldn’t create a mutable, global variable like our notifications vector in a thread-safe way.

#### Reflection Subscriber-2
1. Did I explore beyond the tutorial (like src/lib.rs)?
- To be honest, I didn’t dive deep into src/lib.rs or other files outside the main tutorial steps. The reason is that I was focused on understanding how the notification system works first, especially how the Receiver and Main apps communicate. Since everything I needed to test the core functionality was already covered in the steps, I prioritized getting that working before exploring more. That said, I’m curious now about how Rocket handles routing and shared state behind the scenes—so I might explore lib.rs next time.

2. How does the Observer pattern help with adding more subscribers? What about multiple instances of the Main app?
- The Observer pattern really makes it easy to add new subscribers—since each Receiver just needs to register its URL and it automatically gets notified. We don’t need to change the main logic every time we add a new one, which is super helpful. When I tried running multiple Receiver instances, they all got the notifications without any issue.
As for running multiple instances of the Main app, it might be a bit trickier. Right now, since the state (like notifications or subscriber lists) is stored in memory, each instance would have its own separate copy. That means a new Main instance wouldn't automatically know about subscribers registered in the others—so we’d need to set up a shared database or service to coordinate them if we want to scale that side too.

3. Did I write my own tests or improve documentation in Postman?
- No, I didn’t write my own tests or make additional documentation in Postman. For this tutorial, I focused more on making sure the basic flow worked correctly—sending notifications and seeing the results. I realize that adding tests or better documentation would make the system more robust and easier to maintain, so it's definitely something I want to try in future projects or group assignments.
