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
