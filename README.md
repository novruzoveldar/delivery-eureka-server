# delivery-eureka-server

## 🧭 About

In a distributed microservice architecture, service discovery is essential for scalability and fault tolerance.
The **Delivery Eureka Server** enables automatic service registration and discovery across all backend services in the delivery system.

It eliminates the need for manual service configuration and supports:

* Dynamic load balancing via client-side discovery
* High availability (HA) through Eureka clustering (if enabled)
* Centralized service registry for all delivery-related microservices

Typical registered services include:

* `gw-delivery` — API Gateway
* `ms-delivery-admin` — Admin microservice
* `ms-courier-order` — Courier microservice
* `ms-parcel-order` — Parcel microservice

---

## 🚀 Features

* Acts as **central service registry** for all microservices
* Supports **Eureka client registration and discovery**
* Integrated with **Spring Cloud Config Server** (optional)
* Lightweight and easy to deploy
* Supports clustering (peer awareness) for redundancy
* Compatible with Spring Boot Actuator for health monitoring

---

## 🛠 Tech Stack

| Component                              | Description                |
| -------------------------------------- | -------------------------- |
| **Java 11+**                           | Language                   |
| **Spring Boot 2.6+**                   | Framework                  |
| **Spring Cloud Netflix Eureka Server** | Service registry           |
| **Spring Cloud Config (optional)**     | Externalized configuration |
| **Gradle**                             | Build system               |

---

## ⚙️ Configuration

The server’s configuration can be set via `application.yml` or through a Spring Cloud Config server.

### Example `application.yml`

```yaml
server:
  port: 8761

spring:
  application:
    name: delivery-eureka-server
  cloud:
    config:
      uri: http://localhost:8888
      fail-fast: false

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      defaultZone: http://localhost:8761/eureka/
  server:
    wait-time-in-ms-when-sync-empty: 0
  instance:
    hostname: localhost
```

---

## 🧩 Project Structure

```
delivery-eureka-server/
├── src/
│   ├── main/
│   │   ├── java/com/guavapay/eureka/
│   │   │   ├── DeliveryEurekaServerApplication.java  # Main Spring Boot entry point
│   │   └── resources/
│   │       ├── application.yml                      # Local configuration
│   │       └── bootstrap.yml                        # Spring Cloud bootstrap (optional)
├── build.gradle
├── settings.gradle
└── README.md
```

---

## 🧰 Build & Run

### Build

```bash
./gradlew clean build
```

### Run Locally

```bash
./gradlew bootRun
```

or run the built JAR:

```bash
java -jar build/libs/delivery-eureka-server-0.0.1-SNAPSHOT.jar
```

Once started, open the Eureka Dashboard in your browser:

👉 [http://localhost:8761](http://localhost:8761)

---

## 🧪 Verify Registration

When other microservices (like `gw-delivery`, `ms-delivery-admin`, or `ms-courier-order`) are started with proper Eureka configuration, you’ll see them automatically appear on the Eureka Dashboard under the **Instances currently registered with Eureka** section.

Example:

```
Application      AMIs    Availability Zones    Status
MS-DELIVERY-ADMIN    n/a    (1)                 UP (1) - http://localhost:8081
GW-DELIVERY          n/a    (1)                 UP (1) - http://localhost:8080
MS-COURIER-ORDER     n/a    (1)                 UP (1) - http://localhost:8082
```

---

## 🔄 Clustering (Optional)

You can run multiple Eureka servers for redundancy.
Example peer configuration for two-node cluster:

### Node 1 (`application-peer1.yml`)

```yaml
server:
  port: 8761
eureka:
  instance:
    hostname: eureka-peer1
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://eureka-peer2:8762/eureka/
```

### Node 2 (`application-peer2.yml`)

```yaml
server:
  port: 8762
eureka:
  instance:
    hostname: eureka-peer2
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://eureka-peer1:8761/eureka/
```

---

## 🩺 Health Check

Eureka includes built-in health monitoring through Spring Boot Actuator.
Access via:

```
GET http://localhost:8761/actuator/health
```

Expected response:

```json
{
  "status": "UP"
}
```

---

## 🧠 Troubleshooting

| Issue                       | Possible Cause              | Solution                                          |
| --------------------------- | --------------------------- | ------------------------------------------------- |
| Dashboard not accessible    | Wrong port or startup error | Check `server.port` and logs                      |
| Services not showing up     | Clients not registered      | Ensure Eureka client config is correct            |
| Config Server not reachable | Config URI invalid          | Check `spring.cloud.config.uri`                   |
| Peers not syncing           | Cluster misconfiguration    | Validate `eureka.client.service-url` in each peer |

---

## 👥 Contributors

* **Eldar Novruzov** – Lead Developer
* Contributions welcome! Feel free to open issues or submit pull requests.

---

## 📄 License

Licensed under the **MIT License**.
See the [LICENSE](./LICENSE) file for full terms.

---
