# ESP32 Prime Resonator Node Firmware

## Overview

This document provides the source code and implementation details for the firmware that runs on the ESP32 microcontroller in each prime resonator node. The firmware handles oscillator control, phase detection, entropy injection, network communications, and visualization.

## System Architecture

The ESP32 firmware is structured in a modular, event-driven architecture optimized for real-time processing of oscillator signals and phase relationships.

```
+----------------------------------------------------------+
|                   Application Layer                       |
|   +-------------+  +-------------+  +---------------+     |
|   | Prime State |  | User        |  | Network       |     |
|   | Controller  |  | Interface   |  | Communication |     |
|   +-------------+  +-------------+  +---------------+     |
|                                                          |
+----------------------------------------------------------+
|                    Service Layer                          |
|   +-------------+  +-------------+  +---------------+     |
|   | Oscillator  |  | Phase       |  | Entropy       |     |
|   | Manager     |  | Detector    |  | Source        |     |
|   +-------------+  +-------------+  +---------------+     |
|                                                          |
|   +-------------+  +-------------+                       |
|   | LED Control |  | Display     |                       |
|   | System      |  | Manager     |                       |
|   +-------------+  +-------------+                       |
|                                                          |
+----------------------------------------------------------+
|                    Hardware Layer                         |
|   +-------------+  +-------------+  +---------------+     |
|   | GPIO        |  | ADC         |  | DAC           |     |
|   | Interface   |  | Interface   |  | Interface     |     |
|   +-------------+  +-------------+  +---------------+     |
|                                                          |
|   +-------------+  +-------------+  +---------------+     |
|   | I2C         |  | WiFi        |  | NeoPixel      |     |
|   | Interface   |  | Interface   |  | Interface     |     |
|   +-------------+  +-------------+  +---------------+     |
|                                                          |
+----------------------------------------------------------+
|                    ESP32 Hardware                         |
+----------------------------------------------------------+
```

## Source Code

### 1. Main Application File

```cpp
/**
 * Prime Resonator Node Firmware
 * For ESP32 microcontroller
 * Version 1.0
 */

#include <Arduino.h>
#include <Wire.h>
#include <WiFi.h>
#include <Adafruit_NeoPixel.h>
#include <SPI.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include "oscillator_control.h"
#include "phase_detection.h"
#include "entropy_source.h"
#include "prime_state.h"
#include "network_comm.h"
#include "display_manager.h"

// Configuration
#define NODE_ID 1               // Unique ID for this node
#define NUM_OSCILLATORS 3       // Number of prime oscillators
#define DISPLAY_WIDTH 128       // OLED display width
#define DISPLAY_HEIGHT 64       // OLED display height
#define NEOPIXEL_PIN 5          // NeoPixel LED strip pin
#define NUM_PIXELS 8            // Number of NeoPixels

// Global objects
OscillatorControl oscillators;
PhaseDetection phaseDetector;
EntropySource entropy;
PrimeState primeState;
NetworkComm network;
DisplayManager display;
Adafruit_NeoPixel pixels(NUM_PIXELS, NEOPIXEL_PIN, NEO_GRB + NEO_KHZ800);
Adafruit_SSD1306 oled(DISPLAY_WIDTH, DISPLAY_HEIGHT, &Wire, -1);

// System state variables
bool systemInitialized = false;
bool networkConnected = false;
unsigned long lastStatusUpdate = 0;
unsigned long lastEntropyInjection = 0;
unsigned long lastNetworkSync = 0;

void setup() {
  Serial.begin(115200);
  Serial.println("Prime Resonator Node Starting...");
  
  // Initialize hardware
  Wire.begin();
  
  // Initialize OLED display
  if(!oled.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("SSD1306 allocation failed");
  }
  oled.clearDisplay();
  oled.setTextSize(1);
  oled.setTextColor(WHITE);
  oled.setCursor(0, 0);
  oled.println("Prime Computer");
  oled.println("Initializing...");
  oled.display();
  
  // Initialize NeoPixels
  pixels.begin();
  pixels.clear();
  pixels.show();
  
  // Initialize system components
  oscillators.init();
  phaseDetector.init();
  entropy.init();
  primeState.init(NODE_ID, NUM_OSCILLATORS);
  network.init();
  display.init(&oled, &pixels);
  
  // Start oscillators with prime frequencies
  oscillators.setFrequency(0, 2);  // 2 Hz (prime)
  oscillators.setFrequency(1, 3);  // 3 Hz (prime)
  oscillators.setFrequency(2, 5);  // 5 Hz (prime)
  oscillators.start();
  
  // System is ready
  systemInitialized = true;
  Serial.println("Initialization complete");
  
  display.showStartupAnimation();
}

void loop() {
  // Read oscillator phases
  oscillators.update();
  
  // Detect phase relationships
  phaseDetector.update();
  
  // Update prime state based on phase relationships
  float phaseMetrics[NUM_OSCILLATORS][NUM_OSCILLATORS];
  phaseDetector.getPhaseRelationships(phaseMetrics);
  primeState.updateFromPhaseData(phaseMetrics);
  
  // Periodic entropy injection
  unsigned long currentTime = millis();
  if (currentTime - lastEntropyInjection > 10000) {  // Every 10 seconds
    float entropyValue = entropy.generateEntropy();
    oscillators.injectEntropy(entropyValue);
    lastEntropyInjection = currentTime;
    Serial.printf("Entropy injected: %.3f\n", entropyValue);
  }
  
  // Periodic status update
  if (currentTime - lastStatusUpdate > 500) {  // Every 0.5 seconds
    updateDisplay();
    lastStatusUpdate = currentTime;
  }
  
  // Network communication
  if (currentTime - lastNetworkSync > 1000) {  // Every second
    if (network.isConnected()) {
      network.shareState(primeState.getStateVector());
      
      // Process any incoming states from other nodes
      if (network.hasNewData()) {
        int sourceNode;
        float* remoteState = network.receiveState(&sourceNode);
        primeState.integrateRemoteState(sourceNode, remoteState);
      }
    }
    lastNetworkSync = currentTime;
  }
  
  // Small delay to prevent excessive CPU usage
  delay(10);
}

void updateDisplay() {
  // Update display with current state
  display.clear();
  
  // Display node information
  display.printNodeInfo(NODE_ID, oscillators.getStatus(), network.isConnected());
  
  // Display prime resonance pattern
  float* stateVector = primeState.getStateVector();
  display.showResonancePattern(stateVector, primeState.getStateVectorSize());
  
  // Update LEDs to reflect phase relationships
  float coherence = primeState.getSystemCoherence();
  display.updateLEDs(coherence, stateVector);
  
  // Render to display
  display.render();
}
```

### 2. Oscillator Control Module

```cpp
/**
 * oscillator_control.h
 * Manages the prime-tuned oscillators
 */

#ifndef OSCILLATOR_CONTROL_H
#define OSCILLATOR_CONTROL_H

#include <Arduino.h>

class OscillatorControl {
public:
  OscillatorControl();
  void init();
  void setFrequency(int oscIndex, float frequency);
  void start();
  void stop();
  void update();
  bool getStatus();
  void injectEntropy(float entropyValue);
  float getPhase(int oscIndex);
  
private:
  static const int MAX_OSCILLATORS = 3;
  int oscPins[MAX_OSCILLATORS] = {12, 13, 14};  // GPIO pins for oscillator control
  float frequencies[MAX_OSCILLATORS];
  float phases[MAX_OSCILLATORS];
  unsigned long lastUpdateTime;
  bool running;
  
  // For software-controlled oscillators
  void updateSoftwareOscillators();
};

// Implementation in oscillator_control.cpp
#endif
```

```cpp
/**
 * oscillator_control.cpp
 * Implementation of oscillator control functions
 */

#include "oscillator_control.h"

OscillatorControl::OscillatorControl() {
  running = false;
  lastUpdateTime = 0;
  
  // Initialize frequencies
  for (int i = 0; i < MAX_OSCILLATORS; i++) {
    frequencies[i] = 0;
    phases[i] = 0;
  }
}

void OscillatorControl::init() {
  // Configure oscillator control pins
  for (int i = 0; i < MAX_OSCILLATORS; i++) {
    pinMode(oscPins[i], OUTPUT);
    digitalWrite(oscPins[i], LOW);
  }
  
  lastUpdateTime = millis();
}

void OscillatorControl::setFrequency(int oscIndex, float frequency) {
  if (oscIndex >= 0 && oscIndex < MAX_OSCILLATORS) {
    frequencies[oscIndex] = frequency;
  }
}

void OscillatorControl::start() {
  running = true;
}

void OscillatorControl::stop() {
  running = false;
  
  // Reset oscillator outputs
  for (int i = 0; i < MAX_OSCILLATORS; i++) {
    digitalWrite(oscPins[i], LOW);
    phases[i] = 0;
  }
}

void OscillatorControl::update() {
  if (!running) return;
  
  unsigned long currentTime = millis();
  float deltaTime = (currentTime - lastUpdateTime) / 1000.0;  // Convert to seconds
  lastUpdateTime = currentTime;
  
  // Update software oscillators
  updateSoftwareOscillators();
  
  // Read hardware oscillators if physical oscillators are used
  // This would use ADC to read oscillator outputs
  
  // For a hardware implementation, we would use real oscillators and read their
  // states using the ADC. For software simulation:
  // readHardwareOscillators();
}

void OscillatorControl::updateSoftwareOscillators() {
  // Update phase of each oscillator
  for (int i = 0; i < MAX_OSCILLATORS; i++) {
    if (frequencies[i] > 0) {
      // Calculate phase increment based on frequency
      unsigned long currentTime = millis();
      // Phase increases by 2π every 1/frequency seconds
      phases[i] = fmod(phases[i] + (2 * PI * frequencies[i] / 1000.0), 2 * PI);
      
      // Generate square wave output on GPIO pin
      digitalWrite(oscPins[i], (phases[i] < PI) ? HIGH : LOW);
    }
  }
}

bool OscillatorControl::getStatus() {
  return running;
}

void OscillatorControl::injectEntropy(float entropyValue) {
  // Introduce phase perturbations based on entropy
  for (int i = 0; i < MAX_OSCILLATORS; i++) {
    // Add random phase offset proportional to entropy
    phases[i] = fmod(phases[i] + entropyValue * random(100) / 100.0, 2 * PI);
  }
}

float OscillatorControl::getPhase(int oscIndex) {
  if (oscIndex >= 0 && oscIndex < MAX_OSCILLATORS) {
    return phases[oscIndex];
  }
  return 0;
}
```

### 3. Phase Detection Module

```cpp
/**
 * phase_detection.h
 * Detects phase relationships between oscillators
 */

#ifndef PHASE_DETECTION_H
#define PHASE_DETECTION_H

#include <Arduino.h>

class PhaseDetection {
public:
  PhaseDetection();
  void init();
  void update();
  void getPhaseRelationships(float phaseMatrix[][3]);
  float getCoherence();
  
private:
  static const int MAX_OSCILLATORS = 3;
  int phasePins[3] = {32, 33, 34};  // ADC pins for phase detector outputs
  float phaseRelationships[MAX_OSCILLATORS][MAX_OSCILLATORS];
  float systemCoherence;
  
  void updatePhaseMatrix();
  float calculateCoherence();
};

#endif
```

### 4. Prime State Module

```cpp
/**
 * prime_state.h
 * Manages the prime resonance state of the node
 */

#ifndef PRIME_STATE_H
#define PRIME_STATE_H

#include <Arduino.h>

class PrimeState {
public:
  PrimeState();
  void init(int nodeId, int numOscillators);
  void updateFromPhaseData(float phaseMatrix[][3]);
  float* getStateVector();
  int getStateVectorSize();
  float getSystemCoherence();
  void integrateRemoteState(int remoteNodeId, float* remoteState);
  
private:
  int nodeId;
  int numOscillators;
  static const int MAX_STATE_SIZE = 10;
  float stateVector[MAX_STATE_SIZE];
  int stateVectorSize;
  float systemCoherence;
  
  void calculateSymbolicState();
};

#endif
```

### 5. Network Communication Module

```cpp
/**
 * network_comm.h
 * Handles network communication between nodes
 */

#ifndef NETWORK_COMM_H
#define NETWORK_COMM_H

#include <Arduino.h>
#include <WiFi.h>
#include <ESPNow.h>

class NetworkComm {
public:
  NetworkComm();
  void init();
  bool isConnected();
  void shareState(float* stateVector);
  bool hasNewData();
  float* receiveState(int* sourceNodeId);
  
private:
  bool connected;
  static const int MAX_NODES = 10;
  static const int MAX_STATE_SIZE = 10;
  float receivedStates[MAX_NODES][MAX_STATE_SIZE];
  bool newDataFlag[MAX_NODES];
  
  static void onDataReceived(const uint8_t* mac, const uint8_t* data, int len);
  void setupESPNow();
  void setupWiFiMesh();
};

#endif
```

### 6. Display Manager Module

```cpp
/**
 * display_manager.h
 * Manages visual output via OLED and LED indicators
 */

#ifndef DISPLAY_MANAGER_H
#define DISPLAY_MANAGER_H

#include <Arduino.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Adafruit_NeoPixel.h>

class DisplayManager {
public:
  DisplayManager();
  void init(Adafruit_SSD1306* oled, Adafruit_NeoPixel* pixels);
  void clear();
  void printNodeInfo(int nodeId, bool running, bool connected);
  void showResonancePattern(float* stateVector, int size);
  void updateLEDs(float coherence, float* stateVector);
  void showStartupAnimation();
  void render();
  
private:
  Adafruit_SSD1306* oled;
  Adafruit_NeoPixel* pixels;
  
  void drawPrimeWave(float frequency, int x, int y, int width, int height);
};

#endif
```

### 7. Entropy Source Module

```cpp
/**
 * entropy_source.h
 * Generates entropy (randomness) for state transitions
 */

#ifndef ENTROPY_SOURCE_H
#define ENTROPY_SOURCE_H

#include <Arduino.h>

class EntropySource {
public:
  EntropySource();
  void init();
  float generateEntropy();
  float getLastEntropy();
  void setEntropyStrength(float strength);
  
private:
  int entropyPin = 35;  // ADC pin connected to noise source
  float lastEntropy;
  float entropyStrength;
  
  float readAnalogNoise();
  float processNoiseToEntropy(float raw);
};

#endif
```

## Compilation and Flashing Instructions

### Required Libraries
The firmware requires the following libraries:
- Arduino ESP32 Core
- Adafruit NeoPixel
- Adafruit GFX
- Adafruit SSD1306
- ESP-NOW (included in ESP32 Arduino)

### Development Environment Setup

1. **Install Arduino IDE**:
   - Download from [arduino.cc](https://www.arduino.cc/en/software)

2. **Install ESP32 Board Support**:
   - In Arduino IDE, go to File > Preferences
   - Add `https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json` to Additional Board Manager URLs
   - Go to Tools > Board > Boards Manager
   - Search for "esp32" and install "ESP32 by Espressif Systems"

3. **Install Required Libraries**:
   - Go to Tools > Manage Libraries
   - Install the libraries mentioned above

4. **Configure Board Settings**:
   - Board: "ESP32 Dev Module"
   - Upload Speed: 921600
   - Flash Frequency: 80MHz
   - Flash Mode: QIO
   - Flash Size: 4MB
   - Partition Scheme: Default 4MB with spiffs

### Compilation Process

1. Create a new Arduino project
2. Create files for each module shown above
3. Copy the code into the respective files
4. Connect your ESP32 to your computer via USB
5. Select the correct port in Tools > Port
6. Click Upload button to compile and flash

### Troubleshooting Common Issues

1. **Upload Failed**: 
   - Press and hold BOOT button while uploading
   - Check USB connection
   - Verify correct board and port are selected

2. **Compilation Errors**:
   - Ensure all libraries are installed
   - Check for typos in include statements
   - Verify that all files are in the same directory

3. **Hardware Not Responding**:
   - Check connections between ESP32 and peripherals
   - Verify power supply is adequate (500mA minimum)
   - Check serial monitor for debug messages

## Custom Programming Guide

### Adding New Features

1. **Additional Sensors**:
   - Create a new module class for the sensor
   - Add initialization in the setup() function
   - Add reading code in the loop() function
   - Integrate readings into the prime state

2. **Advanced Visualization**:
   - Extend the DisplayManager class with new methods
   - Create custom graphics for prime state visualization

3. **Enhanced Network Capabilities**:
   - Add methods to the NetworkComm class
   - Implement more sophisticated state sharing protocols

### Tuning Parameters

The firmware includes several tunable parameters:

1. **Oscillator Frequencies**:
   ```cpp
   oscillators.setFrequency(0, 2);  // Change prime frequencies
   ```

2. **Entropy Injection Rate**:
   ```cpp
   // In loop()
   if (currentTime - lastEntropyInjection > 10000) {  // Change from 10000ms
   ```

3. **Network Sync Rate**:
   ```cpp
   // In loop()
   if (currentTime - lastNetworkSync > 1000) {  // Change from 1000ms
   ```

## Advanced Configuration

### WiFi Network Setup

To connect the nodes to a WiFi network:

```cpp
// In network_comm.cpp
void NetworkComm::init() {
  WiFi.begin("YourSSID", "YourPassword");
  int attempts = 0;
  while (WiFi.status() != WL_CONNECTED && attempts < 20) {
    delay(500);
    Serial.print(".");
    attempts++;
  }
  
  if (WiFi.status() == WL_CONNECTED) {
    Serial.println("WiFi connected");
    connected = true;
  } else {
    Serial.println("WiFi connection failed");
    connected = false;
  }
  
  // then setup ESP-NOW
  setupESPNow();
}
```

### Multi-Node Network Configuration

For a network of prime computers, each node needs a unique ID and peer configuration:

```cpp
// In setup()
// Set this node's MAC address
uint8_t customMACAddress[] = {0x36, 0x33, 0x33, 0x33, 0x33, NODE_ID};
esp_wifi_set_mac(ESP_IF_WIFI_STA, customMACAddress);

// Add peer nodes
esp_now_peer_info_t peerInfo;
peerInfo.channel = 0;
peerInfo.encrypt = false;

// Add peer 1 (if this is not node 1)
if (NODE_ID != 1) {
  uint8_t peer1MACAddress[] = {0x36, 0x33, 0x33, 0x33, 0x33, 0x01};
  memcpy(peerInfo.peer_addr, peer1MACAddress, 6);
  esp_now_add_peer(&peerInfo);
}

// Add more peers as needed
```

## Testing and Validation

### Self-Test Routine

Add the following to enable a self-test routine:

```cpp
void runSelfTest() {
  Serial.println("Starting self-test...");
  
  // Test oscillators
  oscillators.start();
  delay(1000);
  oscillators.stop();
  
  // Test display
  display.clear();
  display.oled->println("Display Test");
  display.render();
  delay(1000);
  
  // Test LEDs
  for (int i = 0; i < 8; i++) {
    pixels.setPixelColor(i, pixels.Color(255, 0, 0));
    pixels.show();
    delay(100);
    pixels.setPixelColor(i, pixels.Color(0, 0, 0));
    pixels.show();
  }
  
  // Test entropy source
  float e = entropy.generateEntropy();
  Serial.printf("Entropy test: %.3f\n", e);
  
  Serial.println("Self-test complete");
}
```

Add `runSelfTest();` to the setup() function to run on startup.

### Debug Output

For detailed debugging, add:

```cpp
#define DEBUG true

// Then throughout the code:
#if DEBUG
  Serial.printf("Debug: %s\n", message);
#endif
```

## Power Management

For battery-powered nodes, add power management:

```cpp
void setupPowerManagement() {
  // Set CPU frequency
  setCpuFrequencyMhz(80);  // Reduce from default 240MHz
  
  // Enable light sleep between readings
  esp_sleep_enable_timer_wakeup(10000);  // Wake every 10ms
}

// In loop(), add:
void performPowerSaving() {
  // If on battery and no activity needed
  if (batteryPowered && !activeProcessingRequired) {
    esp_light_sleep_start();
  }
}