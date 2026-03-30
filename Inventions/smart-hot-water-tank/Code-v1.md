This is Arduino framework code targeting the XIAO ESP32-S3, written for ESPHome-compatible structure but runnable standalone for initial bringup.

---

cpp

```cpp
#include <Arduino.h>
#include <Wire.h>
#include <OneWire.h>
#include <DallasTemperature.h>
#include <WiFi.h>
#include <PubSubClient.h>

// ─── PIN DEFINITIONS ──────────────────────────────────────
#define ONE_WIRE_BUS     2    // DS18B20 data pin
#define VEML_INT_PIN     3    // VEML6030 interrupt pin (optional)
#define LED_PIN          21   // Onboard LED for debug

// ─── WIFI / MQTT ──────────────────────────────────────────
const char* WIFI_SSID     = "YOUR_SSID";
const char* WIFI_PASS     = "YOUR_PASSWORD";
const char* MQTT_BROKER   = "YOUR_BROKER_IP";
const int   MQTT_PORT     = 1883;
const char* MQTT_TOPIC    = "waterheater/status";

// ─── VEML6030 I2C ─────────────────────────────────────────
#define VEML6030_ADDR    0x10
#define ALS_CONF         0x00
#define ALS_DATA         0x04

// ─── FLASH CODE TABLE (Resideo WV8840 series) ─────────────
struct FlashCode {
  uint8_t count;
  const char* meaning;
  const char* severity;  // "ok", "warn", "fault"
};

const FlashCode WV8840_CODES[] = {
  { 1,  "Normal operation - standby",           "ok"    },
  { 2,  "Weak pilot signal (thermopile low)",   "warn"  },
  { 3,  "Insufficient heating - auto reset",    "warn"  },
  { 4,  "High limit trip - manual reset req",   "fault" },
  { 5,  "Temperature sensor failure",           "fault" },
  { 6,  "Pilot flame out of sequence",          "fault" },
  { 7,  "Gas control electronics fault",        "fault" },
  { 8,  "Valve internal fault",                 "fault" },
};

// Continuous strobe during heating cycle (no pause between flashes)
// 1 flash per 3 seconds = normal idle

// ─── OBJECTS ──────────────────────────────────────────────
OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature sensors(&oneWire);
WiFiClient espClient;
PubSubClient mqtt(espClient);

// ─── FLASH DETECTION STATE ────────────────────────────────
#define SAMPLE_RATE_MS      50    // Sample VEML6030 every 50ms (20Hz)
#define FLASH_ON_THRESHOLD  50    // Lux above this = LED on
#define FLASH_OFF_THRESHOLD 20    // Lux below this = LED off
#define INTER_BURST_GAP_MS  2000  // Gap between flash sequences
#define MAX_FLASHES         10

uint8_t  flashCount       = 0;
uint8_t  confirmedFlashes = 0;
bool     ledWasOn         = false;
uint32_t lastEdgeTime     = 0;
uint32_t lastSampleTime   = 0;
uint32_t lastReportTime   = 0;

// ─── VEML6030 SETUP ───────────────────────────────────────
void vemlInit() {
  Wire.begin();
  // ALS_CONF: gain=1x, integration=100ms, interrupt disabled, power on
  // Bits [12:11]=00 (gain 1x), [9:6]=0000 (100ms), [1]=0 (int off), [0]=0 (on)
  uint16_t conf = 0x0000;
  Wire.beginTransmission(VEML6030_ADDR);
  Wire.write(ALS_CONF);
  Wire.write(conf & 0xFF);
  Wire.write((conf >> 8) & 0xFF);
  Wire.endTransmission();
  delay(5);
}

float vemlReadLux() {
  Wire.beginTransmission(VEML6030_ADDR);
  Wire.write(ALS_DATA);
  Wire.endTransmission(false);
  Wire.requestFrom(VEML6030_ADDR, 2);
  if (Wire.available() == 2) {
    uint16_t raw = Wire.read() | (Wire.read() << 8);
    // Resolution at gain=1x, 100ms integration = 0.0576 lux/count
    return raw * 0.0576;
  }
  return -1.0;
}

// ─── FLASH DECODER ────────────────────────────────────────
void processFlash(float lux) {
  bool ledOn = (lux > FLASH_ON_THRESHOLD);
  uint32_t now = millis();

  // Rising edge: LED turned on
  if (ledOn && !ledWasOn) {
    flashCount++;
    lastEdgeTime = now;
  }

  // Gap detection: no activity for INTER_BURST_GAP_MS = end of sequence
  if (!ledOn && (now - lastEdgeTime > INTER_BURST_GAP_MS) && flashCount > 0) {
    confirmedFlashes = flashCount;
    flashCount = 0;
    lastEdgeTime = now;
  }

  ledWasOn = ledOn;
}

const char* decodeFlash(uint8_t count) {
  for (auto& code : WV8840_CODES) {
    if (code.count == count) return code.meaning;
  }
  return "Unknown code - check thermostat manual";
}

const char* decodeSeverity(uint8_t count) {
  for (auto& code : WV8840_CODES) {
    if (code.count == count) return code.severity;
  }
  return "unknown";
}

// ─── WIFI + MQTT ──────────────────────────────────────────
void connectWifi() {
  Serial.print("Connecting to WiFi");
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi connected: " + WiFi.localIP().toString());
}

void connectMqtt() {
  mqtt.setServer(MQTT_BROKER, MQTT_PORT);
  while (!mqtt.connected()) {
    Serial.print("Connecting to MQTT...");
    if (mqtt.connect("waterheater_monitor")) {
      Serial.println("connected");
    } else {
      Serial.print("failed, rc=");
      Serial.println(mqtt.state());
      delay(2000);
    }
  }
}

void publishStatus(float tempUpper, float tempLower, uint8_t flashes) {
  char payload[256];
  snprintf(payload, sizeof(payload),
    "{\"temp_upper\":%.1f,\"temp_lower\":%.1f,"
    "\"flash_count\":%d,\"status\":\"%s\",\"severity\":\"%s\"}",
    tempUpper, tempLower,
    flashes,
    decodeFlash(flashes),
    decodeSeverity(flashes)
  );
  mqtt.publish(MQTT_TOPIC, payload);
  Serial.println("Published: " + String(payload));
}

// ─── SETUP ────────────────────────────────────────────────
void setup() {
  Serial.begin(115200);
  delay(1000);

  pinMode(LED_PIN, OUTPUT);

  vemlInit();
  sensors.begin();
  connectWifi();
  connectMqtt();

  Serial.println("Water Heater Monitor online");
  Serial.printf("DS18B20 devices found: %d\n", sensors.getDeviceCount());
}

// ─── LOOP ─────────────────────────────────────────────────
void loop() {
  uint32_t now = millis();

  mqtt.loop();

  // Sample VEML6030 at 20Hz
  if (now - lastSampleTime >= SAMPLE_RATE_MS) {
    lastSampleTime = now;
    float lux = vemlReadLux();
    processFlash(lux);

    // Debug to serial
    Serial.printf("Lux: %.2f | Flashes pending: %d | Confirmed: %d\n",
      lux, flashCount, confirmedFlashes);
  }

  // Report temps and flash status every 30 seconds
  if (now - lastReportTime >= 30000) {
    lastReportTime = now;

    sensors.requestTemperatures();
    float upper = sensors.getTempFByIndex(0);
    float lower = sensors.getTempFByIndex(1);

    if (confirmedFlashes > 0) {
      publishStatus(upper, lower, confirmedFlashes);
    }
  }
}
```

---

## What This Does

**Flash decoder:** Samples the VEML6030 at 20Hz, detects rising edges (LED on transitions), counts them, then uses the 2-second inter-burst gap to recognize end of sequence. Matches the count against the WV8840 lookup table and outputs the human-readable meaning and severity level.

**Temperature:** Reads both DS18B20 probes on the 1-Wire bus every 30 seconds. Index 0 is upper, index 1 is lower (you'll confirm which physical probe maps to which index on first run via serial monitor).

**MQTT payload** is structured JSON so Home Assistant or any broker can parse it directly:

json

```json
{
  "temp_upper": 118.4,
  "temp_lower": 104.2,
  "flash_count": 1,
  "status": "Normal operation - standby",
  "severity": "ok"
}
```

---

## First Things to Do Before Running

1. Set your WiFi credentials and MQTT broker IP.
2. Open serial monitor at 115200 baud and watch the raw lux values with the LED both on and off. Adjust `FLASH_ON_THRESHOLD` and `FLASH_OFF_THRESHOLD` to bracket your actual LED brightness inside the shroud.
3. Verify probe index mapping by holding one probe in your hand (body heat) and watching which index reads higher on serial output.
4. The ADS1115 is not yet wired into this code. That comes in v2 once the optical and temperature paths are validated.