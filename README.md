# Weather App Deployment Using Docker and Docker-Compose

This guide provides step-by-step instructions to deploy a simple weather application using Flask, Docker, and Docker-Compose.

---

## Prerequisites
- CentOS-based system
- `sudo` privileges
- Internet connection

---

## Step 1: Install Docker

1. Install `yum-utils` and add the Docker repository:
   ```bash
   sudo yum install -y yum-utils
   sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   ```

2. Install Docker:
   ```bash
   sudo yum install -y docker-ce docker-ce-cli containerd.io
   ```

3. Start and enable Docker:
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

4. Verify the Docker installation:
   ```bash
   docker --version
   ```

---

## Step 2: Install Docker-Compose

1. Download the Docker-Compose binary:
   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

2. Grant executable permissions:
   ```bash
   sudo chmod +x /usr/local/bin/docker-compose
   ```

3. Verify the Docker-Compose installation:
   ```bash
   docker-compose --version
   ```

---

## Step 3: Create the Weather Application

1. Create the project directory:
   ```bash
   mkdir weather-app
   cd weather-app
   ```

2. Create a subdirectory for the weather service:
   ```bash
   mkdir weather-data-service
   cd weather-data-service
   ```

3. Create the application file:
   ```bash
   sudo nano app.py
   ```

4. Add the following Python code to `app.py`:
   ```python
   from flask import Flask, request, jsonify
   import requests

   app = Flask(__name__)

   API_KEY = "3cb90eeb1d49ffd71ad2d4af46e127f9"  # Replace with your OpenWeatherMap API key

   @app.route('/weather', methods=['GET'])
   def get_weather():
       city = request.args.get('city')
       if not city:
           return jsonify({"error": "Please provide a city name"}), 400

       url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}"
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

5. Create a Dockerfile:
   ```bash
   sudo nano Dockerfile
   ```

6. Add the following content to the `Dockerfile`:
   ```dockerfile
   FROM python:3.9-slim
   WORKDIR /app
   COPY app.py /app
   RUN pip install flask requests
   EXPOSE 5001
   CMD ["python", "app.py"]
   ```

7. Build and run the service:
   ```bash
   sudo docker-compose up -d --build weather-data-service
   ```

---

## Step 4: Create Docker-Compose Configuration

1. Navigate to the project root directory:
   ```bash
   cd ..
   ```

2. Create the `docker-compose.yml` file:
   ```bash
   sudo nano docker-compose.yml
   ```

3. Add the following content to `docker-compose.yml`:
   ```yaml
   version: '3.8'
   services:
     weather-data-service:
       build:
         context: ./weather-data-service
       ports:
         - "5001:5001"
   ```

4. Deploy the application:
   ```bash
   sudo docker-compose up -d
   ```
curl "http://localhost:5001/weather?city=London"
