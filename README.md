# Practica3_PD
Esta práctica tiene como objetivo trabajar con WiFi y Bluetooth utilizando el microprocesador ESP32. Durante la práctica, se creará un servidor web accesible desde un navegador, así como una comunicación serie mediante Bluetooth con una aplicación móvil.

----

## Práctica A: Generación de una Página Web

### Código base: Conexión IP
```cpp
#include <Arduino.h>
#include <WiFi.h> 
#include <WebServer.h> 

void handle_root();

// Configuración de WiFi
const char* ssid = "Phoenix";  
const char* password = "PauLozanoDanes";  

WebServer server(80);  // Servidor web en el puerto 80

void setup() { 
  Serial.begin(115200); 
  Serial.println("Conectando a: "); 
  Serial.println(ssid); 
  
  WiFi.begin(ssid, password); 

  while (WiFi.status() != WL_CONNECTED) { 
    delay(1000); 
    Serial.print("."); 
  } 
  
  Serial.println(""); 
  Serial.println("WiFi conectado correctamente"); 
  Serial.print("Dirección IP: "); 
  Serial.println(WiFi.localIP());  

  server.on("/", handle_root); 
  server.begin(); 
  Serial.println("Servidor HTTP iniciado"); 
  delay(100);  
} 

void loop() { 
  server.handleClient(); 
}

// Página web HTML
String HTML = "<!DOCTYPE html>\ 
<html>\ 
<body>\ 
<h1>Mi Primera Página con ESP32 - Modo Estación &#128522;</h1>\ 
</body>\ 
</html>"; 

// Respuesta al acceder a la raíz "/"
void handle_root() { 
  server.send(200, "text/html", HTML); 
}
```
### Código con Simulación en la Página Web
```cpp
#include <Arduino.h>
#include <WiFi.h>
#include <WebServer.h>

void handle_root();

const char* ssid = "Phoenix";  
const char* password = "PauLozanoDanes";  

WebServer server(80);  

void setup() { 
  Serial.begin(115200); 
  Serial.println("Conectando a: "); 
  Serial.println(ssid); 

  WiFi.begin(ssid, password); 

  while (WiFi.status() != WL_CONNECTED) { 
    delay(1000); 
    Serial.print("."); 
  } 

  Serial.println(""); 
  Serial.println("WiFi conectado correctamente"); 
  Serial.print("Dirección IP: "); 
  Serial.println(WiFi.localIP());  
  
  server.on("/", handle_root); 
  server.begin(); 
  Serial.println("Servidor HTTP iniciado"); 
  delay(100);  
} 

void loop() { 
  server.handleClient(); 
} 

// Página web con animación
String HTML = "<!DOCTYPE html>\ 
<html>\ 
<head>\ 
<style>\ 
@keyframes bounce {\ 
  0% { transform: scale(1); }\ 
  50% { transform: scale(1.5); }\ 
  100% { transform: scale(1); }\ 
}\ 
.circle {\ 
  width: 100px;\ 
  height: 100px;\ 
  background-color: red;\ 
  border-radius: 50%;\ 
  position: absolute;\ 
  top: 50%;\ 
  left: 50%;\ 
  transform: translate(-50%, -50%);\ 
  animation: bounce 2s infinite ease-in-out;\ 
}\ 
</style>\ 
</head>\ 
<body>\ 
<h1 style='text-align:center;'>Animación con ESP32 🚀</h1>\ 
<div class='circle'></div>\ 
</body>\ 
</html>"; 

void handle_root() { 
  server.send(200, "text/html", HTML); 
}
```
### Salida esperada en el Monitor Serial
```Serial Monitor
Conectando a:
Phoenix
...........................
WiFi conectado correctamente
Dirección IP: 192.168.50.114
Servidor HTTP iniciado

```

---

## Práctica B

```cpp
#include <Arduino.h>
#include <NimBLEDevice.h>

#define SERVICE_UUID        "4fafc201-1fb5-459e-8fcc-c5c9c331914b"
#define CHARACTERISTIC_UUID "beb5483e-36e1-4688-b7f5-ea07361b26a8"

NimBLEServer* pServer = nullptr;
NimBLECharacteristic* pCharacteristic = nullptr;

void setup() {
  Serial.begin(115200);
  Serial.println("Iniciando BLE con NimBLE");

  NimBLEDevice::init("ESP32-NimBLE");

  pServer = NimBLEDevice::createServer();
  NimBLEService* pService = pServer->createService(SERVICE_UUID);

  pCharacteristic = pService->createCharacteristic(
    CHARACTERISTIC_UUID,
    NIMBLE_PROPERTY::READ | NIMBLE_PROPERTY::WRITE
  );

  pCharacteristic->setValue("Hola desde ESP32 NimBLE");

  pService->start();

  NimBLEAdvertising* pAdvertising = NimBLEDevice::getAdvertising();
  pAdvertising->addServiceUUID(SERVICE_UUID);
  pAdvertising->setScanResponse(true);
  pAdvertising->setMinPreferred(0x06);
  pAdvertising->setMinPreferred(0x12);
  NimBLEDevice::startAdvertising();

  Serial.println("¡Servidor BLE NimBLE activo!");
}

void loop() {
  delay(2000);
}
```
Este código configura una comunicación Bluetooth BLE entre el ESP32 y un dispositivo móvil, permitiendo el envío y recepción de datos de manera sencilla.

---

## Ejercicios de Mejora

### Ejercicio de Mejora 1

```cpp
#include <Arduino.h>
#include <WiFi.h>
#include <WebServer.h>

void handle_root();

const char* ssid = "Phoenix";  
const char* password = "PauLozanoDanes";  

WebServer server(80);  

String HTML = "<!DOCTYPE html>\ 
<html>\ 
<head><title>ESP32 Access Point</title></head>\ 
<body>\ 
<h1>ESP32 en Modo Access Point</h1>\ 
<p>Estás conectado al ESP32 como punto de acceso.</p>\ 
</body>\ 
</html>"; 

void handle_root() { 
  server.send(200, "text/html", HTML); 
}

void setup() {
  Serial.begin(115200);
  Serial.println("Iniciando Access Point...");

  WiFi.softAP(ssid, password);

  Serial.print("Dirección IP del AP: ");
  Serial.println(WiFi.softAPIP());

  server.on("/", handle_root);
  server.begin();
  Serial.println("Servidor web en modo Access Point iniciado.");
}

void loop() {
  server.handleClient();
}
```
### Ejercicio de Mejora 2
```cpp
#include <Arduino.h>
#include <BLEDevice.h>
#include <BLEUtils.h>
#include <BLEServer.h>

#define SERVICE_UUID        "4fafc201-1fb5-459e-8fcc-c5c9c331914b"
#define CHARACTERISTIC_UUID "beb5483e-36e1-4688-b7f5-ea07361b26a8"

void setup() {
  Serial.begin(115200);
  Serial.println("Iniciando comunicación BLE");

  BLEDevice::init("MyESP32");

  BLEServer *pServer = BLEDevice::createServer();
  BLEService *pService = pServer->createService(SERVICE_UUID);

  BLECharacteristic *pCharacteristic = pService->createCharacteristic(
    CHARACTERISTIC_UUID,
    BLECharacteristic::PROPERTY_READ |
    BLECharacteristic::PROPERTY_WRITE
  );

  pCharacteristic->setValue("Hola Mundo desde ESP32");

  pService->start();

  BLEAdvertising *pAdvertising = BLEDevice::getAdvertising();
  pAdvertising->addServiceUUID(SERVICE_UUID);
  pAdvertising->setScanResponse(true);
  pAdvertising->setMinPreferred(0x06);
  pAdvertising->setMinPreferred(0x12);
  BLEDevice::startAdvertising();

  Serial.println("¡Servidor BLE disponible!");
}

void loop() {
  delay(2000);
}
```
Este código convierte al ESP32 en un servidor BLE utilizando la biblioteca BLEDevice, permitiendo leer y escribir datos desde otros dispositivos compatibles.
