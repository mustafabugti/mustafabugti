 

# Weather App Microservices

This project demonstrates a weather application divided into three distinct microservices, each handling a specific functionality. The services are built using Flask and Dockerized for easy deployment.

---

## Microservices Breakdown

### 1. **Weather Data Service**
- Fetches weather data from the OpenWeatherMap API.
- Exposes an endpoint to retrieve weather information for a given city.

### 2. **User Preferences Service**
- Manages user-specific preferences such as temperature units and default location.
- Allows saving and retrieving user preferences.

### 3. **Notification Service**
- Sends notifications (e.g., weather alerts or daily summaries) to users.
- Simulates notification sending with a simple API endpoint.

---

## Project Structure

```plaintext
weather-app/
├── weather-data-service/
│   ├── app.py
│   ├── Dockerfile
├── user-preferences-service/
│   ├── app.py
│   ├── Dockerfile
├── notification-service/
│   ├── app.py
│   ├── Dockerfile
├── docker-compose.yml
```

---

## Prerequisites

1. **Docker**: Install Docker on your system.
   ```bash
   sudo yum install -y docker
   sudo systemctl start docker
   sudo systemctl enable docker
   docker --version
   ```
2. **Docker Compose** (optional): For managing all services together.
   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   docker-compose --version
   ```

---

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/weather-app.git
cd weather-app
```

### 2. Build and Run the Services

#### Using Docker Compose (Recommended)

```bash
docker-compose up --build
```

#### Running Each Service Individually

1. **Weather Data Service**:
   ```bash
   cd weather-data-service
   docker build -t weather-data-service .
   docker run -d -p 5001:5001 weather-data-service
   ```

2. **User Preferences Service**:
   ```bash
   cd ../user-preferences-service
   docker build -t user-preferences-service .
   docker run -d -p 5002:5002 user-preferences-service
   ```

3. **Notification Service**:
   ```bash
   cd ../notification-service
   docker build -t notification-service .
   docker run -d -p 5003:5003 notification-service
   ```

---

## API Endpoints

### 1. **Weather Data Service**

- **Endpoint**: `/weather`
- **Method**: `GET`
- **Parameters**:
  - `city`: Name of the city.
- **Example**:
  ```bash
  curl "http://localhost:5001/weather?city=London"
  ```

---

### 2. **User Preferences Service**

- **Endpoint**: `/preferences`
- **Methods**:
  - `POST`: Save user preferences.
  - `GET`: Retrieve user preferences.
- **Example**:
  - Save preferences:
    ```bash
    curl -X POST -H "Content-Type: application/json" -d '{"user_id": "123", "preferences": {"unit": "metric", "location": "London"}}' "http://localhost:5002/preferences"
    ```
  - Get preferences:
    ```bash
    curl "http://localhost:5002/preferences?user_id=123"
    ```

---

### 3. **Notification Service**

- **Endpoint**: `/send_notification`
- **Method**: `GET`
- **Example**:
  ```bash
  curl "http://localhost:5003/send_notification"
  ```

---

## Push Images to Docker Hub

1. **Tag the images**:
   ```bash
   docker tag weather-data-service mustafabugti/weather-data-service
   docker tag user-preferences-service mustafabugti/user-preferences-service
   docker tag notification-service mustafabugti/notification-service
   ```

2. **Push the images**:
   ```bash
   docker push mustafabugti/weather-data-service
   docker push mustafabugti/user-preferences-service
   docker push mustafabugti/notification-service
   ```

---

## Pull and Run on Another System

1. **Pull the images**:
   ```bash
   docker pull mustafabugti/weather-data-service
   docker pull mustafabugti/user-preferences-service
   docker pull mustafabugti/notification-service
   ```

2. **Run the containers**:
   ```bash
   docker run -d -p 5001:5001 mustafabugti/weather-data-service
   docker run -d -p 5002:5002 mustafabugti/user-preferences-service
   docker run -d -p 5003:5003 mustafabugti/notification-service
   ```

---

## Testing the Application

1. **Weather Data Service**:
   ```bash
   curl "http://localhost:5001/weather?city=London"
   ```

2. **User Preferences Service**:
   - Save preferences:
     ```bash
     curl -X POST -H "Content-Type: application/json" -d '{"user_id": "123", "preferences": {"unit": "metric", "location": "London"}}' "http://localhost:5002/preferences"
     ```
   - Retrieve preferences:
     ```bash
     curl "http://localhost:5002/preferences?user_id=123"
     ```

3. **Notification Service**:
   ```bash
   curl "http://localhost:5003/send_notification"
   ```

---
