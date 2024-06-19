# SISTEMAS-EMBARCADOS-RTOS
Repositório utilizado na matéria de projeto integrador da faculdade

# Esquemático

![1](https://github.com/Mateuspv/SISTEMAS-EMBARCADOS-RTOS/assets/76630728/d1d18b0c-d67c-40f5-8560-1b13eb57e03a)

# PCB

![2](https://github.com/Mateuspv/SISTEMAS-EMBARCADOS-RTOS/assets/76630728/567c2706-d03e-4d94-b293-703927d153c1)

# 3D
![3](https://github.com/Mateuspv/SISTEMAS-EMBARCADOS-RTOS/assets/76630728/cb049af1-bdd0-45f7-8add-ae8082eaf8d0)


### O MQTT funciona com o Sub enviando mensagem para o pub atravez do Broker(com topicos) declarado sendo usado no exemplo abaixo como "ControleRellay" "ON"

# Codigo utilizado

``` sh
#include <Arduino.h>
#include <WiFi.h>
#include <PubSubClient.h>
// Variaveis para conectar na internet
const char* ssid = "MateusFe";
const char* password = "quesenha";
const char* mqtt_server = "192.168.25.149";

WiFiClient espClient;
PubSubClient client(espClient);
const int Relay = 2;

void setup_wifi() {
// Espera 10 milissegundos antes de iniciar a conexão
delay(10);
Serial.println();
Serial.print("Connecting to ");
Serial.println(ssid);

// Inicia a conexão com a rede Wi-Fi usando o SSID e a senha fornecidos
WiFi.begin(ssid, password);

// Loop que espera até que a conexão Wi-Fi seja estabelecida
while (WiFi.status() != WL_CONNECTED) {
  delay(500); // Espera 500 milissegundos
  Serial.print("."); // Imprime um ponto para indicar progresso
}

Serial.println("");
Serial.println("WiFi connected");
Serial.println("IP address: ");
Serial.println(WiFi.localIP()); // Imprime o endereço IP local
}

void callback(char* topic, byte* payload, unsigned int length) {
  // Imprime a mensagem recebida no console serial para depuração
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  
  String message;
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]); // Imprime cada caractere do payload
  }
  Serial.println(message); // Imprime a mensagem completa

  // Verifica se a mensagem é do tópico "ControleRelay"
  if (String(topic) == "ControleRelay"){
    if (message == "ON") {
      digitalWrite(Relay, LOW); // Ativa o relé
    } else if (message == "OFF") {
      digitalWrite(Relay, HIGH); // Desativa o relé
    }
  }
}

void setup() {
  pinMode(Relay, OUTPUT); // Define o pino do relé como saída
  Serial.begin(9600); // Inicializa a comunicação serial a 9600 bps
  setup_wifi(); // Chama a função para configurar a conexão Wi-Fi
  client.setServer(mqtt_server, 1883); // Define o servidor MQTT e a porta
  client.setCallback(callback); // Define a função de callback para mensagens MQTT
}

void reconnect() {
  // Loop que tenta reconectar ao broker MQTT se a conexão for perdida
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Tenta conectar ao broker com o ID "ESP8266Client"
    if (client.connect("ESP8266Client")) {
      Serial.println("connected");
      client.subscribe("ControleRelay"); // Se inscreve no tópico "ControleRelay"
    } else {
      // Caso a conexão falhe, imprime o código de erro
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      delay(5000); // Espera 5 segundos antes de tentar novamente
    }
  }
}

void loop() {
  // Verifica se está conectado ao broker MQTT
  if (!client.connected()) {
    reconnect(); // Se não estiver conectado, chama a função reconnect()
  }
  client.loop(); // Mantém a comunicação com o broker MQTT
}
}
``` sh


Servicos.png
