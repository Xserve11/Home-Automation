#include <Wire.h>
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Adafruit_Sensor.h>
#include <DHT.h>
#include <Blynk.h>


#define SCREEN_WIDTH 128  // OLED display width, in pixels
#define SCREEN_HEIGHT 64  // OLED display height, in pixels


Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

#define DHTPIN 14  //D5
#define DHTTYPE DHT22
#define RELAY_PIN 0    //D3
#define RELAY_PIN_2 2  //D4
#define TARGET_TEMP 30
#define LDR_PIN 12  //D6

#define BLYNK_PRINT Serial

DHT dht(DHTPIN, DHTTYPE);

char auth[] = "Blynk Token";
char ssid[] = "WIfi Name";
char pass[] = "Wifi Password";

// Declare Timer
BlynkTimer timer;

int manual;

void setup() {
  // Start Serial
  Serial.begin(9600);
  // Start DHT
  dht.begin();

  // Set Timer Function

  // Start Blynk
  Blynk.begin(auth, ssid, pass, IPAddress(41, 216, 186, 178), 8080);

  // Start Oled
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("SSD1306 allocation failed"));
    for (;;)
      ;
  }
  display.clearDisplay();
  display.setTextColor(WHITE);

  // Relay INIT
  pinMode(RELAY_PIN, OUTPUT);
  pinMode(RELAY_PIN_2, OUTPUT);
  pinMode(LDR_PIN, INPUT);
}

BLYNK_WRITE(V8) {
  manual = param.asInt();
}

void loop() {
  //read temperature and humidity
  float t = dht.readTemperature();
  float h = dht.readHumidity();

  if (isnan(h) || isnan(t)) {
    Serial.println("Failed to read from DHT sensor!");
  }

  // Read LDR Voltage
  int ldrValue = digitalRead(LDR_PIN);

  // clear display
  display.clearDisplay();

  // display temperature
  display.setTextSize(1);
  display.setCursor(0, 0);
  display.print("Temperature: ");
  display.setTextSize(2);
  display.setCursor(0, 10);
  display.print(t);
  display.print(" ");
  display.setTextSize(1);
  display.cp437(true);
  display.write(167);
  display.setTextSize(2);
  display.print("C");

  // display humidity
  display.setTextSize(1);
  display.setCursor(0, 35);
  display.print("Humidity: ");
  display.setTextSize(2);
  display.setCursor(0, 45);
  display.print(h);
  display.print(" %");

  display.display();

  switch (manual) {
    case 1:
      {
        // Set Temperature Relay Trigger
        if (t < TARGET_TEMP) {
          digitalWrite(RELAY_PIN, HIGH);
        } else {
          digitalWrite(RELAY_PIN, LOW);
        }
        // Set LDR
        if (ldrValue == LOW) {
          digitalWrite(RELAY_PIN_2, HIGH);
        } else {
          digitalWrite(RELAY_PIN_2, LOW);
        }
        break;
      }
    default:
      {
      }  // Set Temperature Relay Trigger
  }
  // Show Temperature And Humidity On Blynk
  Blynk.virtualWrite(V6, h);
  Blynk.virtualWrite(V5, t);
  Blynk.run();
  timer.run();
}
