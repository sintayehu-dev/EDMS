# 🚀 Event-Driven Microservices with Spring Boot & Kafka

![Java](https://img.shields.io/badge/Java-17-orange)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.0.0--M3-green)
![Kafka](https://img.shields.io/badge/Apache%20Kafka-Event%20Streaming-black)
![Maven](https://img.shields.io/badge/Maven-Build-blue)

## 📖 Overview

This project demonstrates a robust **Event-Driven Architecture** using **Spring Boot** and **Apache Kafka**. It simulates a real-world e-commerce scenario where order placement triggers asynchronous events handled by multiple downstream services.

The system is composed of a producer service (`order-service`) that publishes events to a Kafka topic, and consumer services (`stock-service`, `email-service`) that process these events in real-time.

## 🏗️ Architecture

The application is built using a microservices architecture pattern:

| Service | Role | Port | Description |
|---------|------|------|-------------|
| **Order Service** | Producer | `8080` | Handles order creation and publishes `OrderEvent` to Kafka. |
| **Stock Service** | Consumer | `8081` | Listens for order events to update inventory/stock database. |
| **Email Service** | Consumer | `8082` | Listens for order events to send email notifications to customers. |
| **Base Domains** | Shared Lib | N/A | Contains shared DTOs (`Order`, `OrderEvent`) used by all services. |

**Kafka Topic:** `order_topics`

## 🛠️ Tech Stack

- **Java 17**
- **Spring Boot 3.0.0-M3**
- **Spring Cloud**
- **Spring for Apache Kafka**
- **Apache Kafka & Zookeeper**
- **MySQL Database**
- **Lombok**
- **Maven**

## ⚙️ Setup & Installation

### Prerequisites
1.  **Java 17** installed.
2.  **Maven** installed.
3.  **Apache Kafka** installed and running locally.

### 1. Start Zookeeper & Kafka
Navigate to your Kafka installation directory and run the following commands in separate terminals:

```bash
# Start Zookeeper
.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties

# Start Kafka Broker
.\bin\windows\kafka-server-start.bat .\config\server.properties
```

### 2. build the project
From the root directory, build the project to install the shared `base-domains` library:

```bash
mvn clean install -DskipTests
```

## 🚀 Running the Application

Run the services in the following order:

1.  **Order Service**:
    ```bash
    cd order-service
    mvn spring-boot:run
    ```
2.  **Stock Service**:
    ```bash
    cd stock-service
    mvn spring-boot:run
    ```
3.  **Email Service**:
    ```bash
    cd email-service
    mvn spring-boot:run
    ```

## 🔌 API Usage

### Place an Order

**Endpoint:** `POST http://localhost:8080/api/v1/orders`

**Request Body (JSON):**

```json
{
    "name": "Order 1",
    "qty": 5,
    "price": 199.00
}
```

**Response:**
```text
Order placed successfully ...
```

**Workflow:**
1.  `OrderController` receives the POST request.
2.  `OrderProducer` sends an `OrderEvent` (with status `PENDING`) to the `order_topics` Kafka topic.
3.  `StockConsumer` (in Stock Service) receives the event and logs the processing.
4.  `EmailConsumer` (in Email Service) receives the event and logs the email notification.

## 📂 Project Structure

```
EDMS/
├── base-domains/       # Shared DTO modules
├── email-service/      # Kafka Consumer (Email) - Port 8082
├── order-service/      # Kafka Producer (Order) - Port 8080
├── stock-service/      # Kafka Consumer (Stock) - Port 8081
└── pom.xml             # Parent POM
```
