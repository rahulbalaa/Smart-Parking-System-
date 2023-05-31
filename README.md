
#include <WiFi.h>

#include <WiFiClient.h>

#include <ESPAsyncWebServer.h>

const char* ssid = "Your WiFi SSID";

const char* password = "Your WiFi Password";

AsyncWebServer server(80);

int parkingSpaces = 5;  // Total number of parking spaces

int availableSpaces = parkingSpaces;  // Number of currently available parking spaces

void setup() {

  Serial.begin(115200);

  // Connect to Wi-Fi

  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {

    delay(1000);

    Serial.println("Connecting to WiFi...");

  }

  // Print the IP address

  Serial.println(WiFi.localIP());

  // Route for root ("/")

  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){

    String message = "Total parking spaces: " + String(parkingSpaces) + "<br>";

    message += "Available parking spaces: " + String(availableSpaces);

    request->send(200, "text/html", message);

  });

  // Route to increment parking spaces

  server.on("/increment", HTTP_GET, [](AsyncWebServerRequest *request){

    if (availableSpaces < parkingSpaces) {

      availableSpaces++;

    }

    request->send(200, "text/plain", "Incremented available parking spaces.");

  });

  // Route to decrement parking spaces

  server.on("/decrement", HTTP_GET, [](AsyncWebServerRequest *request){

    if (availableSpaces > 0) {

      availableSpaces--;

    }

    request->send(200, "text/plain", "Decremented available parking spaces.");

  });

  // Start server

  server.begin();

}

void loop() {

  // Handle incoming client requests

  server.handleClient();

}
