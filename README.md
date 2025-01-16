sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io

sudo systemctl start docker
sudo systemctl enable docker 

docker --version (It Will Verify the Version of the Docker)

Now Next step is To install Docker-compose
sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
It Will Give Executable Permissions

docker-compose --version

mkdir  weather-app
cd weather-app 

and Now we are going to create Sub Directory 
mkdir weather-data-service
cd weather-data-service 

sudo nano app.py

Then add This Python Code
from flask import Flask, request, jsonify
import requests

app = Flask(__name__)

API_KEY = "3cb90eeb1d49ffd71ad2d4af46e127f9"  # Replace with your OpenWeatherMa>

@app.route('/weather', methods=['GET'])
def get_weather():
    city = request.args.get('city')
    if not city:
        return jsonify({"error": "Please provide a city name"}), 400

    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_>
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
    


        sudo nano dockerfile 
        then add these code 
        FROM python:3.9-slim
WORKDIR /app
COPY app.py /app
RUN pip install flask requests
EXPOSE 5001
CMD ["python", "app.py"] 



    
