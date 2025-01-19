 

# Weather App Microservices

This repository contains a **Weather App** divided into three microservices. Each microservice handles a specific functionality to ensure modularity, scalability, and ease of management.

## Microservices Overview

1. **Weather Data Service**  
   Fetches weather data from the OpenWeatherMap API and provides it to other services or users.

2. **User Preferences Service**  
   Manages user-specific preferences like temperature units and default locations.

3. **Notification Service**  
   Sends notifications such as weather alerts and daily summaries based on user preferences and weather conditions.

---

## Project Setup

### Prerequisites
- **Docker** installed on your system.  
  Install Docker on CentOS (if not already done):

  ```bash
  sudo yum install -y docker
  sudo systemctl start docker
  sudo systemctl enable docker
  docker --version
  ```

### Directory Structure

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

## Microservices Setup

### 1. Weather Data Service
This service fetches weather data from the OpenWeatherMap API.

#### Python Code (`app.py`)

```python
from flask import Flask, request, jsonify
import requests

app = Flask(__name__)

API_KEY = "your_openweathermap_api_key"  # Replace with your OpenWeatherMap API key

@app.route('/weather', methods=['GET'])
def get_weather():
    city = request.args.get('city')
    if not city:
        return jsonify({"error": "Please provide a city name"}), 400
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}&units=metric"
    response = requests.get(url)
    
    if response.status_code != 200:
        return jsonify({"error": "City not found"}), 404
    
    data = response.json()
    return jsonify({
        "city": city,
        "temperature": data['main']['temp'],
        "description": data['weather'][0]['description']
    })

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5001)
```

#### Dockerfile

```Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY app.py /app
RUN pip install flask requests
EXPOSE 5001
CMD ["python", "app.py"]
```

---

### 2. User Preferences Service
This service manages user-specific preferences.

#### Python Code (`app.py`)

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

user_preferences = {}

@app.route('/preferences', methods=['GET', 'POST'])
def manage_preferences():
    if request.method == 'POST':
        data = request.json
        user_id = data.get('user_id')
        preferences = data.get('preferences')
        user_preferences[user_id] = preferences
        return jsonify({"message": "Preferences saved successfully!"}), 201
    
    user_id = request.args.get('user_id')
    if user_id and user_id in user_preferences:
        return jsonify(user_preferences[user_id])
    return jsonify({"error": "User preferences not found"}), 404

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5002)
```

#### Dockerfile

```Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY app.py /app
RUN pip install flask
EXPOSE 5002
CMD ["python", "app.py"]
```

---

### 3. Notification Service
This service sends notifications based on user preferences and weather conditions.

#### Python Code (`app.py`)

```python
from flask import Flask, jsonify
import time

app = Flask(__name__)

@app.route('/send_notification', methods=['GET'])
def send_notification():
    # Simulating sending a notification
    time.sleep(2)  # Simulating some delay in sending notification
    return jsonify({"message": "Notification sent successfully!"})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5003)
```

#### Dockerfile

```Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY app.py /app
RUN pip install flask
EXPOSE 5003
CMD ["python", "app.py"]
```

---

### Docker Compose Setup
To manage all services together:

#### Docker Compose File (`docker-compose.yml`)

```yaml
version: '3.8'

services:
  weather-data-service:
    build: ./weather-data-service
    ports:
      - "5001:5001"
  
  user-preferences-service:
    build: ./user-preferences-service
    ports:
      - "5002:5002"
  
  notification-service:
    build: ./notification-service
    ports:
      - "5003:5003"
```

---

## Running the Microservices

### Using Docker Compose
1. Build and start all services:
   ```bash
   docker-compose up --build
   ```

2. Stop the services:
   ```bash
   docker-compose down
   ```

### Testing the Microservices

1. **Weather Data Service**:
   ```bash
   curl "http://localhost:5001/weather?city=London"
   ```

2. **User Preferences Service**:
   ```bash
   curl -X POST -H "Content-Type: application/json" -d '{"user_id": "123", "preferences": {"unit": "metric", "location": "London"}}' "http://localhost:5002/preferences"
   curl "http://localhost:5002/preferences?user_id=123"
   ```

3. **Notification Service**:
   ```bash
   curl "http://localhost:5003/send_notification"
   ```

---

## Pushing Images to Docker Hub

Replace `yourusername` with your Docker Hub username (`mustafabugti`).

1. Tag and push each image:
   ```bash
   docker tag weather-data-service mustafabugti/weather-data-service
   docker tag user-preferences-service mustafabugti/user-preferences-service
   docker tag notification-service mustafabugti/notification-service

   docker push mustafabugti/weather-data-service
   docker push mustafabugti/user-preferences-service
   docker push mustafabugti/notification-service
   ```

2. Pull and run the services on any system:
   ```bash
   docker pull mustafabugti/weather-data-service
   docker pull mustafabugti/user-preferences-service
   docker pull mustafabugti/notification-service

   docker run -d -p 5001:5001 mustafabugti/weather-data-service
   docker run -d -p 5002:5002 mustafabugti/user-preferences-service
   docker run -d -p 5003:5003 mustafabugti/notification-service
   ```

---

This Weather App is divided into three microservices:
- **Weather Data Service**: Fetches weather data.
- **User Preferences Service**: Manages user preferences.
- **Notification Service**: Sends notifications.

