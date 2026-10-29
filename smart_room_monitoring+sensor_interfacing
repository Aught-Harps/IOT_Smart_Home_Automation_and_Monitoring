#include "DHT.h"
#include <WiFi.h>
#include <PubSubClient.h>

const char* ssid = "Wokwi-GUEST";
const char* password = "";

const char* mqtt_server = "broker.hivemq.com";

WiFiClient espClient;
PubSubClient client(espClient);

#define DHT_PIN 4
#define DHT_TYPE DHT22
#define LDR_PIN 34
#define LED_PIN 2

DHT dht(DHT_PIN, DHT_TYPE);


void setup_wifi() {

  Serial.print("Connecting to WiFi");

  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println();
  Serial.println("WiFi connected");
  Serial.println(WiFi.localIP());
}


void reconnect_mqtt() {

  while (!client.connected()) {

    Serial.print("Connecting to MQTT...");

    String clientID = "SmartRoomESP32-";
    clientID += String(random(0xffff), HEX);

    if (client.connect(clientID.c_str())) {

      Serial.println("connected");

    } else {

      Serial.print("failed, rc=");
      Serial.println(client.state());

      delay(2000);
    }
  }
}


void setup() {

  Serial.begin(115200);

  dht.begin();

  setup_wifi();

  client.setServer(mqtt_server, 1883);

  pinMode(LDR_PIN, INPUT);
  pinMode(LED_PIN, OUTPUT);

  digitalWrite(LED_PIN, LOW);

  Serial.println("Smart Room Monitoring System");
  Serial.println("----------------------------");
}


void loop() {

  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();

  int light = analogRead(LDR_PIN);


  // Check DHT22
  if (isnan(temperature) || isnan(humidity)) {

    Serial.println("DHT22 reading failed!");

    digitalWrite(LED_PIN, LOW);

    delay(2000);

    return;
  }


  // Threshold decision
  bool alert = false;

  if (temperature > 30) {
    alert = true;
  }

  if (light < 500) {
    alert = true;
  }


  // Control LED
  if (alert) {
    digitalWrite(LED_PIN, HIGH);
  } 
  else {
    digitalWrite(LED_PIN, LOW);
  }


  // Connect to MQTT
  if (!client.connected()) {
    reconnect_mqtt();
  }

  client.loop();


  // Create JSON packet
  String payload = "{";

  payload += "\"temperature\":" + String(temperature, 1) + ",";
  payload += "\"humidity\":" + String(humidity, 1) + ",";
  payload += "\"light\":" + String(light) + ",";
  payload += "\"alert\":" + String(alert ? "true" : "false") + ",";
  payload += "\"led\":" + String(alert ? "true" : "false");

  payload += "}";


  // Publish data
  client.publish("smartroom/data", payload.c_str());


  // Serial output
  Serial.print("MQTT: ");
  Serial.println(payload);

  Serial.print("Temperature: ");
  Serial.print(temperature);

  Serial.print(" °C | Humidity: ");
  Serial.print(humidity);

  Serial.print(" % | Light: ");
  Serial.print(light);

  Serial.print(" | Alert: ");

  if (alert) {
    Serial.println("YES | LED: ON");
  } 
  else {
    Serial.println("NO | LED: OFF");
  }


  delay(2000);
}
