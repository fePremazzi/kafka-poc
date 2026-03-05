# Kafka Driver Location PoC

This project is a Proof of Concept (PoC) demonstrating a real-time data streaming pipeline using Python and Apache Kafka. It simulates a driver sending GPS location updates and a consumer service processing those updates.

## Project Overview

The project consists of two main Python scripts:

*   **`producer.py`**: Acts as a driver's device. It generates simulated GPS coordinates (latitude, longitude) and sends them to a Kafka topic named `driver-location` every 5 seconds.
*   **`consumer.py`**: Acts as a backend service. It listens to the `driver-location` topic, receives the updates in real-time, and processes (prints) the data.

## Prerequisites

*   **Python 3.6+**
*   **Docker** and **Docker Compose** (for running the Kafka server)

## 1. Setting up Kafka using Docker

To run this project, you need a Kafka broker running locally. The easiest way to set this up is using Docker Compose.

1.  Create a file named `docker-compose.yml` in the root of your project folder with the following content:

    ```yaml
        version: '3'
        services:
        kafka:
            image: confluentinc/cp-kafka:latest
            container_name: kafka
            ports:
            - "9092:9092"
            environment:
            KAFKA_NODE_ID: 1
            KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: 'CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT'
            KAFKA_ADVERTISED_LISTENERS: 'PLAINTEXT://kafka:29092,PLAINTEXT_HOST://localhost:9092'
            KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
            KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS: 0
            KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
            KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
            KAFKA_PROCESS_ROLES: 'broker,controller'
            KAFKA_CONTROLLER_QUORUM_VOTERS: '1@kafka:29093'
            KAFKA_LISTENERS: 'PLAINTEXT://kafka:29092,CONTROLLER://kafka:29093,PLAINTEXT_HOST://0.0.0.0:9092'
            KAFKA_INTER_BROKER_LISTENER_NAME: 'PLAINTEXT'
            KAFKA_CONTROLLER_LISTENER_NAMES: 'CONTROLLER'
            KAFKA_LOG_DIRS: '/tmp/kraft-combined-logs'
            # Replace CLUSTER_ID with a unique base64 UUID using "bin/kafka-storage.sh random-uuid"
            CLUSTER_ID: 'MkU3OEVBNTcwNTJENDM2Qk'
    ```

2.  Start the Kafka services:
    ```bash
    docker-compose up -d
    ```

## 2. Python Setup

Install the required Python libraries from `requirements.txt` to allow your scripts to interact with the Kafka server.

```bash
pip install -r requirements.txt
```

## 3. Running the Project

You will need two separate terminal windows to see the real-time interaction.

**Terminal 1: Start the Consumer**
```bash
python consumer.py
```

**Terminal 2: Start the Producer**
```bash
python producer.py
```

You should see the producer sending JSON data in one terminal, and the consumer immediately receiving and printing the formatted location data in the other.