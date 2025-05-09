# Firmware Reference

## Introduction

This section provides a comprehensive reference for the Prime Resonance Computer firmware, including its architecture, source code organization, API documentation, customization options, and advanced programming techniques. This information is essential for those who wish to modify, extend, or deeply understand the software that powers the system.

## Firmware Architecture

The Prime Resonance Computer firmware follows a modular, layered architecture designed for maintainability, extensibility, and clear separation of concerns.

### Architecture Overview

![Firmware Architecture Diagram](diagrams/firmware_architecture.png)

The firmware is structured in layers, from hardware abstraction at the bottom to application logic at the top:

1. **Hardware Abstraction Layer (HAL)**
   - Direct interface with ESP32 hardware peripherals
   - Platform-specific implementations
   - Abstraction for compatibility across different ESP32 variants

2. **Driver Layer**
   - Component-specific drivers (oscillators, phase detectors, OLED)
   - Hardware-independent interfaces
   - Data acquisition and signal processing

3. **System Services Layer**
   - Core functionality shared across the application
   - Network communication
   - Configuration management
   - Logging and diagnostics

4. **Application Layer**
   - Core computation engine
   - User interface controller
   - Task management
   - Application-specific logic

5. **API Layer**
   - External interfaces (Serial, Web)
   - Command processor
   - Data export services

### Component Interactions

Key interactions between components:

1. **Oscillator Management**
   - HAL provides timer and GPIO interfaces
   - Driver layer reads and controls oscillators
   - System services synchronize oscillator data
   - Application layer uses oscillator data for computation

2. **Phase Detection**
   - HAL provides ADC interfaces
   - Driver layer samples and processes phase detector signals
   - System services analyze phase relationships
   - Application layer uses phase data for computation

3. **User Interface Flow**
   - HAL interfaces with display hardware and buttons
   - Driver layer translates UI commands to hardware operations
   - System services manage UI state
   - Application layer determines UI content and navigation

## Source Code Organization

The firmware source code is organized in a logical directory structure reflecting the architecture:

```
/firmware
├── /include                 # Public header files
│   ├── configuration.h      # System configuration parameters
│   ├── constants.h          # Global constants
│   ├── errors.h             # Error codes and handling
│   └── types.h              # Common data types
│
├── /src                     # Source code
│   ├── /hal                 # Hardware Abstraction Layer
│   │   ├── adc.cpp          # ADC interface
│   │   ├── gpio.cpp         # GPIO management
│   │   ├── i2c.cpp          # I2C communication
│   │   ├── nvs.cpp          # Non-volatile storage
│   │   ├── timers.cpp       # Hardware timers
│   │   └── wifi.cpp         # WiFi and ESP-NOW
│   │
│   ├── /drivers             # Hardware drivers
│   │   ├── display.cpp      # OLED display driver
│   │   ├── entropy.cpp      # Entropy source management
│   │   ├── oscillator.cpp   # Oscillator interface
│   │   └── phase.cpp        # Phase detector
│   │
│   ├── /services            # System services
│   │   ├── config.cpp       # Configuration management
│   │   ├── logger.cpp       # Logging system
│   │   ├── network.cpp      # Network management
│   │   └── sync.cpp         # Synchronization engine
│   │
│   ├── /application         # Application layer
│   │   ├── compute.cpp      # Computing engine
│   │   ├── prime.cpp        # Prime number algorithms
│   │   ├── resonance.cpp    # Resonance analysis
│   │   ├── system.cpp       # System management
│   │   └── ui.cpp           # User interface logic
│   │
│   ├── /api                 # External APIs
│   │   ├── commands.cpp     # Command processor
│   │   ├── serial.cpp       # Serial interface
│   │   ├── web.cpp          # Web server
│   │   └── script.cpp       # Scripting engine
│   │
│   └── main.cpp             # Main application entry
│
├── /lib                     # External libraries
│   ├── /Adafruit_GFX        # Graphics library
│   ├── /Adafruit_SSD1306    # OLED driver
│   ├── /ArduinoJson         # JSON processing
│   └── /WiFi                # ESP32 WiFi library
│
└── /tools                   # Development tools
    ├── calibration.cpp      # Calibration utility
    ├── diagnostics.cpp      # Diagnostic tools
    └── test.cpp             # Testing framework
```

## Key Classes and Functions

This section outlines the most important classes and functions in each major component of the firmware.

### Oscillator Management

**OscillatorManager Class**

```cpp
class OscillatorManager {
public:
    // Constructor and initialization
    OscillatorManager();
    bool begin(uint8_t osc1Pin, uint8_t osc2Pin, uint8_t osc3Pin);
    
    // Core functionality
    void update();                        // Update all oscillator readings
    float getFrequency(uint8_t oscIndex); // Get current frequency (0-2)
    float getPhase(uint8_t oscIndex);     // Get current phase (0-359.99)
    bool isStable(uint8_t oscIndex);      // Check stability
    
    // Configuration
    void setTargetFrequency(uint8_t oscIndex, float freq);
    float getTrimAdjustment(uint8_t oscIndex);
    void setTrimAdjustment(uint8_t oscIndex, float trim);
    
    // Advanced features
    void injectResonancePattern(uint32_t value);
    float getResonanceQuality();
    
private:
    // Internal implementation details
    void measureFrequency(uint8_t oscIndex);
    void calculatePhase(uint8_t oscIndex);
    void updateStability();
    
    // Internal state
    Oscillator oscillators[3];
    float targetFrequencies[3];
    float trimValues[3];
    bool stable[3];
};
```

**Usage Example**

```cpp
// Initialize oscillator manager
OscillatorManager oscillators;
oscillators.begin(PIN_OSC1, PIN_OSC2, PIN_OSC3);

// Configure target frequencies (prime numbers)
oscillators.setTargetFrequency(0, 2.0); // 2 Hz
oscillators.setTargetFrequency(1, 3.0); // 3 Hz
oscillators.setTargetFrequency(2, 5.0); // 5 Hz

// Main loop
void loop() {
    // Update current readings
    oscillators.update();
    
    // Display current frequencies
    for (int i = 0; i < 3; i++) {
        Serial.print("Oscillator ");
        Serial.print(i + 1);
        Serial.print(": ");
        Serial.print(oscillators.getFrequency(i), 3);
        Serial.println(" Hz");
    }
    
    delay(1000);
}
```

### Phase Detector System

**PhaseDetectorSystem Class**

```cpp
class PhaseDetectorSystem {
public:
    // Constructor and initialization  
    PhaseDetectorSystem();
    bool begin(uint8_t pd1Pin, uint8_t pd2Pin, uint8_t pd3Pin);
    
    // Core functionality
    void update();                      // Update all phase readings
    float getPhase(uint8_t pdIndex);    // Get phase difference (0-359.99)
    float getResonanceValue(uint8_t pdIndex); // Get resonance measurement
    
    // Configuration
    void setSamplingRate(uint16_t samplesPerSecond);
    void setFilterLevel(uint8_t level); // 0=none, 1=low, 2=med, 3=high
    void calibrate();                   // Run auto-calibration
    
    // Advanced analysis
    bool detectResonancePattern(ResonancePattern& pattern);
    float calculateResonanceStrength();
    
private:
    // Implementation details
    void sampleDetectors();
    void processPhaseData();
    float calculatePhaseFromADC(uint16_t adcValue);
    
    // Internal state
    uint16_t rawValues[3];
    float phaseValues[3];
    float resonanceValues[3];
    uint16_t samplingRate;
    uint8_t filterLevel;
};
```

**Usage Example**

```cpp
// Initialize phase detector system
PhaseDetectorSystem phaseDetectors;
phaseDetectors.begin(PIN_PD1, PIN_PD2, PIN_PD3);
phaseDetectors.setSamplingRate(100);  // 100 samples per second
phaseDetectors.setFilterLevel(2);     // Medium filtering

// Main loop
void loop() {
    // Update all phase detector readings
    phaseDetectors.update();
    
    // Display phase relationships
    Serial.print("Phase 1-2: ");
    Serial.print(phaseDetectors.getPhase(0));
    Serial.println(" degrees");
    
    Serial.print("Phase 1-3: ");
    Serial.print(phaseDetectors.getPhase(1));
    Serial.println(" degrees");
    
    Serial.print("Phase 2-3: ");
    Serial.print(phaseDetectors.getPhase(2));
    Serial.println(" degrees");
    
    // Check resonance strength
    float resonance = phaseDetectors.calculateResonanceStrength();
    Serial.print("Resonance strength: ");
    Serial.println(resonance, 4);
    
    delay(1000);
}
```

### Compute Engine

**ComputeEngine Class**

```cpp
class ComputeEngine {
public:
    // Constructor and initialization
    ComputeEngine(OscillatorManager& oscMgr, PhaseDetectorSystem& phaseSys);
    bool begin();
    
    // Core functionality
    bool isPrime(uint32_t number);
    bool factorize(uint32_t number, std::vector<uint32_t>& factors);
    bool findPrimes(uint32_t start, uint32_t end, std::vector<uint32_t>& primes);
    float recognizePattern(const std::vector<float>& pattern);
    
    // Computation control
    void startComputation(ComputeTask task);
    void stopComputation();
    bool isRunning();
    float getProgress();
    ComputeResult getResult();
    
    // Configuration
    void setResonanceThreshold(float threshold);
    void setComputeIntensity(uint8_t level);
    void setEntropyWeight(float weight);
    
private:
    // Implementation details
    void primeTestTask(void* parameters);
    void factorizationTask(void* parameters);
    void patternRecognitionTask(void* parameters);
    
    // Algorithmic methods
    float calculatePrimeResonance(uint32_t number);
    void injectNumberPattern(uint32_t number);
    bool detectFactorResonance(uint32_t number, uint32_t& factor);
    
    // Internal state
    OscillatorManager& oscillators;
    PhaseDetectorSystem& phaseDetectors;
    ComputeTask currentTask;
    ComputeResult lastResult;
    bool computeRunning;
    float progress;
};
```

**Usage Example**

```cpp
// Initialize compute engine with oscillator and phase detector references
ComputeEngine computeEngine(oscillators, phaseDetectors);
computeEngine.begin();

// Configure compute parameters
computeEngine.setResonanceThreshold(0.85);
computeEngine.setEntropyWeight(0.2);

// Prime test example
void testPrime() {
    uint32_t numberToTest = 997;
    
    if (computeEngine.isPrime(numberToTest)) {
        Serial.println("Number is prime");
    } else {
        Serial.println("Number is composite");
    }
    
    // Get detailed information
    ComputeResult result = computeEngine.getResult();
    Serial.print("Confidence: ");
    Serial.print(result.confidence * 100);
    Serial.println("%");
    Serial.print("Computation time: ");
    Serial.print(result.computeTime);
    Serial.println(" ms");
}
```

### Network Manager

**NetworkManager Class**

```cpp
class NetworkManager {
public:
    // Constructor and initialization
    NetworkManager();
    bool begin();
    
    // Network configuration
    void setNodeId(uint8_t id);
    bool setCoordinator(bool isCoordinator);
    bool addPeer(const uint8_t* macAddress);
    bool removePeer(const uint8_t* macAddress);
    
    // Communication
    bool sendMessage(uint8_t targetNodeId, const NetworkMessage& message);
    bool broadcastMessage(const NetworkMessage& message);
    bool receiveMessage(NetworkMessage& message, uint32_t timeout = 0);
    
    // Synchronization
    bool syncNetwork();
    bool waitForSync(uint32_t timeout);
    
    // Network status
    uint8_t getPeerCount();
    bool isPeerConnected(uint8_t nodeId);
    int8_t getSignalStrength(uint8_t nodeId);
    float getRoundTripTime(uint8_t nodeId);
    
private:
    // Implementation details
    static void receiveCallback(const uint8_t* mac, const uint8_t* data, int dataLen);
    void processIncomingMessage(const uint8_t* mac, const uint8_t* data, int dataLen);
    bool sendRawMessage(const uint8_t* mac, const uint8_t* data, size_t dataLen);
    
    // Internal state
    uint8_t nodeId;
    bool isCoordinator;
    std::vector<PeerInfo> peers;
    CircularBuffer<NetworkMessage, 32> messageQueue;
};
```

**Usage Example**

```cpp
// Initialize network manager
NetworkManager network;
network.setNodeId(1);  // This is node 1
network.setCoordinator(true);  // This is the coordinator node
network.begin();

// Add peer nodes
uint8_t node2Mac[] = {0x24, 0x6F, 0x28, 0xB1, 0xC3, 0x54};
uint8_t node3Mac[] = {0x24, 0x6F, 0x28, 0xB2, 0xD7, 0x18};
network.addPeer(node2Mac);
network.addPeer(node3Mac);

// Send a message to node 2
NetworkMessage msg;
msg.type = MSG_COMMAND;
msg.command = CMD_SYNC_OSCILLATORS;
msg.dataLength = 0;
network.sendMessage(2, msg);

// Process incoming messages
void processMessages() {
    NetworkMessage incomingMsg;
    while (network.receiveMessage(incomingMsg)) {
        // Handle message based on type
        switch (incomingMsg.type) {
            case MSG_STATUS:
                handleStatusMessage(incomingMsg);
                break;
            case MSG_DATA:
                handleDataMessage(incomingMsg);
                break;
            // Handle other message types
        }
    }
}
```

### User Interface Controller

**UserInterfaceController Class**

```cpp
class UserInterfaceController {
public:
    // Constructor and initialization
    UserInterfaceController();
    bool begin(Adafruit_SSD1306& display);
    
    // Screen management
    void showHomeScreen();
    void showOscillatorScreen();
    void showPhaseScreen();
    void showNetworkScreen();
    void showComputeScreen();
    void showMenuScreen(const std::vector<String>& menuItems);
    
    // User input handling
    void handleButtonPress(uint8_t buttonId);
    void navigateNext();
    void navigatePrevious();
    void select();
    void back();
    
    // Dynamic content
    void updateStatus(const SystemStatus& status);
    void updateComputeProgress(float progress, const String& statusText);
    void showResult(const ComputeResult& result);
    void showAlert(const String& message, uint16_t durationMs = 2000);
    
private:
    // Implementation details
    void renderScreen();
    void drawProgressBar(uint16_t x, uint16_t y, uint16_t width, 
                         uint16_t height, uint8_t progress);
    void drawBattery(uint16_t x, uint16_t y, uint8_t percentage);
    
    // Internal state
    Adafruit_SSD1306 display;
    uint8_t currentScreen;
    uint8_t menuSelection;
    std::vector<String> menuItems;
    SystemStatus systemStatus;
    String alertMessage;
    uint32_t alertExpireTime;
};
```

**Usage Example**

```cpp
// Initialize display
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire);
display.begin(SSD1306_SWITCHCAPVCC, 0x3C);

// Initialize UI controller
UserInterfaceController ui;
ui.begin(display);

// Update system status
void updateUI() {
    // Create status object
    SystemStatus status;
    status.batteryLevel = 75;
    status.isComputing = computeEngine.isRunning();
    status.networkConnected = network.getPeerCount() > 0;
    
    // Update oscillator information
    for (int i = 0; i < 3; i++) {
        status.oscillatorFreq[i] = oscillators.getFrequency(i);
        status.oscillatorStable[i] = oscillators.isStable(i);
    }
    
    // Update phase information
    for (int i = 0; i < 3; i++) {
        status.phaseValues[i] = phaseDetectors.getPhase(i);
    }
    
    // Update UI with current status
    ui.updateStatus(status);
    
    // Show appropriate screen
    if (computeEngine.isRunning()) {
        ui.showComputeScreen();
        ui.updateComputeProgress(computeEngine.getProgress(), "Computing...");
    }
}
```

### Configuration Manager

**ConfigManager Class**

```cpp
class ConfigManager {
public:
    // Constructor and initialization
    ConfigManager();
    bool begin();
    
    // Basic configuration operations
    bool load();
    bool save();
    bool reset();
    
    // Configuration getters
    String getNodeName();
    float getOscillatorTrim(uint8_t oscIndex);
    float getPhaseThreshold();
    uint8_t getDisplayBrightness();
    uint8_t getWifiChannel();
    
    // Configuration setters
    void setNodeName(const String& name);
    void setOscillatorTrim(uint8_t oscIndex, float value);
    void setPhaseThreshold(float threshold);
    void setDisplayBrightness(uint8_t brightness);
    void setWifiChannel(uint8_t channel);
    
    // Export/import
    bool exportToJson(String& jsonOutput);
    bool importFromJson(const String& jsonInput);
    
private:
    // Implementation details
    bool writeToNVS();
    bool readFromNVS();
    
    // Configuration data structure
    struct Config {
        char nodeName[32];
        float oscillatorTrim[3];
        float phaseThreshold;
        uint8_t displayBrightness;
        uint8_t wifiChannel;
        // Many other configuration parameters
    };
    
    Config config;
};
```

**Usage Example**

```cpp
// Initialize configuration manager
ConfigManager configManager;
configManager.begin();

// Load saved configuration
if (!configManager.load()) {
    Serial.println("No saved configuration found, using defaults");
}

// Apply configuration to components
oscillators.setTrimAdjustment(0, configManager.getOscillatorTrim(0));
oscillators.setTrimAdjustment(1, configManager.getOscillatorTrim(1));
oscillators.setTrimAdjustment(2, configManager.getOscillatorTrim(2));

phaseDetectors.setThreshold(configManager.getPhaseThreshold());

display.setBrightness(configManager.getDisplayBrightness());

// Save updated configuration
void saveNewConfiguration() {
    // Update configuration with current values
    configManager.setOscillatorTrim(0, oscillators.getTrimAdjustment(0));
    configManager.setOscillatorTrim(1, oscillators.getTrimAdjustment(1));
    configManager.setOscillatorTrim(2, oscillators.getTrimAdjustment(2));
    
    // Save to non-volatile storage
    if (configManager.save()) {
        Serial.println("Configuration saved successfully");
    } else {
        Serial.println("Error saving configuration");
    }
}
```

## Command Interface Reference

The Prime Resonance Computer supports a comprehensive command interface via the serial console. These commands can be used for configuration, diagnostics, and manual control.

### System Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `help` | None | Display available commands |
| `status` | None | Show system status |
| `reboot` | None | Restart the system |
| `version` | None | Display firmware version |
| `save` | None | Save current configuration |
| `reset` | None | Reset to default configuration |

### Configuration Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `set nodename` | `<name>` | Set node name |
| `set oscillator1_trim` | `<value>` | Set oscillator 1 trim (-5.0 to 5.0) |
| `set oscillator2_trim` | `<value>` | Set oscillator 2 trim (-5.0 to 5.0) |
| `set oscillator3_trim` | `<value>` | Set oscillator 3 trim (-5.0 to 5.0) |
| `set phase_threshold` | `<value>` | Set phase detection threshold (0.01-0.5) |
| `set display_brightness` | `<value>` | Set display brightness (0-255) |
| `set wifi_channel` | `<value>` | Set WiFi channel (1-13) |

### Diagnostic Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `diagnostics` | None | Run full system diagnostics |
| `test oscillators` | None | Test oscillator functionality |
| `test phase` | None | Test phase detector functionality |
| `test entropy` | None | Test entropy source |
| `test display` | None | Test display functionality |
| `test network` | None | Test network connectivity |

### Oscillator Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `oscillator status` | None | Show oscillator status |
| `oscillator frequency` | `<index>` | Show oscillator frequency (0-2) |
| `oscillator stability` | None | Show oscillator stability |
| `calibrate oscillator` | `<index>` | Calibrate specific oscillator (0-2) |

### Computation Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `compute prime` | `<number>` | Test if number is prime |
| `compute factorize` | `<number>` | Factorize a number |
| `compute findprimes` | `<start> <end>` | Find primes in range |
| `compute cancel` | None | Cancel current computation |
| `compute status` | None | Show computation status |

### Network Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `network status` | None | Show network status |
| `network scan` | None | Scan for wireless networks |
| `network sync` | None | Force network synchronization |
| `network ping` | `<nodeId>` | Ping specific node |
| `network ping all` | None | Ping all connected nodes |

### Data Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `data export` | `[filename]` | Export data to file |
| `data log` | `<start\|stop>` | Start/stop data logging |
| `data clear` | None | Clear stored data |

### Scripting Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `script list` | None | List available scripts |
| `script run` | `<filename>` | Run script file |
| `script stop` | None | Stop current script |

## Scripting Interface

The Prime Computation Scripting Language (PCSL) provides a simplified programming interface for custom computations. The scripting engine interprets these scripts at runtime.

### PCSL Syntax Reference

| Category | Commands | Description |
|----------|----------|-------------|
| **Variables** | `var = value` | Variable assignment |
| | `INPUT_NUMBER` | Built-in variable for input number |
| | `RESULT_value` | Built-in result values |
| **Oscillator Control** | `OSC_TUNE idx freq` | Set oscillator frequency |
| | `OSC_ENABLE idx state` | Enable/disable oscillator |
| | `OSC_ENABLE_ALL state` | Enable/disable all oscillators |
| **Measurement** | `PHASE_READ osc1 osc2` | Read phase between oscillators |
| | `OSC_FREQ idx` | Read oscillator frequency |
| | `RESONANCE_CALCULATE p1 p2 p3` | Calculate resonance from phases |
| **Entropy** | `ENTROPY_INJECT value` | Inject entropy pattern |
| | `ENTROPY_LEVEL level` | Set entropy level (0.0-1.0) |
| **Flow Control** | `IF condition THEN` | Conditional execution |
| | `ELSE` | Alternative execution path |
| | `END` | End conditional block |
| | `FOR var = start TO end` | Loop from start to end |
| | `END` | End loop block |
| | `DELAY ms` | Pause execution for milliseconds |
| **Output** | `PRINT text` | Output text to console/display |
| | `RESULT_SET name value` | Set a named result |
| **Math** | `+, -, *, /` | Basic arithmetic operators |
| | `>, <, >=, <=, ==, !=` | Comparison operators |
| | `AND, OR, NOT` | Logical operators |
| | `SQRT(x), ABS(x), SIN(x)` | Mathematical functions |

### Example PCSL Script

```
// Prime Number Verification Script
// Uses resonance patterns to verify primality

// Get the number to test
number = INPUT_NUMBER
PRINT "Testing if " + number + " is prime"

// Configure oscillators
OSC_TUNE 1 2.00
OSC_TUNE 2 3.00
OSC_TUNE 3 5.00
OSC_ENABLE_ALL true

// Inject number pattern and wait for stabilization
ENTROPY_INJECT number
DELAY 1000

// Measure phase relationships
phase12 = PHASE_READ 1 2
phase13 = PHASE_READ 1 3
phase23 = PHASE_READ 2 3

PRINT "Phase 1-2: " + phase12
PRINT "Phase 1-3: " + phase13
PRINT "Phase 2-3: " + phase23

// Calculate resonance score (higher for primes)
resonance = RESONANCE_CALCULATE phase12 phase13 phase23
PRINT "Resonance score: " + resonance

// Determine if prime based on resonance threshold
IF resonance > 0.85 THEN
    PRINT number + " is likely PRIME"
    RESULT_SET "isPrime" true
    RESULT_SET "confidence" resonance
ELSE
    PRINT number + " is likely COMPOSITE"
    RESULT_SET "isPrime" false
    RESULT_SET "confidence" (1.0 - resonance)
    
    // Try to find factors
    FOR i = 2 TO SQRT(number)
        // Check if divisible
        IF number % i == 0 THEN
            PRINT "Found factor: " + i
            RESULT_SET "factor1" i
            RESULT_SET "factor2" (number / i)
            // Exit loop early
            i = number
        END
    END
END

PRINT "Computation complete"
```

## Customization Guidelines

### Adding New Features

Follow these guidelines when extending the firmware with new features:

1. **Determine the Appropriate Layer**
   - Is it a hardware interface? Add to HAL layer
   - Is it a new device driver? Add to Drivers layer
   - Is it a shared functionality? Add to Services layer
   - Is it an application-specific feature? Add to Application layer
   - Is it an external interface? Add to API layer

2. **Follow Coding Standards**
   - Use camelCase for variables and methods
   - Use PascalCase for class names
   - Comment public interfaces thoroughly
   - Avoid global variables
   - Use appropriate error handling

3. **Integration Approach**
   - Define clear interfaces for new components
   - Update the configuration system for new parameters
   - Add appropriate commands to the command interface
   - Consider backward compatibility
   - Add appropriate documentation

### Example: Adding a New Computational Algorithm

```cpp
// In compute.h, add new method declaration
class ComputeEngine {
public:
    // Existing methods...
    
    // Add new method
    bool goldbach(uint32_t number, 
                 std::pair<uint32_t, uint32_t>& result);
};

// In compute.cpp, implement the algorithm
bool ComputeEngine::goldbach(uint32_t number, 
                           std::pair<uint32_t, uint32_t>& result) {
    // Check if even and > 2
    if (number <= 2 || number % 2 != 0) {
        return false;
    }
    
    // Goldbach's conjecture: every even integer > 2 is the sum of two primes
    for (uint32_t i = 2; i <= number / 2; i++) {
        if (isPrime(i)) {
            uint32_t j = number - i;
            if (isPrime(j)) {
                result = std::make_pair(i, j);
                return true;
            }
        }
    }
    
    return false; // No solution found (shouldn't happen per the conjecture)
}

// In commands.cpp, add a new command handler
void handleGoldbachCommand(const std::vector<String>& args) {
    if (args.size() < 1) {
        Serial.println("Error: Missing number parameter");
        return;
    }
    
    uint32_t number = args[0].toInt();
    std::pair<uint32_t, uint32_t> result;
    
    if (computeEngine.goldbach(number, result)) {
        Serial.print("Goldbach decomposition of ");
        Serial.print(number);
        Serial.print(" = ");
        Serial.print(result.first);
        Serial.print(" + ");
        Serial.println(result.second);
    } else {
        Serial.println("No Goldbach decomposition found or invalid input");
    }
}

// In commands.cpp, register the new command
void registerCommands() {
    // Existing commands...
    commandProcessor.registerCommand("compute goldbach", handleGoldbachCommand);
}
```

### Optimizing Performance

The firmware can be optimized for better performance:

1. **Memory Optimization**
   - Use static memory allocation where possible
   - Minimize heap fragmentation
   - Consider using FreeRTOS memory management features
   - Use appropriate data types (uint8_t vs int)

2. **Processing Optimization**
   - Move time-critical code to IRAM
   - Use bit manipulation for flags
   - Optimize ADC sampling for phase detection
   - Implement task-specific optimizations

3. **ESP32-Specific Optimizations**
   - Use both cores efficiently
   - Optimize WiFi power management
   - Consider using ESP32's DSP instructions
   - Use DMA for data transfers when appropriate

## Firmware Upgrade Procedures

### Standard Upgrade Process

1. **Prepare the New Firmware**
   - Compile the firmware using Arduino IDE or PlatformIO
   - Generate the binary file (.bin)

2. **Backup Current Configuration**
   - Connect to the node via USB
   - Enter command: `data export config backup.json`
   - Save the exported file to your computer

3. **Upload the Firmware**
   - Use the Arduino IDE: Sketch > Upload
   - Or use esptool.py directly:
     ```
     esptool.py --chip esp32 --port COM5 --baud 921600 write_flash -z 0x10000 firmware.bin
     ```

4. **Restore Configuration**
   - Connect to the node via USB
   - Import the saved configuration:
     ```
     data import config backup.json
     ```
   - Save the imported configuration: `save`
   - Reboot: `reboot`

### OTA Firmware Updates

For remote updates:

1. **Enable OTA in Firmware**
   - Include the ArduinoOTA library
   - Configure OTA password and port
   - Call OTA handler in the main loop

2. **Upload via OTA**
   - In Arduino IDE: Tools > Port > select the network port
   - Sketch > Upload
   - Or use the dedicated OTA tool with the binary file

3. **Verify Update**
   - Check the version after update: `version`
   - Perform basic tests to verify functionality

## Next Steps

With this comprehensive firmware reference, you have all the information needed to understand, modify, and extend the Prime Resonance Computer firmware. The final section provides a complete appendix with additional resources, references, and troubleshooting information.