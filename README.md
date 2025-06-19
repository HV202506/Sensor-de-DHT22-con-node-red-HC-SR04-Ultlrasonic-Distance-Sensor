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

//agregados para la distancia
const int Trigger = 0;   //Pin digital 2 para el Trigger del sensor
const int Echo = 2;   //Pin digital 3 para el Echo del sensor
 // fin del agregado 



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
 
 //agregado para la distancia
  pinMode(Trigger, OUTPUT); //pin como salida
  pinMode(Echo, INPUT);  //pin como entrada
  digitalWrite(Trigger, LOW);//Inicializamos el pin con 0
 // fin del agregado 
 
  
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

//agregado para la distancia
  long t; //timepo que demora en llegar el eco
  long d; //distancia en centimetros

  digitalWrite(Trigger, HIGH);
  delayMicroseconds(10);          //Enviamos un pulso de 10us
  digitalWrite(Trigger, LOW);
  
  t = pulseIn(Echo, HIGH); //obtenemos el ancho del pulso
  d = t/59;             //escalamos el tiempo a una distancia en cm
 // fin del agregado 


    doc["DEVICE"] = "ESP32";
    doc["Nombre"] ="Hector Vega";
    //doc["Empresa"] = "Educatronicos";
    doc["TEMPERATURA"] = String(data.temperature, 1);
    doc["HUMEDAD"] = String(data.humidity, 1);
    doc["DISTANCIA"] = String(d);
   

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

![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/librerias.png?raw=true)

## 1.3 Hacer las conexiones entre los elementos del entorno simulado del siguiente modo como se muestra en la imagen

![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/Sin%20t%C3%ADtulo%202%20Hardware.png?raw=true)

## 1.4 Intrucciones de operación

1. Iniciar la simulación.
2. Observar el envio de los datos a traves del monitor serial.
3. Variar los valores de la temperatura, humedad y distancia para observar que se actualizan adecuadamente

# 2 Node-RED 
## 2.1 Dentro del Node-RED ya funcionando se agregara la siguiente libreria

- node-red-dashboard
  
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/iDashboard%201.png?raw=true)
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/iDashboard%202.png?raw=true)
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/iDashboard%203.png?raw=true)
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/iDashboard%204.png?raw=true)
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/iDashboard%205.png?raw=true)
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/iDashboard%206.png?raw=true)

## 2.2 Integrar en la interfaz los siguientes elementos
- mqtt in (para hacer de entrada de dats)
- json (para comunicacion entre aplicaciones web y servidores)
- debug (para observar la informacion que se esta recibiendo)
- function 1 (se empleara para la temperatura)
- function 2 (se empleara para la humedad)
- function 3 (se empleara para la distancia)
- chart 1 (se empleara para poder observar en graficos los cambios de las variables)
- gauge 1 (temperatura)
- gauge 2 (humedad)
- gauge 3 (distancia)

## 2.3 Conexiones Node-RED
Se interconectaran los elementos tal cual se muestra en la siguiente imagen

![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/conexiones.png?raw=true2)

##  2.4 Configuraciones de los bloques de Node-RED

- mqtt in (para hacer de entrada de dats)

![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/mqtt.png?raw=true)

- json (para comunicacion entre aplicaciones web y
servidores)

![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/json.png?raw=true)
- debug (para observar la informacion que se esta recibiendo)
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/debug.png?raw=true)

- function 1 (se empleara para la temperatura)

```
msg.payload = msg.payload.TEMPERATURA;
msg.topic = "TEMPERATURA";
return msg;
```
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/funcion%201.png?raw=true)

- function 2 (se empleara para la humedad)
```
msg.payload = msg.payload.HUMEDAD;
msg.topic = "HUMEDAD";
return msg;
```
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/funcion%202.png?raw=true)

- function 3 (se empleara para la distancia)
```
msg.payload = msg.payload.DISTANCIA;
msg.topic = "DISTANCIA";
return msg;
```
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/funcion%203.png?raw=true)

- chart 1 (se empleara para poder observar en graficos los cambios de las variables)
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/chart%201.png?raw=true)

- gauge 1 (temperatura)
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/gauge%201.png?raw=true)

- gauge 2 (humedad)
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/gauge%202.png?raw=true) 

- gauge 3 (distancia)
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/gauge%203.png?raw=true)

# Resultados:

En el apartado de Dashboard se encontrara una visualizacion del los valores del entorno a traves de un grafico, aqui se anexa una screenshot de como se ve el resultado final.

Para acceder al dashboard, hacer click en el triangulo de la parte superior derecha
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/dashboard%201.png?raw=true)

Elegir la opcion dashboard y dar click en el icono de visualizar en otra pestaña
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/dashboard%202.png?raw=true)

Observacion del dashboard
![](https://github.com/HV202506/Sensor-de-DHT22-con-node-red-HC-SR04-Ultlrasonic-Distance-Sensor/blob/main/DASHBOARD.png?raw=true)


# Créditos

Desarrollado por: Ing. Héctor Angel Vega Rodríguez
