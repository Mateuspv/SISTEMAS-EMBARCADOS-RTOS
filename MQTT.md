### O MQTT funciona com o Sub enviando mensagem para o pub atravez do Broker(com topicos) declarado sendo usado no exemplo abaixo como "ControleRellay" "ON"

# Codigo utilizado

``` sh
#include <Arduino.h>
#include <WiFi.h>
#include <PubSubClient.h>

const char* ssid = "MateusFe";
const char* password = "quesenha";
const char* mqtt_server = "192.168.25.149";

WiFiClient espClient;
PubSubClient client(espClient);
const int Relay = 2;

void setup_wifi() {
  delay(10);
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  String message;
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println(message);

  if (String(topic) == "ControleRelay"){
    if (message == "ON") {
      digitalWrite(Relay, LOW);
    } else if (message == "OFF") {
      digitalWrite(Relay, HIGH);
    }
  }
}

void setup() {
  pinMode(Relay, OUTPUT);
  Serial.begin(9600);
  setup_wifi();
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);
}

void reconnect() {
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    if (client.connect("ESP8266Client")) {
      Serial.println("connected");
      client.subscribe("ControleRelay");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      delay(5000);
    }
  }
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();
}
``` sh


![Servicos](https://github.com/Mateuspv/SISTEMAS-EMBARCADOS-RTOS/assets/76630728/69d7106b-9e62-41e9-82b4-e5d14da8ab63)



