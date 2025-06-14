# ESP32 con Sensor-de-DHT22-y-HC-SR04-Ultlrasonic-Distance-Sensor-em-node-red

En este repositorio se mostrara la configuracion yla programacion tanto de el simulador WOKWI y Node-RED, para generar un dashboard en donde podamos interpretar y observar el comportamiento de la distancia, temperatura y humedad del entorno. 

# Introducción:
La plataforma Node-RED nos auxilia a obtener una interfaz observable dentro de nuestra red en este caso nos apoyaremos de un Dashboard para interpretar estos tados y asi lograr una visualizacion de lo que sucede en el entorno simulado.

Este proyecto se realizara empleando las siguientes herramientas 
- WOKWI
- Node-RED

# Material necesario
- WOKWI
- Una tarjeta ESP32 (para adquisicion de datos)
- Sensor ultrasonico "HC-SR04 Ultrasonic Distance Sensor"
- Plataforma Node-RED conectada a un broker

  # Desarrollo de la proyecto

## 1 Entorno simulado (WOKWI)
## 1.1 Codigo para el entorno simulado en WOKWI

  ```
#include <ArduinoJson.h>
#include <WiFi.h>
#include <PubSubClient.h>
#define BUILTIN_LED 2
#include "DHTesp.h"
const int DHT_PIN = 15;
DHTesp dhtSensor;
// Update these with values suitable for your network.

const char* ssid = "Wokwi-GUEST";
const char* password = "";
const char* mqtt_server = "52.29.87.71";
String username_mqtt="hector vega";
String password_mqtt="1234";

WiFiClient espClient;
PubSubClient client(espClient);
unsigned long lastMsg = 0;
#define MSG_BUFFER_SIZE  (50)
char msg[MSG_BUFFER_SIZE];
int value = 0;

void setup_wifi() {

  delay(10);
  // We start by connecting to a WiFi network
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  randomSeed(micros());

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();

  // Switch on the LED if an 1 was received as first character
  if ((char)payload[0] == '1') {
    digitalWrite(BUILTIN_LED, LOW);   
    // Turn the LED on (Note that LOW is the voltage level
    // but actually the LED is on; this is because
    // it is active low on the ESP-01)
  } else {
    digitalWrite(BUILTIN_LED, HIGH);  
    // Turn the LED off by making the voltage HIGH
  }

}

void reconnect() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Create a random client ID
    String clientId = "ESP8266Client-";
    clientId += String(random(0xffff), HEX);
    // Attempt to connect
    if (client.connect(clientId.c_str(), username_mqtt.c_str() , password_mqtt.c_str())) {
      Serial.println("connected");
      // Once connected, publish an announcement...
      client.publish("outTopic", "hello world");
      // ... and resubscribe
      client.subscribe("inTopic");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}

void setup() {
  pinMode(BUILTIN_LED, OUTPUT);     // Initialize the BUILTIN_LED pin as an output
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);
  dhtSensor.setup(DHT_PIN, DHTesp::DHT22);
}

void loop() {


delay(1000);
TempAndHumidity  data = dhtSensor.getTempAndHumidity();
  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  unsigned long now = millis();
  if (now - lastMsg > 2000) {
    lastMsg = now;
    //++value;
    //snprintf (msg, MSG_BUFFER_SIZE, "hello world #%ld", value);

    StaticJsonDocument<128> doc;

    doc["DEVICE"] = "ESP32";
    doc["Nombre"] ="Hector Vega";
    //doc["Empresa"] = "Educatronicos";
    doc["TEMPERATURA"] = String(data.temperature, 1);
    doc["HUMEDAD"] = String(data.humidity, 1);
   

    String output;
    
    serializeJson(doc, output);

    Serial.print("Publish message: ");
    Serial.println(output);
    Serial.println(output.c_str());
    client.publish("DiplomadoHV", output.c_str());
  }
}
  ```
## 1.2 Librerias empleadas para el entorno simulado en WOKWI

Instalar las siguientes librerias

- Arduino Json
- WiFi
- DHT Sensor library for ESPx
- PubSubClient

![](LIBRERIAS)

## 1.3 Hacer las conexiones entre los elementos del entorno simulado del siguiente modo como se muestra en la imagen

![](CONEXIONES)

## 1.4 Intrucciones de operación

1. Iniciar la simulación.
2. Observar el envio de los datos a traves del monitor serial.
3. Variar los valores de la temperatura, humedad y distancia para observar que se actualizan adecuadamente

# 2 Node-RED 
## 2.1 Dentro del Node-RED ya funcionando se agregara la siguiente libreria

- node-red-dashboard
- 
![]() IMAGENES DE DASHBOARD
![]() IMAGENES DE DASHBOARD
![]() IMAGENES DE DASHBOARD
![]() IMAGENES DE DASHBOARD
![]() IMAGENES DE DASHBOARD

## 2.2 Integrar en la interfaz los siguientes elementos
- mqtt in (para hacer de entrada de dats)
- json (para comunicacion entre aplicaciones web y servidores)
- debug (para observar la informacion que se esta recibiendo)
- function 1 (se empleara para la temperatura)
- function 2 (se empleara para la humedad)
- function 3 (se empleara para la distancia)
- gauge 1 (se empleara para poder observar en graficos los cambios de las variables)
- chart 1 (temperatura)
- chart 2 (humedad)
- chart 3 (distancia)

## 2.3 Conexiones Node-RED
Se interconectaran los elementos tal cual se muestra en la siguiente imagen

![](CONEXIONES 2)

##  2.4 Configuraciones de los bloques de Node-RED

- mqtt in (para hacer de entrada de dats)
[] 
- json (para comunicacion entre aplicaciones web y
servidores)
[] 
- debug (para observar la informacion que se esta recibiendo)
[] 
- function 1 (se empleara para la temperatura)
```
msg.payload = msg.payload.TEMPERATURA;
msg.topic = "TEMPERATURA";
return msg;
```
[] 

- function 2 (se empleara para la humedad)
```
msg.payload = msg.payload.HUMEDAD;
msg.topic = "HUMEDAD";
return msg;
```
[] 
- function 3 (se empleara para la distancia)
```
msg.payload = msg.payload.DISTANCIA;
msg.topic = "DISTANCIA";
return msg;
```
[] 
- gauge 1 (se empleara para poder observar en graficos los cambios de las variables)
[] 
- chart 1 (temperatura)
[] 
- chart 2 (humedad)
[] 
- chart 3 (distancia)
[] 



# Créditos

Desarrollado por: Ing. Héctor Angel Vega Rodríguez
