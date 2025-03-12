
import Adafruit_DHT
import time
import requests

# Sensor Configuration
DHT_SENSOR = Adafruit_DHT.DHT11
DHT_PIN = 4  # GPIO pin where the sensor is connected

# Thingspeak API Configuration (Optional - For Cloud Monitoring)
THINGSPEAK_API_KEY = "your_thingspeak_api_key"
THINGSPEAK_URL = "https://api.thingspeak.com/update"

def read_sensor():
    humidity, temperature = Adafruit_DHT.read(DHT_SENSOR, DHT_PIN)
    if humidity is not None and temperature is not None:
        return temperature, humidity
    else:
        print("Failed to retrieve data from sensor")
        return None, None

def send_to_cloud(temp, hum):
    payload = {
        'api_key': THINGSPEAK_API_KEY,
        'field1': temp,
        'field2': hum
    }
    try:
        response = requests.get(THINGSPEAK_URL, params=payload)
        if response.status_code == 200:
            print("Data sent to ThingSpeak successfully!")
        else:
            print("Failed to send data. Response code:", response.status_code)
    except Exception as e:
        print("Error sending data to cloud:", e)

print("Temperature and Humidity Monitoring System Active")
try:
    while True:
        temperature, humidity = read_sensor()
        if temperature is not None and humidity is not None:
            print(f"Temperature: {temperature:.2f}°C, Humidity: {humidity:.2f}%")
            send_to_cloud(temperature, humidity)
        time.sleep(10)  # Read data every 10 seconds
except KeyboardInterrupt:
    print("Shutting down...")
