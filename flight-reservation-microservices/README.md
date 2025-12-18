# ✈️ Flight Reservation System – Spring Boot Microservices

A **Flight Reservation System** built using **Spring Boot Microservices**, demonstrating real-world backend architecture concepts such as **service discovery, API gateways, centralized configuration, and domain-based service separation**.

This project focuses on **backend system design and microservice interaction**, not UI richness.

---

## 📌 What This Project Does

The system allows users to:

* Search available flights
* Book a flight
* Perform passenger check-in

Each capability is implemented as an **independent microservice**, following real enterprise backend patterns.

---

## 🧱 High-Level Architecture (HLD)

```
+------------------+
|     Browser      |
|  (User Access)   |
+--------+---------+
         |
         v
+------------------+
|    Website UI    |
|  (Spring MVC)    |
|  Port: 8001      |
+--------+---------+
         |
         v
+----------------------------------+
|          API Gateways             |
|  (Zuul + Hystrix Circuit Breaker) |
+-----+-----------+-----------+----+
      |           |           |
      v           v           v
+---------+   +---------+   +-----------+
| Search  |   | Booking |   | Check-in  |
| Service |   | Service |   | Service   |
+----+----+   +----+----+   +-----+-----+
     |             |              |
     v             v              v
+---------+   +---------+   +-----------+
|  H2 DB  |   |  H2 DB  |   |   H2 DB   |
+---------+   +---------+   +-----------+
```

---

## 🧩 Microservices Overview

| Service            | Responsibility            | Port     |
| ------------------ | ------------------------- | -------- |
| config-server      | Centralized configuration | 8888     |
| eurekaserver       | Service discovery         | 8761     |
| search             | Flight search             | 8090     |
| search-apigateway  | Gateway for search        | 8095     |
| fares              | Fare information          | internal |
| fares-apigateway   | Gateway for fares         | internal |
| book               | Flight booking            | 8080     |
| book-apigateway    | Gateway for booking       | 8065     |
| checkin            | Passenger check-in        | 8081     |
| checkin-apigateway | Gateway for check-in      | 8070     |
| website            | UI client                 | 8001     |

---

## 🔧 Configuration Management

### Config Server

* Uses **Spring Cloud Config**
* Runs in **native mode**
* Reads configuration from:

```
External Properties/
```

Example files:

```
search-service.properties
search-apigateway.properties
website.properties
application.properties
```

This ensures:

* No hardcoded ports
* Centralized configuration
* Easy environment changes

---

## 🔍 Service Discovery

### Eureka Server

* All services register themselves
* Enables lookup by service name
* UI available at:

```
http://localhost:8761
```

---

## 🔁 Request Flow Example – Flight Search

```
Browser
  ↓
Website (UI)
  ↓
search-apigateway
  ↓
search-service
  ↓
H2 Database
```

---

## 🧠 Low-Level Design (LLD – Internal Structure)

Each backend service follows the same layered design:

```
Controller
   ↓
Component (Service Layer)
   ↓
Repository (JPA)
   ↓
H2 Database
```

### Package Structure (Same for all services)

```
controller/   → REST APIs
component/    → Business logic
repository/   → Database access
entity/       → JPA entities
```

---

## 🗄️ Database Details

* **Database Used:** H2 (in-memory)
* Each service has its **own database**
* Data is **pre-seeded**
* Only existing dates return results

Example:

* `22-JAN-18` → returns flights
* Any new date → empty result

✅ This behavior is **expected and correct**

---

## 🛡️ Fault Tolerance

### Hystrix

* Used at API Gateway level
* Prevents cascading failures
* Dashboard module included

Purpose:

> If one service goes down, the entire system does not fail.

---

## 🚫 What This Project Intentionally Does NOT Include

* Authentication / Authorization
* Production database (MySQL/Postgres)
* Docker / Kubernetes
* Distributed tracing

Reason:

> Focus is on **clean backend architecture**, not deployment tooling.

---

## ▶️ How to Build the Project

From any service directory:

```bash
mvn clean package -DskipTests
```

---

## ▶️ How to Run the System (Correct Order)

### 1. Config Server

```bash
cd config-server
java -jar target/config-server-0.0.1-SNAPSHOT.jar
```

### 2. Eureka Server

```bash
cd eurekaserver
java -jar target/eurekaserver-0.0.1-SNAPSHOT.jar
```

### 3. Backend Services

```bash
cd search
java -jar target/search-0.0.1-SNAPSHOT.jar

cd fares
java -jar target/fares-0.0.1-SNAPSHOT.jar

cd book
java -jar target/book-0.0.1-SNAPSHOT.jar

cd checkin
java -jar target/checkin-0.0.1-SNAPSHOT.jar
```

### 4. API Gateways

```bash
cd search-apigateway
java -jar target/search-apigateway-0.0.1-SNAPSHOT.jar

cd fares-apigateway
java -jar target/fares-apigateway-0.0.1-SNAPSHOT.jar

cd book-apigateway
java -jar target/book-apigateway-0.0.1-SNAPSHOT.jar

cd checkin-apigateway
java -jar target/checkin-apigateway-0.0.1-SNAPSHOT.jar
```

### 5. Website (UI)

```bash
cd website
java -jar target/website-0.0.1-SNAPSHOT.jar
```

Access UI:

```
http://localhost:8001
```
