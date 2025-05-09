# Prime Computer Firmware Guide

## Overview

This guide provides comprehensive documentation for the firmware that powers the Prime Resonance Computer system. The firmware is based on the Arduino framework for ESP32 and enables the prime frequency oscillation detection, phase synchronization, and network communication that form the foundation of entropic resonance computing.

## Table of Contents

1. [Development Environment Setup](#development-environment-setup)
2. [Firmware Architecture](#firmware-architecture)
3. [Core Modules](#core-modules)
4. [Installation Instructions](#installation-instructions)
5. [Configuration Options](#configuration-options)
6. [Operation Modes](#operation-modes)
7. [Network Protocol](#network-protocol)
8. [Calibration Procedure](#calibration-procedure)
9. [Troubleshooting](#troubleshooting)
10. [API Reference](#api-reference)

## Development Environment Setup

### Required Software

To develop and flash the Prime Computer firmware, you'll need:

1. **Arduino IDE** (version 2.0 or later)
   - Download from [arduino.cc](https://www.arduino.cc/en/software)
   
2. **ESP32 Board Support**
   - Open Arduino IDE
   - Go to File > Preferences
   - Add this URL to Additional Boards Manager URLs:
     ```
     https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
     ```
   - Go to Tools > Board > Boards Manager
   - Search for "ESP32" and install "ESP32 by Espressif Systems"

3. **Required Libraries**
   - ESP32 (built-in with board package)
   - Adafruit GFX (for display)
   - Adafruit SSD1306 (for OLED display)
   - ArduinoJson (version 6.x or later)
   - FastLED (for RGB LEDs)
   - ESP-NOW (built into ESP32 package)

### Installation Process

1. Install the required libraries through Arduino IDE:
   - Tools > Manage Libraries
   - Search for each required library and install

2. Configure board settings:
   - Board: "ESP32 Dev Module"
   - Upload Speed: 921600
   - CPU Frequency: 240MHz
   - Flash Frequency: 80MHz
   - Flash Mode: QIO
   - Flash Size: 4MB
   - Partition Scheme: "Default 4MB with spiffs"
   - Core Debug Level: None
   - PSRAM: Disabled

### Alternative IDE Options

You can also use:

1. **PlatformIO**
   - Install PlatformIO extension in Visual Studio Code
   - Create a new project with the ESP32 platform
   - Add required libraries to platformio.ini

2. **ESP-IDF**
   - For advanced developers who need lower-level control
   - Requires more setup but provides full ESP32 capabilities

## Firmware Architecture

### System Architecture Overview

The Prime Computer firmware follows a modular architecture designed for flexibility and expandability:

```
+----------------------------------------------------------+
|                   Application Layer                       |
|  +----------------+  +----------------+  +----------------+|
|  | Prime Computer |  | User Interface |  | Configuration  ||
|  | Logic          |  | Controller     |  | Manager        ||
|  +----------------+  +----------------+  +----------------+|
+----------------------------------------------------------+
|                    Middleware Layer                       |
|  +----------------+  +----------------+  +----------------+|
|  | Phase Detection|  | Oscillator     |  | Entropy        ||
|  | Manager        |  | Controller     |  | Source         ||
|  +----------------+  +----------------+  +----------------+|
|                                                           |
|  +----------------+  +----------------+  +----------------+|
|  | Network        |  | Synchronization|  | Data           ||
|  | Manager        |  | Engine         |  | Logger         ||
|  +----------------+  +----------------+  +----------------+|
+----------------------------------------------------------+
|                    Hardware Layer                         |
|  +----------------+  +----------------+  +----------------+|
|  | GPIO           |  | ADC            |  | Display        ||
|  | Controller     |  | Interface      |  | Driver         ||
|  +----------------+  +----------------+  +----------------+|
|                                                           |
|  +----------------+  +----------------+  +----------------+|
|  | WiFi / ESP-NOW |  | Storage        |  | Power          ||
|  | Interface      |  | Manager        |  | Management     ||
|  +----------------+  +----------------+  +----------------+|
+----------------------------------------------------------+
```

### Key Design Patterns

The firmware employs several design patterns:

1. **Observer Pattern**
   - Used for event notification between modules
   - Allows loose coupling between components

2. **Singleton Pattern**
   - Applied to hardware interface managers
   - Ensures single access point to hardware resources

3. **State Machine**
   - Controls the operational modes of the system
   - Manages transitions between different states

4. **Factory Pattern**
   - Creates appropriate handlers for different message types
   - Simplifies protocol implementation

### Memory Management

The ESP32 has limited RAM, so memory management is crucial:

- Static memory allocation preferred over dynamic when possible
- Memory pools used for frequently allocated objects
- Circular buffers for continuous data streams
- PSRAM utilization when available (optional)

## Core Modules

### Oscillator Interface

Interfaces with the hardware oscillators and measures their frequencies and phases:

```cpp
class OscillatorInterface {
private:
  uint8_t pinOsc1;
  uint8_t pinOsc2;
  uint8_t pinOsc3;
  volatile uint32_t pulseCount[3];
  volatile uint32_t lastPulseTime[3];
  float frequency[3];
  
  static void IRAM_ATTR pulseHandler1();
  static void IRAM_ATTR pulseHandler2();
  static void IRAM_ATTR pulseHandler3();
  
public:
  OscillatorInterface(uint8_t pin1, uint8_t pin2, uint8_t pin3);
  void begin();
  float getFrequency(uint8_t oscillator);
  uint32_t getPulseCount(uint8_t oscillator);
  float getPhaseDifference(uint8_t osc1, uint8_t osc2);
  void resetCounters();
};
```

**Key Functions:**
- `begin()`: Initializes oscillator pins and attaches interrupts
- `getFrequency()`: Calculates current oscillator frequency
- `getPhaseDifference()`: Measures phase difference between two oscillators

### Phase Detector

Reads and interprets phase detector outputs from the analog hardware:

```cpp
class PhaseDetector {
private:
  uint8_t adcPin1;
  uint8_t adcPin2;
  uint8_t adcPin3;
  float phaseValues[3];
  MovingAverage<float, 10> filters[3];
  
public:
  PhaseDetector(uint8_t pin1, uint8_t pin2, uint8_t pin3);
  void begin();
  void update();
  float getPhase(uint8_t detector);
  float getNormalizedPhase(uint8_t detector);
  bool isStable(uint8_t detector, float tolerance = 0.05);
  float getStability(uint8_t detector);
};
```

**Key Functions:**
- `update()`: Reads all phase detector values with filtering
- `getPhase()`: Returns raw phase value (0-4095)
- `getNormalizedPhase()`: Returns phase scaled to 0.0-1.0
- `isStable()`: Determines if phase readings are stable

### Entropy Source

Manages the hardware entropy source for randomness:

```cpp
class EntropySource {
private:
  uint8_t entropyPin;
  uint32_t lastSample;
  uint32_t entropyPool;
  uint8_t poolIndex;
  
public:
  EntropySource(uint8_t pin);
  void begin();
  void update();
  uint32_t getRandomValue();
  uint32_t getRandomInRange(uint32_t min, uint32_t max);
  float getRandomFloat();
  void injectEntropy(uint32_t value);
};
```

**Key Functions:**
- `update()`: Samples entropy source and updates pool
- `getRandomValue()`: Returns a random 32-bit value
- `injectEntropy()`: Adds external entropy to the pool

### Network Manager

Handles communication between nodes using ESP-NOW:

```cpp
class NetworkManager {
private:
  uint8_t macAddress[6];
  bool isCoordinator;
  std::vector<PeerInfo> peers;
  NetworkCallback messageCallback;
  
public:
  NetworkManager(bool coordinator = false);
  void begin();
  void addPeer(uint8_t* mac);
  bool sendMessage(uint8_t* target, Message& message);
  bool broadcastMessage(Message& message);
  void onReceive(NetworkCallback callback);
  void update();
  bool isPeerConnected(uint8_t* mac);
  void scanForPeers();
};
```

**Key Functions:**
- `begin()`: Initializes ESP-NOW communication
- `sendMessage()`: Sends a message to a specific peer node
- `broadcastMessage()`: Sends a message to all connected peers
- `scanForPeers()`: Actively searches for other Prime Computer nodes

### Display Controller

Manages the OLED display for user interface:

```cpp
class DisplayController {
private:
  Adafruit_SSD1306* display;
  uint8_t width;
  uint8_t height;
  uint8_t currentScreen;
  
public:
  DisplayController(uint8_t w = 128, uint8_t h = 64);
  void begin();
  void showSplashScreen();
  void showOscillatorStatus(float* frequencies);
  void showPhaseStatus(float* phases);
  void showNetworkStatus(NetworkManager& network);
  void showComputationResult(const char* result);
  void setContrast(uint8_t contrast);
  void nextScreen();
  void update();
};
```

**Key Functions:**
- `showOscillatorStatus()`: Displays oscillator frequencies
- `showPhaseStatus()`: Shows phase detector readings
- `showNetworkStatus()`: Displays network connectivity information
- `update()`: Refreshes display contents

### Prime Resonance Engine

The core computation engine that implements the prime resonance algorithm:

```cpp
class PrimeResonanceEngine {
private:
  float primePhases[MAX_PRIMES];
  bool primeStates[MAX_PRIMES];
  float resonanceThreshold;
  float stabilityFactor;
  OscillatorInterface* oscillators;
  PhaseDetector* phaseDetectors;
  EntropySource* entropy;
  
public:
  PrimeResonanceEngine(OscillatorInterface* osc, PhaseDetector* pd, EntropySource* ent);
  void begin();
  void update();
  void setResonanceThreshold(float threshold);
  void setStabilityFactor(float factor);
  bool isPrimeInResonance(uint8_t prime);
  float getResonanceStrength(uint8_t prime);
  uint32_t computePrime(uint32_t seed);
  void injectEntropy(uint32_t value);
  void synchronize(PrimeResonanceEngine& other);
};
```

**Key Functions:**
- `update()`: Updates internal resonance state based on oscillator readings
- `isPrimeInResonance()`: Determines if a specific prime is in resonance
- `computePrime()`: Uses resonance state to compute a prime result
- `synchronize()`: Synchronizes with another node's resonance engine

### Configuration Manager

Handles persistent settings storage and retrieval:

```cpp
class ConfigManager {
private:
  String configFile;
  StaticJsonDocument<1024> config;
  
public:
  ConfigManager(const char* filename = "/config.json");
  bool begin();
  bool saveConfig();
  bool loadConfig();
  void setDefaults();
  
  // Configuration getters and setters
  String getNodeName();
  void setNodeName(const char* name);
  uint8_t getNodeId();
  void setNodeId(uint8_t id);
  bool isCoordinator();
  void setCoordinator(bool isCoord);
  float getOscillatorTrim(uint8_t oscNum);
  void setOscillatorTrim(uint8_t oscNum, float value);
  // Additional configuration methods...
};
```

**Key Functions:**
- `saveConfig()`: Persists configuration to flash storage
- `loadConfig()`: Loads configuration from flash
- `setDefaults()`: Initializes default configuration values

## Installation Instructions

### Firmware Installation Process

1. **Download the firmware**
   - Clone the repository:
     ```bash
     git clone https://github.com/prime-resonance/prime-computer-firmware.git
     ```
   - Alternatively, download the zip archive from the releases page

2. **Open the project**
   - In Arduino IDE: File > Open > Select main.ino
   - In PlatformIO: Open the project folder

3. **Configure parameters for your node**
   - Edit `config.h` with your node settings:
     ```cpp
     // Node configuration
     #define NODE_ID 1                // Unique node ID (1-254)
     #define IS_COORDINATOR true      // Set to true for coordinator node
     
     // Hardware pin configuration
     #define PIN_OSC1 25              // Oscillator 1 input pin
     #define PIN_OSC2 26              // Oscillator 2 input pin
     #define PIN_OSC3 27              // Oscillator 3 input pin
     #define PIN_PD1 34               // Phase detector 1 input pin
     #define PIN_PD2 35               // Phase detector 2 input pin
     #define PIN_PD3 32               // Phase detector 3 input pin
     #define PIN_ENTROPY 33           // Entropy source input pin
     
     // Network configuration
     #define WIFI_CHANNEL 1           // WiFi channel for ESP-NOW
     ```

4. **Connect your ESP32**
   - Connect ESP32 to your computer via USB
   - Select the correct COM port in IDE

5. **Upload the firmware**
   - Click "Upload" in Arduino IDE
   - Or run `platformio run --target upload` for PlatformIO

6. **Verify installation**
   - Open Serial Monitor at 115200 baud
   - Check for startup messages confirming initialization

### Firmware Update Process

To update existing firmware:

1. Backup your configuration (Settings > Export Configuration)
2. Download the latest firmware
3. Upload as described above
4. Import saved configuration (Settings > Import Configuration)

## Configuration Options

### Basic Configuration

Key configuration settings available:

| Setting | Description | Default | Range |
|---------|-------------|---------|-------|
| Node ID | Unique identifier | 1 | 1-254 |
| Node Name | Descriptive name | "Prime Node" | String |
| Coordinator Mode | Node acts as network coordinator | false | boolean |
| Display Brightness | OLED brightness | 255 | 0-255 |
| Serial Debug | Enable debug over serial | true | boolean |
| Update Interval | Main loop interval (ms) | 10 | 5-1000 |

### Advanced Configuration

Advanced settings for fine-tuning:

| Setting | Description | Default | Range |
|---------|-------------|---------|-------|
| Oscillator Trim 1 | Fine adjustment for OSC1 | 0.0 | -5.0 to 5.0 |
| Oscillator Trim 2 | Fine adjustment for OSC2 | 0.0 | -5.0 to 5.0 |
| Oscillator Trim 3 | Fine adjustment for OSC3 | 0.0 | -5.0 to 5.0 |
| Phase Threshold | Detection threshold | 0.2 | 0.01-0.5 |
| Phase Stability | Required stability time (ms) | 1000 | 100-10000 |
| Network Timeout | Peer timeout (ms) | 5000 | 1000-30000 |
| Entropy Weight | Impact of entropy injection | 0.1 | 0.0-1.0 |

### Configuration UI

Access the configuration UI by:

1. Hold the MODE button while powering on
2. Connect to the AP "PrimeComputer_Config" with password "primeconfig"
3. Browse to http://192.168.4.1
4. Modify settings through the web interface

## Operation Modes

### Boot Mode

When the Prime Computer is powered on:

1. Hardware initialization
   - Verify power rails
   - Initialize oscillators
   - Configure GPIO and ADC

2. Self-test sequence
   - Validate oscillator function
   - Verify phase detector readings
   - Test display functionality
   - Perform memory check

3. Network discovery
   - Identify peer nodes
   - Establish communication
   - Synchronize configuration

### Standard Operation Mode

The default operating mode:

1. Continuous monitoring of oscillators and phase detectors
2. Real-time phase analysis and prime resonance detection
3. Network synchronization with peer nodes
4. Status display on OLED screen

User interface provides:
- Current oscillator frequencies
- Phase detector readings
- Network status
- Computation status

### Calibration Mode

Enter calibration mode by pressing BTN1+BTN2 simultaneously:

1. Oscillator frequency measurement and adjustment
2. Phase detector zero-point calibration
3. Network latency measurement
4. Entropy source validation

Calibration results are stored in non-volatile memory.

### Computation Mode

Initiated by pressing the COMPUTE button:

1. Seed value is input or generated
2. Prime resonance states are initialized
3. Computation cycles through resonance patterns
4. Result is displayed and can be transmitted to peers

### Diagnostic Mode

Accessed through the serial console with command `DIAG`:

1. Detailed hardware diagnostics
2. Raw sensor values display
3. Memory usage statistics
4. Network packet analysis
5. Performance metrics

## Network Protocol

### ESP-NOW Implementation

The system uses ESP-NOW for efficient node communication:

- Low-latency (~1ms) peer-to-peer communication
- No need for router or access point
- Supports up to 20 peers per node
- 250-byte payload per message

### Message Format

Messages follow this binary structure:

```
+----------------+----------------+----------------+----------------+
| Byte 0-1       | Byte 2         | Byte 3         | Byte 4-249     |
| Magic (0xPR)   | Message Type   | Source Node ID | Payload        |
+----------------+----------------+----------------+----------------+
```

### Message Types

| Type ID | Name | Description |
|---------|------|-------------|
| 0x01 | ANNOUNCE | Node announcing presence |
| 0x02 | PING | Network connectivity test |
| 0x03 | PONG | Response to PING |
| 0x04 | SYNC_REQUEST | Request synchronization |
| 0x05 | SYNC_DATA | Synchronization data |
| 0x06 | OSCILLATOR_STATE | Oscillator frequency and phase |
| 0x07 | COMPUTATION_RESULT | Result of computation |
| 0x08 | ENTROPY_INJECTION | Random entropy data |
| 0x0A | CALIBRATION_REQ | Request calibration |
| 0x0B | CALIBRATION_DATA | Calibration information |
| 0x0F | ERROR | Error notification |

### Network Topology

The Prime Computer network uses a hub-and-spoke topology:

- One coordinator node (central hub)
- Multiple satellite nodes (spokes)
- Direct communication between any nodes

For redundancy, nodes automatically select a new coordinator if the primary becomes unavailable.

## Calibration Procedure

### Automated Calibration

For the most accurate operation, perform calibration after assembly:

1. **Power-up calibration**
   - Power up all nodes
   - Wait for oscillator warm-up (5 minutes recommended)

2. **Start calibration**
   - On coordinator node, enter calibration mode
   - Select "Full System Calibration"
   - Follow on-screen prompts

3. **Oscillator calibration**
   - System will measure actual oscillator frequencies
   - Prompt to adjust trimmer potentiometers
   - Target frequencies: 2 Hz, 3 Hz, 5 Hz exactly

4. **Phase detector calibration**
   - System will temporarily set oscillators to same frequency
   - Measures zero-crossing offsets
   - Calculates compensation factors

5. **Network calibration**
   - Measures communication latency
   - Synchronizes clocks between nodes
   - Establishes network topology map

### Manual Calibration

For individual component calibration:

1. **Oscillator frequency adjustment**
   - Connect oscilloscope to oscillator output
   - Adjust trimmer until frequency matches target
   - Verify stable output for 1 minute minimum

2. **Phase detector zeroing**
   - Set both input oscillators to identical frequencies
   - Adjust phase detector potentiometer until output is 1.65V (mid-range)
   - Verify with multimeter at test points

## Troubleshooting

### Common Issues

| Problem | Possible Causes | Solution |
|---------|----------------|----------|
| No oscillator signal | Power issue, Component failure | Check power rails, Verify 555 timer connections |
| Unstable oscillator | Noise interference, Poor connections | Add bypass capacitors, Check solder joints |
| Phase detector not working | Input signal too low, Bad connection | Verify oscillator output, Check wiring |
| ESP32 not booting | Power issue, Flash corruption | Check power supply, Re-flash firmware |
| Network connection fails | Wrong channel, Out of range | Verify WiFi channel setting, Reduce distance |

### Diagnostic LEDs

Each node has status LEDs indicating:

| LED | Color | Pattern | Meaning |
|-----|-------|---------|---------|
| PWR | Green | Solid | Power good |
| PWR | Green | Blinking | Low voltage |
| OSC | Blue | Blinking | Oscillators active |
| OSC | Blue | Solid | Oscillator fault |
| NET | Yellow | Blinking | Network traffic |
| NET | Yellow | Solid | Connected to coordinator |
| ERR | Red | Off | No errors |
| ERR | Red | Blinking | Temporary error |
| ERR | Red | Solid | System fault |

### Serial Debug Output

Enable verbose logging through serial console:

1. Connect to ESP32 via USB at 115200 baud
2. Send command: `debug level 3`
3. Debug information will stream to the console

Debug levels:
- 0: Errors only
- 1: Errors and warnings
- 2: Normal operation info
- 3: Verbose details
- 4: Hardware-level debugging

### Firmware Recovery

If firmware becomes corrupted:

1. Hold BOOT button while connecting power
2. ESP32 will enter bootloader mode
3. Flash firmware using `esptool.py`:
   ```bash
   esptool.py --port COM3 write_flash 0x0 firmware.bin
   ```
4. Release BOOT button and reset device

## API Reference

### Core Functions

Primary firmware functions:

```cpp
// Initialize the system
void setup() {
    hardwareInit();
    displayInit();
    networkInit();
    loadConfiguration();
}

// Main program loop
void loop() {
    updateOscillators();
    updatePhaseDetectors();
    updateNetworkState();
    processUserInput();
    updateDisplay();
    handleComputations();
    delay(UPDATE_INTERVAL);
}

// Hardware initialization
void hardwareInit() {
    // Configure GPIO pins
    pinMode(PIN_OSC1, INPUT);
    pinMode(PIN_OSC2, INPUT);
    pinMode(PIN_OSC3, INPUT);
    
    // Setup interrupt handlers
    attachInterrupt(digitalPinToInterrupt(PIN_OSC1), oscHandler1, RISING);
    attachInterrupt(digitalPinToInterrupt(PIN_OSC2), oscHandler2, RISING);
    attachInterrupt(digitalPinToInterrupt(PIN_OSC3), oscHandler3, RISING);
    
    // Configure ADC
    analogReadResolution(12);
    analogSetAttenuation(ADC_11db);
}

// Phase detection processing
void updatePhaseDetectors() {
    // Read raw ADC values
    int pd1 = analogRead(PIN_PD1);
    int pd2 = analogRead(PIN_PD2);
    int pd3 = analogRead(PIN_PD3);
    
    // Apply filtering and scaling
    float phase1 = phaseFilter1.update((float)pd1 / 4095.0);
    float phase2 = phaseFilter2.update((float)pd2 / 4095.0);
    float phase3 = phaseFilter3.update((float)pd3 / 4095.0);
    
    // Update phase state
    primeEngine.updatePhases(phase1, phase2, phase3);
}
```

### Network Communication API

Functions for node communication:

```cpp
// Initialize ESP-NOW communication
void networkInit() {
    WiFi.mode(WIFI_STA);
    esp_now_init();
    esp_now_register_send_cb(onDataSent);
    esp_now_register_recv_cb(onDataReceived);
    
    // Register peers if coordinator
    if (config.isCoordinator()) {
        registerKnownPeers();
    } else {
        // Register coordinator only
        registerCoordinator();
    }
}

// Send message to specific node
bool sendToNode(uint8_t nodeId, uint8_t messageType, uint8_t* data, size_t len) {
    if (len > MAX_DATA_SIZE) return false;
    
    Message msg;
    msg.magic = MESSAGE_MAGIC;
    msg.type = messageType;
    msg.sourceId = config.getNodeId();
    memcpy(msg.data, data, len);
    
    // Find peer by node ID
    for (auto& peer : peers) {
        if (peer.nodeId == nodeId) {
            return esp_now_send(peer.macAddress, (uint8_t*)&msg, sizeof(Message)) == ESP_OK;
        }
    }
    
    return false; // Peer not found
}

// Broadcast message to all nodes
bool broadcast(uint8_t messageType, uint8_t* data, size_t len) {
    if (len > MAX_DATA_SIZE) return false;
    
    Message msg;
    msg.magic = MESSAGE_MAGIC;
    msg.type = messageType;
    msg.sourceId = config.getNodeId();
    memcpy(msg.data, data, len);
    
    // Send to broadcast address
    uint8_t broadcastMac[] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};
    return esp_now_send(broadcastMac, (uint8_t*)&msg, sizeof(Message)) == ESP_OK;
}
```

### Display API

Functions for managing the OLED display:

```cpp
// Initialize the OLED display
void displayInit() {
    if(!display.begin(SSD1306_SWITCHCAPVCC, DISPLAY_ADDRESS)) {
        Serial.println(F("SSD1306 allocation failed"));
        return;
    }
    display.clearDisplay();
    display.setTextSize(1);
    display.setTextColor(SSD1306_WHITE);
    display.setCursor(0,0);
    display.println(F("Prime Computer"));
    display.println(F("Initializing..."));
    display.display();
}

// Show oscillator status screen
void showOscillatorScreen() {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(0,0);
    display.println(F("Oscillators:"));
    
    display.print(F("OSC1: "));
    display.print(oscillators.getFrequency(0), 2);
    display.println(F(" Hz"));
    
    display.print(F("OSC2: "));
    display.print(oscillators.getFrequency(1), 2);
    display.println(F(" Hz"));
    
    display.print(F("OSC3: "));
    display.print(oscillators.getFrequency(2), 2);
    display.println(F(" Hz"));
    
    display.display();
}

// Show phase detector screen
void showPhaseScreen() {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(0,0);
    display.println(F("Phase Detectors:"));
    
    display.print(F("PD1: "));
    display.print(phaseDetector.getNormalizedPhase(0) * 100);
    display.println(F("%"));
    
    display.print(F("PD2: "));
    display.print(phaseDetector.getNormalizedPhase(1) * 100);
    display.println(F("%"));
    
    display.print(F("PD3: "));
    display.print(phaseDetector.getNormalizedPhase(2) * 100);
    display.println(F("%"));
    
    display.display();
}
```

### Computation API

Functions for prime resonance computation:

```cpp
// Initialize the computation engine
void computationInit() {
    primeEngine.setOscillators(&oscillators);
    primeEngine.setPhaseDetector(&phaseDetector);
    primeEngine.setEntropySource(&entropySource);
    primeEngine.begin();
}

// Start a new computation
void startComputation(uint32_t seed) {
    computationActive = true;
    computationStartTime = millis();
    primeEngine.setSeed(seed);
    primeEngine.resetState();
}

// Update computation state
void updateComputation() {
    if (!computationActive) return;
    
    primeEngine.update();
    
    // Check for completion
    if (primeEngine.isResultReady() || 
        millis() - computationStartTime > MAX_COMPUTATION_TIME) {
        
        uint32_t result = primeEngine.getResult();
        computationActive = false;
        
        // Display and broadcast result
        showResult(result);
        broadcastResult(result);
    }
}

// Get current computation progress
float getComputationProgress() {
    return primeEngine.getProgress();
}
```

### Configuration API

Functions for managing configuration:

```cpp
// Load configuration from flash
bool loadConfiguration() {
    if (!SPIFFS.begin(true)) {
        Serial.println("SPIFFS mount failed");
        return false;
    }
    
    File configFile = SPIFFS.open("/config.json", "r");
    if (!configFile) {
        Serial.println("Failed to open config file");
        return false;
    }
    
    StaticJsonDocument<1024> doc;
    DeserializationError error = deserializeJson(doc, configFile);
    if (error) {
        Serial.println("Failed to parse config file");
        return false;
    }
    
    config.nodeId = doc["nodeId"] | DEFAULT_NODE_ID;
    config.nodeName = doc["nodeName"] | DEFAULT_NODE_NAME;
    config.isCoord = doc["isCoordinator"] | false;
    // Load remaining configuration...
    
    configFile.close();
    return true;
}

// Save configuration to flash
bool saveConfiguration() {
    StaticJsonDocument<1024> doc;
    
    doc["nodeId"] = config.nodeId;
    doc["nodeName"] = config.nodeName;
    doc["isCoordinator"] = config.isCoord;
    // Add remaining configuration...
    
    File configFile = SPIFFS.open("/config.json", "w");
    if (!configFile) {
        Serial.println("Failed to open config file for writing");
        return false;
    }
    
    if (serializeJson(doc, configFile) == 0) {
        Serial.println("Failed to write config file");
        return false;
    }
    
    configFile.close();
    return true;
}
```

### Full API Documentation

Complete API documentation is available in the source code and in separate documentation files:

- [Network API Documentation](network_api.md)
- [Oscillator API Documentation](oscillator_api.md)
- [Phase Detection API Documentation](phase_api.md)
- [Computation API Documentation](computation_api.md)
- [Display API Documentation](display_api.md)
- [Configuration API Documentation](config_api.md)