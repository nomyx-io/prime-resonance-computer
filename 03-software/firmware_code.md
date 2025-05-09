# Prime Resonance Computer: Firmware Code

## Overview

This document provides the complete firmware code for implementing the Prime Resonance Computer on ESP32 microcontrollers. The code is organized into modular components that handle oscillator control, phase detection, network communication, user interface, and the core prime resonance algorithms.

## Table of Contents

1. [Main Program](#main-program)
2. [System Configuration](#system-configuration)
3. [Oscillator Controller](#oscillator-controller)
4. [Phase Detection System](#phase-detection-system)
5. [Network Manager](#network-manager)
6. [User Interface](#user-interface)
7. [Prime Resonance Algorithms](#prime-resonance-algorithms)
8. [Helper Utilities](#helper-utilities)
9. [Implementation Notes](#implementation-notes)

## Main Program

### Coordinator Node main.cpp

```cpp
/*
 * Prime Resonance Computer
 * Coordinator Node Firmware
 * 
 * This firmware implements the main control node for the Prime Resonance Computer.
 * It manages system configuration, coordinates with compute nodes, processes
 * resonance data, and provides user interface.
 */

#include <Arduino.h>
#include <Wire.h>
#include <SPI.h>
#include <EEPROM.h>
#include <esp_now.h>
#include <WiFi.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#include "config.h"
#include "oscillator_controller.h"
#include "phase_detector.h"
#include "network_manager.h"
#include "user_interface.h"
#include "prime_resonance.h"

// Global objects
SystemConfig systemConfig;
OscillatorController oscillatorCtrl;
PhaseDetector phaseDetector;
NetworkManager networkManager;
UserInterface userInterface;
PrimeResonance primeResonance;

// System state variables
OperationMode currentMode = MODE_STANDBY;
bool systemInitialized = false;
unsigned long lastStatusUpdate = 0;
unsigned long lastNetworkUpdate = 0;

// Function prototypes
void initializeSystem();
void processLocalOscillators();
void processUserInput();
void processIncomingMessages();
void updateDisplay();
void enterCalibrationMode();
void handleError(uint8_t errorCode, const char* errorMessage);

void setup() {
  // Initialize serial for debugging
  Serial.begin(115200);
  Serial.println("\nPrime Resonance Computer - Coordinator Node");
  
  // Initialize I2C
  Wire.begin();
  
  // Initialize EEPROM
  EEPROM.begin(EEPROM_SIZE);
  
  // Load system configuration
  if (!loadSystemConfig()) {
    Serial.println("Failed to load configuration, using defaults");
    useDefaultConfig();
  }
  
  // Initialize display first for status messages
  if (!userInterface.begin()) {
    Serial.println("Failed to initialize display");
    // Continue anyway, since this is non-critical
  }
  
  // Display welcome message
  userInterface.showMessage("Initializing...");
  
  // Initialize network
  if (!networkManager.begin(COORDINATOR_NODE_ID)) {
    userInterface.showMessage("Network init failed");
    handleError(ERR_NETWORK_INIT, "Network initialization failed");
    delay(2000);
  }
  
  // Initialize oscillator controller
  if (!oscillatorCtrl.begin()) {
    userInterface.showMessage("Oscillator init failed");
    handleError(ERR_OSCILLATOR_INIT, "Oscillator initialization failed");
    delay(2000);
  }
  
  // Initialize phase detector
  if (!phaseDetector.begin()) {
    userInterface.showMessage("Phase detector failed");
    handleError(ERR_PHASE_DETECTOR_INIT, "Phase detector initialization failed");
    delay(2000);
  }
  
  // Initialize prime resonance system
  if (!primeResonance.begin()) {
    userInterface.showMessage("Resonance init failed");
    handleError(ERR_RESONANCE_INIT, "Resonance system initialization failed");
    delay(2000);
  }
  
  // Complete system initialization
  initializeSystem();
  
  // Initial display update
  updateDisplay();
  
  // Ready
  userInterface.showMessage("System Ready", 1000);
  currentMode = MODE_NORMAL;
  systemInitialized = true;
}

void loop() {
  // Process any pending network messages
  processIncomingMessages();
  
  // Process local oscillators data
  processLocalOscillators();
  
  // Handle user input
  processUserInput();
  
  // Update display periodically
  unsigned long currentTime = millis();
  if (currentTime - lastStatusUpdate > DISPLAY_UPDATE_INTERVAL) {
    updateDisplay();
    lastStatusUpdate = currentTime;
  }
  
  // Send periodic network updates
  if (currentTime - lastNetworkUpdate > NETWORK_UPDATE_INTERVAL) {
    networkManager.broadcastStatus();
    lastNetworkUpdate = currentTime;
  }
  
  // Handle special modes
  if (currentMode == MODE_CALIBRATION) {
    runCalibrationProcess();
  } else if (currentMode == MODE_RESONANCE_DETECTION) {
    runResonanceDetection();
  }
  
  // Allow background processing
  yield();
}

void initializeSystem() {
  // Discover network nodes
  networkManager.discoverNodes();
  
  // Configure oscillators based on system config
  oscillatorCtrl.configureOscillators(systemConfig.oscillatorSettings);
  
  // Configure phase detection parameters
  phaseDetector.configure(systemConfig.phaseDetectorSettings);
  
  // Configure prime resonance system
  primeResonance.configure(systemConfig.resonanceSettings);
  
  // Send initial configuration to all nodes
  networkManager.broadcastConfiguration(&systemConfig);
  
  Serial.println("System initialization complete");
}

void processLocalOscillators() {
  // Read current oscillator frequencies
  oscillatorCtrl.updateFrequencies();
  
  // Read phase detector values
  phaseDetector.samplePhases();
  
  // Process phase data for resonance patterns
  PhaseData phaseData = phaseDetector.getPhaseData();
  primeResonance.processPhaseData(phaseData);
  
  // Check for any oscillator frequency drift
  if (oscillatorCtrl.checkForDrift()) {
    // Oscillator frequency has drifted beyond threshold
    if (systemConfig.autoCalibration) {
      enterCalibrationMode();
    } else {
      userInterface.showMessage("Oscillator drift detected");
    }
  }
}

void processUserInput() {
  // Check for button presses or encoder movement
  UserInput input = userInterface.getInput();
  
  // Process menu navigation
  if (input.type != INPUT_NONE) {
    userInterface.handleInput(input);
    
    // Handle special commands
    if (input.command == CMD_CALIBRATE) {
      enterCalibrationMode();
    } else if (input.command == CMD_START_DETECTION) {
      currentMode = MODE_RESONANCE_DETECTION;
      userInterface.showMessage("Resonance detection started");
    } else if (input.command == CMD_STOP_DETECTION) {
      currentMode = MODE_NORMAL;
      userInterface.showMessage("Resonance detection stopped");
    }
  }
}

void processIncomingMessages() {
  // Process all pending messages
  NetworkMessage message;
  while (networkManager.receiveMessage(&message)) {
    // Handle based on message type
    switch (message.messageType) {
      case MSG_STATUS_UPDATE:
        // Process status update from compute node
        networkManager.updateNodeStatus(message);
        break;
        
      case MSG_PHASE_DATA:
        // Process phase data from compute node
        processRemotePhaseData(message);
        break;
        
      case MSG_ERROR_REPORT:
        // Handle error report from node
        handleNodeError(message);
        break;
        
      case MSG_CALIBRATION_RESULT:
        // Process calibration result
        processCalibrationResult(message);
        break;
        
      default:
        // Unknown message type
        Serial.print("Unknown message type: ");
        Serial.println(message.messageType);
        break;
    }
  }
}

void processRemotePhaseData(NetworkMessage &message) {
  // Extract phase data from message
  PhaseData phaseData;
  memcpy(&phaseData, message.payload, sizeof(PhaseData));
  
  // Process in the prime resonance system
  primeResonance.processRemotePhaseData(phaseData, message.sourceId);
  
  // Update node status timestamp
  networkManager.updateNodeLastSeen(message.sourceId);
}

void updateDisplay() {
  // Update system status display
  SystemStatus status;
  
  // Gather current status data
  status.mode = currentMode;
  status.networkNodes = networkManager.getActiveNodeCount();
  status.totalNodes = networkManager.getTotalNodeCount();
  
  // Get oscillator data
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    status.oscillatorFreq[i] = oscillatorCtrl.getFrequency(i);
  }
  
  // Get phase data
  PhaseData phaseData = phaseDetector.getPhaseData();
  memcpy(status.phaseValues, phaseData.phaseValues, sizeof(phaseData.phaseValues));
  
  // Get resonance data
  status.resonanceStrength = primeResonance.getResonanceStrength();
  status.entropyValue = primeResonance.getCurrentEntropy();
  status.detectedPrimes = primeResonance.getDetectedPrimes();
  
  // Update the display with current status
  userInterface.updateStatus(status);
}

void enterCalibrationMode() {
  // Enter calibration mode
  currentMode = MODE_CALIBRATION;
  userInterface.showMessage("Entering calibration mode");
  
  // Notify compute nodes
  networkManager.broadcastCommand(CMD_ENTER_CALIBRATION);
}

void runCalibrationProcess() {
  static int calibrationStep = 0;
  static unsigned long calibrationTimer = 0;
  
  // First time initialization
  if (calibrationStep == 0) {
    calibrationTimer = millis();
    userInterface.showMessage("Calibrating oscillators");
    calibrationStep = 1;
  }
  
  // Run calibration steps
  switch (calibrationStep) {
    case 1:
      // Measure baseline frequencies
      oscillatorCtrl.measureBaseline();
      calibrationStep = 2;
      calibrationTimer = millis();
      userInterface.showMessage("Adjusting oscillator 1");
      break;
      
    case 2:
      // Calibrate oscillator 1
      if (oscillatorCtrl.calibrateOscillator(0)) {
        calibrationStep = 3;
        calibrationTimer = millis();
        userInterface.showMessage("Adjusting oscillator 2");
      }
      break;
      
    case 3:
      // Calibrate oscillator 2
      if (oscillatorCtrl.calibrateOscillator(1)) {
        calibrationStep = 4;
        calibrationTimer = millis();
        userInterface.showMessage("Adjusting oscillator 3");
      }
      break;
      
    case 4:
      // Calibrate oscillator 3
      if (oscillatorCtrl.calibrateOscillator(2)) {
        calibrationStep = 5;
        calibrationTimer = millis();
        userInterface.showMessage("Verifying calibration");
      }
      break;
      
    case 5:
      // Verify all oscillators
      if (oscillatorCtrl.verifyCalibration()) {
        // Calibration successful
        systemConfig.oscillatorSettings = oscillatorCtrl.getSettings();
        saveSystemConfig();
        userInterface.showMessage("Calibration successful", 2000);
        
        // Return to normal mode
        currentMode = MODE_NORMAL;
        calibrationStep = 0;
      } else {
        // Calibration failed
        userInterface.showMessage("Calibration failed", 2000);
        
        // Return to normal mode
        currentMode = MODE_NORMAL;
        calibrationStep = 0;
      }
      break;
  }
  
  // Timeout check
  if (millis() - calibrationTimer > CALIBRATION_TIMEOUT) {
    userInterface.showMessage("Calibration timeout", 2000);
    currentMode = MODE_NORMAL;
    calibrationStep = 0;
  }
}

void runResonanceDetection() {
  // Update prime resonance detection
  primeResonance.update();
  
  // Check if we have a result
  if (primeResonance.resultAvailable()) {
    // Get the result
    PrimeResult result = primeResonance.getResult();
    
    // Display result
    char resultMsg[32];
    sprintf(resultMsg, "Result: %d primes", result.primeCount);
    userInterface.showMessage(resultMsg);
    
    // Show the detected primes
    userInterface.showPrimeResult(result);
  }
}

void handleError(uint8_t errorCode, const char* errorMessage) {
  Serial.print("ERROR (");
  Serial.print(errorCode);
  Serial.print("): ");
  Serial.println(errorMessage);
  
  // Log error
  systemLog.addEntry(LOG_ERROR, errorCode, errorMessage);
  
  // Show on display if available
  userInterface.showError(errorCode, errorMessage);
  
  // For critical errors, restart system
  if (errorCode < 128) {  // Critical errors are below 128
    delay(5000);  // Give time to read the error
    ESP.restart();
  }
}

void handleNodeError(NetworkMessage &message) {
  // Extract error data from message
  uint8_t nodeId = message.sourceId;
  uint8_t errorCode = message.payload[0];
  char errorMessage[32];
  strncpy(errorMessage, (char*)&message.payload[1], 31);
  errorMessage[31] = '\0';  // Ensure null termination
  
  // Log node error
  char logMessage[64];
  sprintf(logMessage, "Node %d error: %s", nodeId, errorMessage);
  systemLog.addEntry(LOG_ERROR, errorCode, logMessage);
  
  // Show briefly on display
  userInterface.showMessage(logMessage, 2000);
  
  // Update node error status
  networkManager.setNodeError(nodeId, errorCode);
}

void processCalibrationResult(NetworkMessage &message) {
  // Extract calibration result
  uint8_t nodeId = message.sourceId;
  bool success = message.payload[0];
  
  if (success) {
    char logMessage[32];
    sprintf(logMessage, "Node %d calibration successful", nodeId);
    systemLog.addEntry(LOG_INFO, 0, logMessage);
  } else {
    char logMessage[32];
    sprintf(logMessage, "Node %d calibration failed", nodeId);
    systemLog.addEntry(LOG_WARNING, 0, logMessage);
    
    // Show message
    userInterface.showMessage(logMessage, 2000);
  }
}

bool loadSystemConfig() {
  // Read configuration from EEPROM
  EEPROM.readBytes(CONFIG_ADDRESS, &systemConfig, sizeof(systemConfig));
  
  // Verify configuration version and checksum
  if (systemConfig.version != CONFIG_VERSION) {
    return false;
  }
  
  uint16_t checksum = calculateChecksum((uint8_t*)&systemConfig, sizeof(systemConfig) - 2);
  if (checksum != systemConfig.checksum) {
    return false;
  }
  
  return true;
}

void saveSystemConfig() {
  // Update checksum
  systemConfig.checksum = calculateChecksum((uint8_t*)&systemConfig, sizeof(systemConfig) - 2);
  
  // Save to EEPROM
  EEPROM.writeBytes(CONFIG_ADDRESS, &systemConfig, sizeof(systemConfig));
  EEPROM.commit();
  
  Serial.println("Configuration saved");
}

void useDefaultConfig() {
  // Set up default configuration
  systemConfig.version = CONFIG_VERSION;
  systemConfig.nodeId = COORDINATOR_NODE_ID;
  systemConfig.networkNodeCount = DEFAULT_NODE_COUNT;
  systemConfig.autoCalibration = true;
  
  // Default oscillator settings
  systemConfig.oscillatorSettings[0].targetFrequency = 2.0f;  // 2 Hz
  systemConfig.oscillatorSettings[1].targetFrequency = 3.0f;  // 3 Hz
  systemConfig.oscillatorSettings[2].targetFrequency = 5.0f;  // 5 Hz
  
  // Default tolerances
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    systemConfig.oscillatorSettings[i].tolerance = DEFAULT_FREQUENCY_TOLERANCE;
    systemConfig.oscillatorSettings[i].calibrationValue = 128;  // Mid-point
  }
  
  // Default phase detector settings
  for (int i = 0; i < NUM_PHASE_DETECTORS; i++) {
    systemConfig.phaseDetectorSettings[i].sampleRate = DEFAULT_SAMPLE_RATE;
    systemConfig.phaseDetectorSettings[i].filterLevel = DEFAULT_FILTER_LEVEL;
  }
  
  // Default resonance settings
  systemConfig.resonanceSettings.threshold = DEFAULT_RESONANCE_THRESHOLD;
  systemConfig.resonanceSettings.minIterations = DEFAULT_MIN_ITERATIONS;
  systemConfig.resonanceSettings.entropyThreshold = DEFAULT_ENTROPY_THRESHOLD;
  
  // Default display settings
  systemConfig.displaySettings.brightness = DEFAULT_BRIGHTNESS;
  systemConfig.displaySettings.contrast = DEFAULT_CONTRAST;
  
  // Save default configuration
  saveSystemConfig();
}

uint16_t calculateChecksum(uint8_t* data, size_t length) {
  uint16_t checksum = 0;
  for (size_t i = 0; i < length; i++) {
    checksum += data[i];
  }
  return checksum;
}
```

### Compute Node main.cpp

```cpp
/*
 * Prime Resonance Computer
 * Compute Node Firmware
 * 
 * This firmware implements a compute node for the Prime Resonance Computer.
 * It manages local oscillators, detects phase relationships, and communicates
 * with the coordinator node.
 */

#include <Arduino.h>
#include <Wire.h>
#include <SPI.h>
#include <EEPROM.h>
#include <esp_now.h>
#include <WiFi.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#include "config.h"
#include "oscillator_controller.h"
#include "phase_detector.h"
#include "network_manager.h"
#include "user_interface.h"

// Global objects
SystemConfig systemConfig;
OscillatorController oscillatorCtrl;
PhaseDetector phaseDetector;
NetworkManager networkManager;
UserInterface userInterface;

// System state variables
OperationMode currentMode = MODE_STANDBY;
bool systemInitialized = false;
bool configurationReceived = false;
unsigned long lastStatusUpdate = 0;
unsigned long lastDataTransmit = 0;
uint8_t nodeId = 0;

// Function prototypes
void initializeSystem();
void processLocalOscillators();
void processUserInput();
void processIncomingMessages();
void updateDisplay();
void enterCalibrationMode();
void transmitData();
void handleError(uint8_t errorCode, const char* errorMessage);

void setup() {
  // Initialize serial for debugging
  Serial.begin(115200);
  Serial.println("\nPrime Resonance Computer - Compute Node");
  
  // Read node ID from dip switches or EEPROM
  nodeId = readNodeId();
  Serial.print("Node ID: ");
  Serial.println(nodeId);
  
  // Initialize I2C
  Wire.begin();
  
  // Initialize EEPROM
  EEPROM.begin(EEPROM_SIZE);
  
  // Load system configuration
  if (!loadSystemConfig()) {
    Serial.println("Failed to load configuration, waiting for coordinator");
  }
  
  // Initialize display first for status messages
  if (!userInterface.begin()) {
    Serial.println("Failed to initialize display");
    // Continue anyway, since this is non-critical
  }
  
  // Display welcome message
  userInterface.showMessage("Node Initializing...");
  
  // Initialize network
  if (!networkManager.begin(nodeId)) {
    userInterface.showMessage("Network init failed");
    handleError(ERR_NETWORK_INIT, "Network initialization failed");
    delay(2000);
  }
  
  // Initialize oscillator controller
  if (!oscillatorCtrl.begin()) {
    userInterface.showMessage("Oscillator init failed");
    handleError(ERR_OSCILLATOR_INIT, "Oscillator initialization failed");
    delay(2000);
  }
  
  // Initialize phase detector
  if (!phaseDetector.begin()) {
    userInterface.showMessage("Phase detector failed");
    handleError(ERR_PHASE_DETECTOR_INIT, "Phase detector initialization failed");
    delay(2000);
  }
  
  // Enter discovery mode
  currentMode = MODE_DISCOVERY;
  userInterface.showMessage("Waiting for coordinator");
  
  // Initial display update
  updateDisplay();
}

void loop() {
  // Process any pending network messages
  processIncomingMessages();
  
  // If we're not initialized yet, keep waiting for configuration
  if (!systemInitialized) {
    // Check if configuration has been received
    if (configurationReceived) {
      initializeSystem();
    } else {
      // Blink status LED
      blinkStatus();
      
      // Wait for configuration
      unsigned long currentTime = millis();
      if (currentTime - lastStatusUpdate > STATUS_BROADCAST_INTERVAL) {
        networkManager.broadcastDiscovery();
        lastStatusUpdate = currentTime;
      }
      
      // Allow CPU to do other tasks
      delay(10);
      return;
    }
  }
  
  // Process local oscillators data
  processLocalOscillators();
  
  // Handle user input
  processUserInput();
  
  // Update display periodically
  unsigned long currentTime = millis();
  if (currentTime - lastStatusUpdate > DISPLAY_UPDATE_INTERVAL) {
    updateDisplay();
    lastStatusUpdate = currentTime;
  }
  
  // Transmit data periodically
  if (currentTime - lastDataTransmit > DATA_TRANSMIT_INTERVAL) {
    transmitData();
    lastDataTransmit = currentTime;
  }
  
  // Handle special modes
  if (currentMode == MODE_CALIBRATION) {
    runCalibrationProcess();
  }
  
  // Allow background processing
  yield();
}

uint8_t readNodeId() {
  // Read Node ID from hardware (DIP switches or jumpers)
  // This is hardware-specific implementation
  // For this example, we'll read from EEPROM location 0
  uint8_t id = EEPROM.read(0);
  
  // Validate range (2-15 for compute nodes)
  if (id < 2 || id > 15) {
    // Default to ID 2 if invalid
    id = 2;
    EEPROM.write(0, id);
    EEPROM.commit();
  }
  
  return id;
}

void initializeSystem() {
  // Configure oscillators based on system config
  oscillatorCtrl.configureOscillators(systemConfig.oscillatorSettings);
  
  // Configure phase detection parameters
  phaseDetector.configure(systemConfig.phaseDetectorSettings);
  
  // Ready
  userInterface.showMessage("System Ready", 1000);
  currentMode = MODE_NORMAL;
  systemInitialized = true;
  
  // Send status to coordinator
  networkManager.sendStatus();
  
  Serial.println("System initialization complete");
}

void processLocalOscillators() {
  // Read current oscillator frequencies
  oscillatorCtrl.updateFrequencies();
  
  // Read phase detector values
  phaseDetector.samplePhases();
  
  // Check for any oscillator frequency drift
  if (oscillatorCtrl.checkForDrift()) {
    // Oscillator frequency has drifted beyond threshold
    if (systemConfig.autoCalibration) {
      enterCalibrationMode();
    } else {
      userInterface.showMessage("Oscillator drift detected");
      // Notify coordinator
      networkManager.sendAlert(ALERT_OSCILLATOR_DRIFT);
    }
  }
}

void processUserInput() {
  // Check for button presses or encoder movement
  UserInput input = userInterface.getInput();
  
  // Process menu navigation
  if (input.type != INPUT_NONE) {
    userInterface.handleInput(input);
    
    // Handle special commands
    if (input.command == CMD_CALIBRATE) {
      enterCalibrationMode();
    }
  }
}

void processIncomingMessages() {
  // Process all pending messages
  NetworkMessage message;
  while (networkManager.receiveMessage(&message)) {
    // Handle based on message type
    switch (message.messageType) {
      case MSG_SYSTEM_CONFIG:
        // Process system configuration from coordinator
        memcpy(&systemConfig, message.payload, sizeof(SystemConfig));
        configurationReceived = true;
        Serial.println("Received system configuration");
        break;
        
      case MSG_COMMAND:
        // Process command from coordinator
        handleCommand(message);
        break;
        
      case MSG_CALIBRATE:
        // Enter calibration mode
        enterCalibrationMode();
        break;
        
      default:
        // Unknown message type
        Serial.print("Unknown message type: ");
        Serial.println(message.messageType);
        break;
    }
  }
}

void handleCommand(NetworkMessage &message) {
  // Extract command from message
  uint8_t command = message.payload[0];
  
  switch (command) {
    case CMD_ENTER_CALIBRATION:
      enterCalibrationMode();
      break;
      
    case CMD_RESET:
      ESP.restart();
      break;
      
    case CMD_REPORT_STATUS:
      networkManager.sendStatus();
      break;
      
    default:
      // Unknown command
      Serial.print("Unknown command: ");
      Serial.println(command);
      break;
  }
}

void updateDisplay() {
  // Update system status display
  SystemStatus status;
  
  // Gather current status data
  status.mode = currentMode;
  status.networkConnected = networkManager.isConnected();
  status.nodeId = nodeId;
  
  // Get oscillator data
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    status.oscillatorFreq[i] = oscillatorCtrl.getFrequency(i);
  }
  
  // Get phase data
  PhaseData phaseData = phaseDetector.getPhaseData();
  memcpy(status.phaseValues, phaseData.phaseValues, sizeof(phaseData.phaseValues));
  
  // Update the display with current status
  userInterface.updateStatus(status);
}

void enterCalibrationMode() {
  // Enter calibration mode
  currentMode = MODE_CALIBRATION;
  userInterface.showMessage("Entering calibration mode");
}

void runCalibrationProcess() {
  static int calibrationStep = 0;
  static unsigned long calibrationTimer = 0;
  
  // First time initialization
  if (calibrationStep == 0) {
    calibrationTimer = millis();
    userInterface.showMessage("Calibrating oscillators");
    calibrationStep = 1;
  }
  
  // Run calibration steps
  switch (calibrationStep) {
    case 1:
      // Measure baseline frequencies
      oscillatorCtrl.measureBaseline();
      calibrationStep = 2;
      calibrationTimer = millis();
      userInterface.showMessage("Adjusting oscillator 1");
      break;
      
    case 2:
      // Calibrate oscillator 1
      if (oscillatorCtrl.calibrateOscillator(0)) {
        calibrationStep = 3;
        calibrationTimer = millis();
        userInterface.showMessage("Adjusting oscillator 2");
      }
      break;
      
    case 3:
      // Calibrate oscillator 2
      if (oscillatorCtrl.calibrateOscillator(1)) {
        calibrationStep = 4;
        calibrationTimer = millis();
        userInterface.showMessage("Adjusting oscillator 3");
      }
      break;
      
    case 4:
      // Calibrate oscillator 3
      if (oscillatorCtrl.calibrateOscillator(2)) {
        calibrationStep = 5;
        calibrationTimer = millis();
        userInterface.showMessage("Verifying calibration");
      }
      break;
      
    case 5:
      // Verify all oscillators
      bool success = oscillatorCtrl.verifyCalibration();
      
      if (success) {
        // Calibration successful
        systemConfig.oscillatorSettings = oscillatorCtrl.getSettings();
        saveSystemConfig();
        userInterface.showMessage("Calibration successful", 2000);
        
        // Notify coordinator
        networkManager.sendCalibrationResult(true);
      } else {
        // Calibration failed
        userInterface.showMessage("Calibration failed", 2000);
        
        // Notify coordinator
        networkManager.sendCalibrationResult(false);
      }
      
      // Return to normal mode
      currentMode = MODE_NORMAL;
      calibrationStep = 0;
      break;
  }
  
  // Timeout check
  if (millis() - calibrationTimer > CALIBRATION_TIMEOUT) {
    userInterface.showMessage("Calibration timeout", 2000);
    currentMode = MODE_NORMAL;
    calibrationStep = 0;
    
    // Notify coordinator
    networkManager.sendCalibrationResult(false);
  }
}

void transmitData() {
  // Get current phase data
  PhaseData phaseData = phaseDetector.getPhaseData();
  
  // Send to coordinator
  networkManager.sendPhaseData(&phaseData);
  
  // Update timestamp
  lastDataTransmit = millis();
}

void handleError(uint8_t errorCode, const char* errorMessage) {
  Serial.print("ERROR (");
  Serial.print(errorCode);
  Serial.print("): ");
  Serial.println(errorMessage);
  
  // Show on display if available
  userInterface.showError(errorCode, errorMessage);
  
  // Send error to coordinator if network is available
  if (networkManager.isConnected()) {
    networkManager.sendError(errorCode, errorMessage);
  }
  
  // For critical errors, restart system
  if (errorCode < 128) {  // Critical errors are below 128
    delay(5000);  // Give time to read the error
    ESP.restart();
  }
}

bool loadSystemConfig() {
  // Read configuration from EEPROM
  EEPROM.readBytes(CONFIG_ADDRESS, &systemConfig, sizeof(systemConfig));
  
  // Verify configuration version and checksum
  if (systemConfig.version != CONFIG_VERSION) {
    return false;
  }
  
  uint16_t checksum = calculateChecksum((uint8_t*)&systemConfig, sizeof(systemConfig) - 2);
  if (checksum != systemConfig.checksum) {
    return false;
  }
  
  return true;
}

void saveSystemConfig() {
  // Update checksum
  systemConfig.checksum = calculateChecksum((uint8_t*)&systemConfig, sizeof(systemConfig) - 2);
  
  // Save to EEPROM
  EEPROM.writeBytes(CONFIG_ADDRESS, &systemConfig, sizeof(systemConfig));
  EEPROM.commit();
  
  Serial.println("Configuration saved");
}

uint16_t calculateChecksum(uint8_t* data, size_t length) {
  uint16_t checksum = 0;
  for (size_t i = 0; i < length; i++) {
    checksum += data[i];
  }
  return checksum;
}

void blinkStatus() {
  // Blink status LED to indicate waiting for configuration
  static unsigned long lastBlink = 0;
  static bool ledState = false;
  
  if (millis() - lastBlink > BLINK_INTERVAL) {
    ledState = !ledState;
    digitalWrite(STATUS_LED_PIN, ledState);
    lastBlink = millis();
  }
}
```

## System Configuration

### config.h

```cpp
/*
 * Prime Resonance Computer - Configuration Header
 * 
 * This file contains all system-wide constants and configuration structures.
 */

#ifndef CONFIG_H
#define CONFIG_H

#include <Arduino.h>

// System version
#define FIRMWARE_VERSION "1.0.0"
#define CONFIG_VERSION 1

// Node roles
#define COORDINATOR_NODE_ID 1
#define DEFAULT_NODE_COUNT 3

// Hardware configuration
#define NUM_OSCILLATORS 3
#define NUM_PHASE_DETECTORS 3  // 3 pairs from 3 oscillators
#define STATUS_LED_PIN 2

// GPIO pin assignments
// Oscillator control pins
#define OSC1_CONTROL_PIN 32
#define OSC2_CONTROL_PIN 33
#define OSC3_CONTROL_PIN 25

// Oscillator monitoring pins
#define OSC1_MONITOR_PIN 34
#define OSC2_MONITOR_PIN 35
#define OSC3_MONITOR_PIN 36

// Phase detector pins
#define PHASE_DETECTOR_1_PIN 39  // 2Hz-3Hz phase
#define PHASE_DETECTOR_2_PIN 27  // 2Hz-5Hz phase
#define PHASE_DETECTOR_3_PIN 14  // 3Hz-5Hz phase

// User interface pins
#define BUTTON_1_PIN 13
#define BUTTON_2_PIN 12
#define BUTTON_3_PIN 15
#define ROTARY_A_PIN 17
#define ROTARY_B_PIN 16
#define ROTARY_BUTTON_PIN 4
#define OLED_RESET_PIN 5

// OLED display parameters
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define SCREEN_ADDRESS 0x3C

// EEPROM configuration
#define EEPROM_SIZE 1024
#define CONFIG_ADDRESS 16

// Timing constants
#define DISPLAY_UPDATE_INTERVAL 500    // ms
#define DATA_TRANSMIT_INTERVAL 1000    // ms
#define NETWORK_UPDATE_INTERVAL 5000   // ms
#define STATUS_BROADCAST_INTERVAL 2000 // ms
#define CALIBRATION_TIMEOUT 60000      // ms
#define BLINK_INTERVAL 500             // ms

// Default parameters
#define DEFAULT_FREQUENCY_TOLERANCE 0.05f  // 5%
#define DEFAULT_SAMPLE_RATE 50            // 50Hz
#define DEFAULT_FILTER_LEVEL 3            // Medium filtering
#define DEFAULT_RESONANCE_THRESHOLD 0.75f // 75% resonance threshold
#define DEFAULT_MIN_ITERATIONS 100        // Minimum iterations
#define DEFAULT_ENTROPY_THRESHOLD 0.2f    // 20% entropy threshold
#define DEFAULT_BRIGHTNESS 15             // Maximum brightness
#define DEFAULT_CONTRAST 127              // Medium contrast

// Error codes
#define ERR_NETWORK_INIT 1
#define ERR_OSCILLATOR_INIT 2
#define ERR_PHASE_DETECTOR_INIT 3
#define ERR_RESONANCE_INIT 4
#define ERR_DISPLAY_INIT 5
#define ERR_CONFIG_LOAD 6
#define ERR_CONFIG_SAVE 7
#define ERR_CALIBRATION 8
// Non-critical errors (>= 128)
#define ERR_NETWORK_TIMEOUT 128
#define ERR_DRIFT_DETECTED 129

// Alert codes
#define ALERT_OSCILLATOR_DRIFT 1
#define ALERT_PHASE_ANOMALY 2
#define ALERT_RESONANCE_DETECTED 3

// Commands
#define CMD_CALIBRATE 1
#define CMD_ENTER_CALIBRATION 2
#define CMD_RESET 3
#define CMD_REPORT_STATUS 4
#define CMD_START_DETECTION 5
#define CMD_STOP_DETECTION 6

// Message types
#define MSG_STATUS_UPDATE 1
#define MSG_SYSTEM_CONFIG 2
#define MSG_COMMAND 3
#define MSG_PHASE_DATA 4
#define MSG_CALIBRATE 5
#define MSG_ERROR_REPORT 6
#define MSG_CALIBRATION_RESULT 7

// Operation modes
enum OperationMode {
  MODE_STANDBY = 0,
  MODE_DISCOVERY,
  MODE_NORMAL,
  MODE_CALIBRATION,
  MODE_RESONANCE_DETECTION
};

// Input types
enum InputType {
  INPUT_NONE = 0,
  INPUT_BUTTON,
  INPUT_ENCODER,
  INPUT_ENCODER_BUTTON
};

// Log levels
enum LogLevel {
  LOG_ERROR = 0,
  LOG_WARNING,
  LOG_INFO,
  LOG_DEBUG
};

// Structures

// Oscillator settings
struct OscillatorSettings {
  float targetFrequency;    // Target frequency in Hz
  float tolerance;          // Acceptable tolerance
  uint8_t calibrationValue; // PWM value for tuning
  uint8_t reserved;         // For future use
};

// Phase detector settings
struct PhaseDetectorSettings {
  uint16_t sampleRate;      // Samples per second
  uint8_t filterLevel;      // Digital filter strength (0-10)
  uint8_t reserved;         // For future use
};

// Resonance detection settings
struct ResonanceSettings {
  float threshold;          // Resonance detection threshold
  uint16_t minIterations;   // Minimum iterations before result
  float entropyThreshold;   // Entropy collapse threshold
  uint8_t reserved;         // For future use
};

// Display settings
struct DisplaySettings {
  uint8_t brightness;       // Display brightness (0-15)
  uint8_t contrast;         // Display contrast (0-255)
  bool invertDisplay;       // Invert display colors
  uint8_t reserved;         // For future use
};

// System configuration structure
struct SystemConfig {
  // Header
  uint8_t version;          // Configuration structure version
  uint8_t nodeId;           // This node's ID
  uint8_t networkNodeCount; // Total nodes in network
  bool autoCalibration;     // Auto-calibration on drift
  
  // Component settings
  OscillatorSettings oscillatorSettings[NUM_OSCILLATORS];
  PhaseDetectorSettings phaseDetectorSettings[NUM_PHASE_DETECTORS];
  ResonanceSettings resonanceSettings;
  DisplaySettings displaySettings;
  
  // Reserved for future expansion
  uint8_t reserved[16];
  
  // Checksum for validation
  uint16_t checksum;
};

// User input structure
struct UserInput {
  InputType type;
  uint8_t buttonId;
  int8_t encoderDelta;
  uint8_t command;
};

// Phase data structure
struct PhaseData {
  uint32_t timestamp;
  float oscillatorFreq[NUM_OSCILLATORS];
  float phaseValues[NUM_PHASE_DETECTORS];
  uint8_t nodeId;
  uint8_t sequenceNumber;
};

// System status structure
struct SystemStatus {
  OperationMode mode;
  uint8_t networkNodes;
  uint8_t totalNodes;
  bool networkConnected;
  uint8_t nodeId;
  float oscillatorFreq[NUM_OSCILLATORS];
  float phaseValues[NUM_PHASE_DETECTORS];
  float resonanceStrength;
  float entropyValue;
  uint8_t detectedPrimes[16];
  uint8_t primeCount;
};

// Network message structure
struct NetworkMessage {
  uint8_t messageType;
  uint8_t sourceId;
  uint8_t targetId;
  uint8_t sequenceNumber;
  uint8_t payload[250];
  uint8_t payloadLength;
};

// Prime detection result
struct PrimeResult {
  uint8_t primes[16];
  uint8_t primeCount;
  float confidence;
  uint32_t computeTime;
};

#endif // CONFIG_H
```

## Oscillator Controller

### oscillator_controller.h

```cpp
/*
 * Prime Resonance Computer
 * Oscillator Controller Module
 * 
 * This module manages the hardware oscillators, controlling their frequency
 * and measuring their output.
 */

#ifndef OSCILLATOR_CONTROLLER_H
#define OSCILLATOR_CONTROLLER_H

#include <Arduino.h>
#include "config.h"

class OscillatorController {
private:
  // Configuration
  OscillatorSettings m_settings[NUM_OSCILLATORS];
  
  // Monitoring pins
  uint8_t m_monitorPins[NUM_OSCILLATORS] = {
    OSC1_MONITOR_PIN, OSC2_MONITOR_PIN, OSC3_MONITOR_PIN
  };
  
  // Control pins
  uint8_t m_controlPins[NUM_OSCILLATORS] = {
    OSC1_CONTROL_PIN, OSC2_CONTROL_PIN, OSC3_CONTROL_PIN
  };
  
  // Channel for PWM output
  uint8_t m_pwmChannels[NUM_OSCILLATORS] = {0, 1, 2};
  
  // Current measured frequencies
  float m_currentFreq[NUM_OSCILLATORS] = {0};
  
  // Calibration process variables
  bool m_calibrating = false;
  float m_baselineFreq[NUM_OSCILLATORS] = {0};
  
  // Private methods
  float measureFrequency(uint8_t oscillator);
  void adjustOscillator(uint8_t oscillator, uint8_t value);

public:
  OscillatorController();
  
  // Initialization
  bool begin();
  void configureOscillators(const OscillatorSettings settings[]);
  
  // Monitoring
  void updateFrequencies();
  float getFrequency(uint8_t oscillator);
  bool checkForDrift();
  
  // Calibration
  void measureBaseline();
  bool calibrateOscillator(uint8_t oscillator);
  bool verifyCalibration();
  
  // Configuration
  OscillatorSettings* getSettings() { return m_settings; }
};

#endif // OSCILLATOR_CONTROLLER_H
```

### oscillator_controller.cpp

```cpp
/*
 * Prime Resonance Computer
 * Oscillator Controller Implementation
 */

#include "oscillator_controller.h"

OscillatorController::OscillatorController() {
  // Initialize with default settings
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    m_settings[i].targetFrequency = 0.0f;
    m_settings[i].tolerance = DEFAULT_FREQUENCY_TOLERANCE;
    m_settings[i].calibrationValue = 128;  // Mid-point
  }
}

bool OscillatorController::begin() {
  // Set up PWM outputs for oscillator control
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    // Configure PWM channel
    ledcSetup(m_pwmChannels[i], 5000, 8);  // 5kHz, 8-bit resolution
    
    // Attach channel to GPIO pin
    ledcAttachPin(m_controlPins[i], m_pwmChannels[i]);
    
    // Set initial value
    ledcWrite(m_pwmChannels[i], m_settings[i].calibrationValue);
    
    // Configure monitor pin as input
    pinMode(m_monitorPins[i], INPUT);
  }
  
  // Initial frequency measurement
  updateFrequencies();
  
  return true;
}

void OscillatorController::configureOscillators(const OscillatorSettings settings[]) {
  // Copy settings
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    m_settings[i] = settings[i];
    
    // Apply calibration value
    ledcWrite(m_pwmChannels[i], m_settings[i].calibrationValue);
  }
  
  // Allow oscillators to stabilize
  delay(500);
  
  // Update frequency measurements
  updateFrequencies();
}

void OscillatorController::updateFrequencies() {
  // Measure all oscillator frequencies
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    m_currentFreq[i] = measureFrequency(i);
  }
}

float OscillatorController::getFrequency(uint8_t oscillator) {
  if (oscillator < NUM_OSCILLATORS) {
    return m_currentFreq[oscillator];
  }
  return 0.0f;
}

bool OscillatorController::checkForDrift() {
  // Check each oscillator against its target frequency
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    float target = m_settings[i].targetFrequency;
    float current = m_currentFreq[i];
    float tolerance = m_settings[i].tolerance;
    
    // Check if drift exceeds tolerance
    if (fabs(current - target) > (target * tolerance)) {
      return true;  // Drift detected
    }
  }
  
  return false;  // No significant drift
}

float OscillatorController::measureFrequency(uint8_t oscillator) {
  if (oscillator >= NUM_OSCILLATORS) {
    return 0.0f;
  }
  
  // We'll count pulses from the oscillator for a fixed time window
  const unsigned long measureTime = 5000;  // 5 seconds for low frequencies
  unsigned long startTime = millis();
  unsigned long endTime = startTime + measureTime;
  
  // Initial state of the input pin
  int lastState = digitalRead(m_monitorPins[oscillator]);
  unsigned int pulseCount = 0;
  
  // Count pulses until time expires
  while (millis() < endTime) {
    int currentState = digitalRead(m_monitorPins[oscillator]);
    
    // Detect rising edge
    if (currentState == HIGH && lastState == LOW) {
      pulseCount++;
    }
    
    lastState = currentState;
    
    // Don't hog the CPU
    yield();
  }
  
  // Calculate frequency in Hz
  // Each complete cycle has 2 edges (rising and falling)
  // We count only rising edges, so pulseCount = number of cycles
  float frequency = (float)pulseCount / (measureTime / 1000.0f);
  
  return frequency;
}

void OscillatorController::adjustOscillator(uint8_t oscillator, uint8_t value) {
  if (oscillator < NUM_OSCILLATORS) {
    // Update the calibration value
    m_settings[oscillator].calibrationValue = value;
    
    // Apply to hardware
    ledcWrite(m_pwmChannels[oscillator], value);
  }
}

void OscillatorController::measureBaseline() {
  // Measure baseline frequencies of all oscillators
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    m_baselineFreq[i] = measureFrequency(i);
  }
}

bool OscillatorController::calibrateOscillator(uint8_t oscillator) {
  if (oscillator >= NUM_OSCILLATORS) {
    return false;
  }
  
  // Target frequency
  float targetFreq = m_settings[oscillator].targetFrequency;
  
  // Current frequency
  float currentFreq = measureFrequency(oscillator);
  
  // Check if already within tolerance
  float tolerance = m_settings[oscillator].tolerance;
  if (fabs(currentFreq - targetFreq) <= (targetFreq * tolerance)) {
    return true;  // Already calibrated
  }
  
  // Binary search for correct calibration value
  int minValue = 0;
  int maxValue = 255;
  int currentValue = m_settings[oscillator].calibrationValue;
  int iterations = 0;
  const int maxIterations = 10;  // Prevent infinite loops
  
  while (iterations < maxIterations) {
    // Apply current test value
    adjustOscillator(oscillator, currentValue);
    
    // Allow oscillator to stabilize
    delay(1000);
    
    // Measure new frequency
    currentFreq = measureFrequency(oscillator);
    
    // Check if within tolerance
    if (fabs(currentFreq - targetFreq) <= (targetFreq * tolerance)) {
      return true;  // Calibration successful
    }
    
    // Adjust search range
    if (currentFreq < targetFreq) {
      // Need to increase frequency
      minValue = currentValue;
      currentValue = (currentValue + maxValue) / 2;
    } else {
      // Need to decrease frequency
      maxValue = currentValue;
      currentValue = (currentValue + minValue) / 2;
    }
    
    // If we can't narrow down further, break
    if (maxValue - minValue <= 1) {
      // Use the closest value
      adjustOscillator(oscillator, currentValue);
      delay(1000);
      return (fabs(measureFrequency(oscillator) - targetFreq) <= (targetFreq * tolerance));
    }
    
    iterations++;
  }
  
  // Calibration failed to converge
  return false;
}

bool OscillatorController::verifyCalibration() {
  // Check all oscillators are within tolerance
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    float targetFreq = m_settings[i].targetFrequency;
    float currentFreq = measureFrequency(i);
    float tolerance = m_settings[i].tolerance;
    
    if (fabs(currentFreq - targetFreq) > (targetFreq * tolerance)) {
      return false;  // One oscillator is out of tolerance
    }
  }
  
  return true;  // All oscillators calibrated successfully
}
```

## Phase Detection System

### phase_detector.h

```cpp
/*
 * Prime Resonance Computer
 * Phase Detection System
 * 
 * This module samples and analyzes the phase relationships between oscillators.
 */

#ifndef PHASE_DETECTOR_H
#define PHASE_DETECTOR_H

#include <Arduino.h>
#include "config.h"

class PhaseDetector {
private:
  // Configuration
  PhaseDetectorSettings m_settings[NUM_PHASE_DETECTORS];
  
  // ADC pins
  uint8_t m_adcPins[NUM_PHASE_DETECTORS] = {
    PHASE_DETECTOR_1_PIN, PHASE_DETECTOR_2_PIN, PHASE_DETECTOR_3_PIN
  };
  
  // Current phase data
  PhaseData m_phaseData;
  
  // Buffer for filtering
  float m_filterBuffer[NUM_PHASE_DETECTORS][10];
  uint8_t m_filterIndex[NUM_PHASE_DETECTORS] = {0};
  
  // Private methods
  float filterReading(uint8_t detector, float rawValue);
  float convertToDegrees(float rawValue);

public:
  PhaseDetector();
  
  // Initialization
  bool begin();
  void configure(const PhaseDetectorSettings settings[]);
  
  // Sampling/analysis
  void samplePhases();
  const PhaseData& getPhaseData() const { return m_phaseData; }
  
  // Phase relationships
  float getPhaseAngle(uint8_t detector) const;
  float getSynchronizationIndex() const;
};

#endif // PHASE_DETECTOR_H
```

### phase_detector.cpp

```cpp
/*
 * Prime Resonance Computer
 * Phase Detection System Implementation
 */

#include "phase_detector.h"

PhaseDetector::PhaseDetector() {
  // Initialize with default settings
  for (int i = 0; i < NUM_PHASE_DETECTORS; i++) {
    m_settings[i].sampleRate = DEFAULT_SAMPLE_RATE;
    m_settings[i].filterLevel = DEFAULT_FILTER_LEVEL;
  }
  
  // Initialize phase data
  m_phaseData.timestamp = 0;
  m_phaseData.sequenceNumber = 0;
  m_phaseData.nodeId = 0;
  
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    m_phaseData.oscillatorFreq[i] = 0.0f;
  }
  
  for (int i = 0; i < NUM_PHASE_DETECTORS; i++) {
    m_phaseData.phaseValues[i] = 0.0f;
    m_filterIndex[i] = 0;
    
    // Clear filter buffer
    for (int j = 0; j < 10; j++) {
      m_filterBuffer[i][j] = 0.0f;
    }
  }
}

bool PhaseDetector::begin() {
  // Configure ADC pins
  for (int i = 0; i < NUM_PHASE_DETECTORS; i++) {
    pinMode(m_adcPins[i], INPUT);
  }
  
  // Set ADC resolution
  analogReadResolution(12);  // 12-bit resolution (0-4095)
  
  return true;
}

void PhaseDetector::configure(const PhaseDetectorSettings settings[]) {
  // Copy settings
  for (int i = 0; i < NUM_PHASE_DETECTORS; i++) {
    m_settings[i] = settings[i];
  }
}

void PhaseDetector::samplePhases() {
  // Update timestamp
  m_phaseData.timestamp = millis();
  m_phaseData.sequenceNumber++;
  
  // Sample each phase detector
  for (int i = 0; i < NUM_PHASE_DETECTORS; i++) {
    // Read raw ADC value
    int rawADC = analogRead(m_adcPins[i]);
    
    // Convert to voltage (0-3.3V range with 12-bit ADC)
    float voltage = (rawADC / 4095.0f) * 3.3f;
    
    // Filter the reading
    float filteredValue = filterReading(i, voltage);
    
    // Convert to phase angle (0-360 degrees)
    float phaseAngle = convertToDegrees(filteredValue);
    
    // Store in phase data
    m_phaseData.phaseValues[i] = phaseAngle;
  }
}

float PhaseDetector::filterReading(uint8_t detector, float rawValue) {
  if (detector >= NUM_PHASE_DETECTORS) {
    return rawValue;
  }
  
  // Get filter level (how many samples to average)
  uint8_t filterLevel = m_settings[detector].filterLevel;
  if (filterLevel < 1) filterLevel = 1;
  if (filterLevel > 10) filterLevel = 10;
  
  // Add new value to buffer
  m_filterBuffer[detector][m_filterIndex[detector]] = rawValue;
  
  // Update buffer index
  m_filterIndex[detector] = (m_filterIndex[detector] + 1) % filterLevel;
  
  // Calculate average
  float sum = 0.0f;
  for (int i = 0; i < filterLevel; i++) {
    sum += m_filterBuffer[detector][i];
  }
  
  return sum / filterLevel;
}

float PhaseDetector::convertToDegrees(float rawValue) {
  // The phase detector outputs 0-3.3V corresponding to 0-180 degrees
  // Phase differences can be 0-360 degrees in a full cycle
  
  // First, scale to 0-1 range
  float normalized = rawValue / 3.3f;
  
  // Convert to degrees (0-360)
  // In practice, the actual mapping may need calibration
  // based on the specific phase detector circuit
  return normalized * 360.0f;
}

float PhaseDetector::getPhaseAngle(uint8_t detector) const {
  if (detector < NUM_PHASE_DETECTORS) {
    return m_phaseData.phaseValues[detector];
  }
  return 0.0f;
}

float PhaseDetector::getSynchronizationIndex() const {
  // Calculate an index of synchronization across all oscillators
  // This is a simplified metric based on how close phase differences
  // are to stable resonance states
  
  float synchronization = 0.0f;
  
  // Calculate synchronization for each phase detector
  for (int i = 0; i < NUM_PHASE_DETECTORS; i++) {
    float phase = m_phaseData.phaseValues[i];
    
    // Find closest resonance angle (could be 0, 90, 180, 270 degrees)
    float closestResonance = 90.0f * round(phase / 90.0f);
    
    // Calculate how close we are to resonance (0-1, where 1 is perfect resonance)
    float distance = fabs(phase - closestResonance);
    if (distance > 180.0f) distance = 360.0f - distance;
    
    // Convert to a 0-1 scale (45 degrees is max distance from resonance)
    float resonanceStrength = 1.0f - (distance / 45.0f);
    if (resonanceStrength < 0.0f) resonanceStrength = 0.0f;
    
    // Add to total synchronization
    synchronization += resonanceStrength;
  }
  
  // Average across all detectors
  return synchronization / NUM_PHASE_DETECTORS;
}
```

## Network Manager

### network_manager.h

```cpp
/*
 * Prime Resonance Computer
 * Network Manager
 * 
 * This module handles communication between nodes using ESP-NOW.
 */

#ifndef NETWORK_MANAGER_H
#define NETWORK_MANAGER_H

#include <Arduino.h>
#include <esp_now.h>
#include <WiFi.h>
#include "config.h"

// Maximum number of nodes in the network
#define MAX_NODES 16

class NetworkManager {
private:
  // Network configuration
  uint8_t m_nodeId;
  uint8_t m_coordinatorId;
  bool m_isCoordinator;
  
  // Node management
  struct NodeInfo {
    uint8_t id;
    uint8_t macAddress[6];
    uint32_t lastSeen;
    bool isActive;
    uint8_t errorCode;
  };
  
  NodeInfo m_nodes[MAX_NODES];
  uint8_t m_activeNodeCount;
  uint8_t m_totalNodeCount;
  
  // ESP-NOW peer info
  esp_now_peer_info_t m_peerInfo;
  
  // Message sequencing
  uint8_t m_txSequence;
  uint8_t m_rxSequence[MAX_NODES];
  
  // Connection state
  bool m_connected;
  uint32_t m_lastNetworkActivity;
  
  // Broadcast MAC address
  uint8_t m_broadcastMac[6] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};
  
  // Message buffer
  NetworkMessage m_messageBuffer[10];
  uint8_t m_messageBufferHead;
  uint8_t m_messageBufferTail;
  
  // Private methods
  void addMessageToBuffer(NetworkMessage &message);
  bool isMacEqual(const uint8_t *mac1, const uint8_t *mac2);
  
  // ESP-NOW callbacks
  static void onDataSent(const uint8_t *macAddr, esp_now_send_status_t status);
  static void onDataReceived(const uint8_t *macAddr, const uint8_t *data, int dataLen);
  
  // Pointer to this instance for callbacks
  static NetworkManager* s_instance;

public:
  NetworkManager();
  
  // Initialization
  bool begin(uint8_t nodeId);
  
  // Node discovery and management
  void discoverNodes();
  bool addNode(uint8_t nodeId, const uint8_t* macAddress);
  bool removeNode(uint8_t nodeId);
  void updateNodeLastSeen(uint8_t nodeId);
  void setNodeError(uint8_t nodeId, uint8_t errorCode);
  
  // Status information
  uint8_t getActiveNodeCount() { return m_activeNodeCount; }
  uint8_t getTotalNodeCount() { return m_totalNodeCount; }
  bool isConnected() { return m_connected; }
  
  // Network operations
  bool sendMessage(uint8_t targetNodeId, uint8_t messageType, const void* data, size_t dataSize);
  bool broadcastMessage(uint8_t messageType, const void* data, size_t dataSize);
  bool receiveMessage(NetworkMessage* message);
  
  // Communication helpers
  void broadcastDiscovery();
  void broadcastStatus();
  void broadcastCommand(uint8_t command);
  void broadcastConfiguration(SystemConfig* config);
  void sendStatus();
  void sendPhaseData(PhaseData* phaseData);
  void sendAlert(uint8_t alertCode);
  void sendError(uint8_t errorCode, const char* errorMessage);
  void sendCalibrationResult(bool success);
  void updateNodeStatus(NetworkMessage &message);
};

#endif // NETWORK_MANAGER_H
```

### network_manager.cpp

```cpp
/*
 * Prime Resonance Computer
 * Network Manager Implementation
 */

#include "network_manager.h"

// Static instance pointer for callbacks
NetworkManager* NetworkManager::s_instance = nullptr;

NetworkManager::NetworkManager() {
  // Initialize node information
  m_nodeId = 0;
  m_coordinatorId = COORDINATOR_NODE_ID;
  m_isCoordinator = false;
  m_connected = false;
  m_lastNetworkActivity = 0;
  m_activeNodeCount = 0;
  m_totalNodeCount = 0;
  
  // Initialize node array
  for (int i = 0; i < MAX_NODES; i++) {
    m_nodes[i].id = 0;
    m_nodes[i].lastSeen = 0;
    m_nodes[i].isActive = false;
    m_nodes[i].errorCode = 0;
    memset(m_nodes[i].macAddress, 0, 6);
  }
  
  // Initialize message sequences
  m_txSequence = 0;
  for (int i = 0; i < MAX_NODES; i++) {
    m_rxSequence[i] = 0;
  }
  
  // Initialize message buffer
  m_messageBufferHead = 0;
  m_messageBufferTail = 0;
}

bool NetworkManager::begin(uint8_t nodeId) {
  // Store node ID
  m_nodeId = nodeId;
  m_isCoordinator = (nodeId == m_coordinatorId);
  
  // Set static instance for callbacks
  s_instance = this;
  
  // Initialize WiFi in station mode
  WiFi.mode(WIFI_STA);
  WiFi.disconnect();
  
  // Initialize ESP-NOW
  if (esp_now_init() != ESP_OK) {
    Serial.println("Error initializing ESP-NOW");
    return false;
  }
  
  // Set ESP-NOW callbacks
  esp_now_register_send_cb(onDataSent);
  esp_now_register_recv_cb(onDataReceived);
  
  // Add broadcast peer
  memset(&m_peerInfo, 0, sizeof(m_peerInfo));
  memcpy(m_peerInfo.peer_addr, m_broadcastMac, 6);
  m_peerInfo.channel = 0;
  m_peerInfo.encrypt = false;
  
  if (esp_now_add_peer(&m_peerInfo) != ESP_OK) {
    Serial.println("Failed to add broadcast peer");
    return false;
  }
  
  Serial.println("Network manager initialized");
  Serial.print("MAC Address: ");
  Serial.println(WiFi.macAddress());
  
  // If this is the coordinator, initialize with self as first node
  if (m_isCoordinator) {
    uint8_t mac[6];
    WiFi.macAddress(mac);
    addNode(m_nodeId, mac);
  }
  
  return true;
}

void NetworkManager::discoverNodes() {
  if (!m_isCoordinator) {
    return;  // Only coordinator can discover nodes
  }
  
  Serial.println("Starting node discovery");
  
  // Broadcast discovery request
  uint8_t discoveryData = 1;  // 1 = discovery request
  broadcastMessage(MSG_COMMAND, &discoveryData, 1);
  
  // Wait for responses
  delay(1000);
  
  // Print discovered nodes
  Serial.print("Discovered ");
  Serial.print(m_activeNodeCount);
  Serial.println(" nodes:");
  
  for (int i = 0; i < MAX_NODES; i++) {
    if (m_nodes[i].isActive) {
      Serial.print("Node ");
      Serial.print(m_nodes[i].id);
      Serial.print(": ");
      for (int j = 0; j < 6; j++) {
        Serial.print(m_nodes[i].macAddress[j], HEX);
        if (j < 5) Serial.print(":");
      }
      Serial.println();
    }
  }
}

bool NetworkManager::addNode(uint8_t nodeId, const uint8_t* macAddress) {
  if (nodeId >= MAX_NODES) {
    return false;
  }
  
  // Check if node already exists
  for (int i = 0; i < MAX_NODES; i++) {
    if (m_nodes[i].isActive && m_nodes[i].id == nodeId) {
      // Update MAC if different
      if (!isMacEqual(m_nodes[i].macAddress, macAddress)) {
        memcpy(m_nodes[i].macAddress, macAddress, 6);
      }
      
      // Update last seen
      m_nodes[i].lastSeen = millis();
      return true;
    }
  }
  
  // Find empty slot
  int emptySlot = -1;
  for (int i = 0; i < MAX_NODES; i++) {
    if (!m_nodes[i].isActive) {
      emptySlot = i;
      break;
    }
  }
  
  if (emptySlot < 0) {
    Serial.println("No space for new node");
    return false;
  }
  
  // Add the node
  m_nodes[emptySlot].id = nodeId;
  memcpy(m_nodes[emptySlot].macAddress, macAddress, 6);
  m_nodes[emptySlot].lastSeen = millis();
  m_nodes[emptySlot].isActive = true;
  m_nodes[emptySlot].errorCode = 0;
  
  // Add as ESP-NOW peer
  memcpy(m_peerInfo.peer_addr, macAddress, 6);
  m_peerInfo.channel = 0;
  m_peerInfo.encrypt = false;
  
  if (esp_now_add_peer(&m_peerInfo) != ESP_OK) {
    Serial.println("Failed to add peer");
    return false;
  }
  
  // Update counts
  m_activeNodeCount++;
  m_totalNodeCount = max(m_totalNodeCount, m_activeNodeCount);
  
  Serial.print("Added node ");
  Serial.println(nodeId);
  
  return true;
}

bool NetworkManager::removeNode(uint8_t nodeId) {
  if (nodeId >= MAX_NODES) {
    return false;
  }
  
  // Find node
  for (int i = 0; i < MAX_NODES; i++) {
    if (m_nodes[i].isActive && m_nodes[i].id == nodeId) {
      // Remove ESP-NOW peer
      esp_now_del_peer(m_nodes[i].macAddress);
      
      // Deactivate node
      m_nodes[i].isActive = false;
      
      // Update counts
      m_activeNodeCount--;
      
      Serial.print("Removed node ");
      Serial.println(nodeId);
      
      return true;
    }
  }
  
  return false;  // Node not found
}

void NetworkManager::updateNodeLastSeen(uint8_t nodeId) {
  for (int i = 0; i < MAX_NODES; i++) {
    if (m_nodes[i].isActive && m_nodes[i].id == nodeId) {
      m_nodes[i].lastSeen = millis();
      break;
    }
  }
}

void NetworkManager::setNodeError(uint8_t nodeId, uint8_t errorCode) {
  for (int i = 0; i < MAX_NODES; i++) {
    if (m_nodes[i].isActive && m_nodes[i].id == nodeId) {
      m_nodes[i].errorCode = errorCode;
      break;
    }
  }
}

bool NetworkManager::sendMessage(uint8_t targetNodeId, uint8_t messageType, const void* data, size_t dataSize) {
  if (dataSize > 250) {
    Serial.println("Message too large");
    return false;
  }
  
  // Find target node
  int targetIndex = -1;
  for (int i = 0; i < MAX_NODES; i++) {
    if (m_nodes[i].isActive && m_nodes[i].id == targetNodeId) {
      targetIndex = i;
      break;
    }
  }
  
  if (targetIndex < 0) {
    Serial.print("Target node not found: ");
    Serial.println(targetNodeId);
    return false;
  }
  
  // Prepare message
  NetworkMessage message;
  message.messageType = messageType;
  message.sourceId = m_nodeId;
  message.targetId = targetNodeId;
  message.sequenceNumber = m_txSequence++;
  message.payloadLength = dataSize;
  
  if (data != nullptr && dataSize > 0) {
    memcpy(message.payload, data, dataSize);
  }
  
  // Send with ESP-NOW
  esp_err_t result = esp_now_send(m_nodes[targetIndex].macAddress, (uint8_t*)&message, 
                                  sizeof(message) - 250 + dataSize);
  
  if (result != ESP_OK) {
    Serial.println("Failed to send message");
    return false;
  }
  
  return true;
}

bool NetworkManager::broadcastMessage(uint8_t messageType, const void* data, size_t dataSize) {
  if (dataSize > 250) {
    Serial.println("Message too large");
    return false;
  }
  
  // Prepare message
  NetworkMessage message;
  message.messageType = messageType;
  message.sourceId = m_nodeId;
  message.targetId = 0;  // 0 = broadcast
  message.sequenceNumber = m_txSequence++;
  message.payloadLength = dataSize;
  
  if (data != nullptr && dataSize > 0) {
    memcpy(message.payload, data, dataSize);
  }
  
  // Send with ESP-NOW
  esp_err_t result = esp_now_send(m_broadcastMac, (uint8_t*)&message, 
                                  sizeof(message) - 250 + dataSize);
  
  if (result != ESP_OK) {
    Serial.println("Failed to send broadcast message");
    return false;
  }
  
  return true;
}

bool NetworkManager::receiveMessage(NetworkMessage* message) {
  // Check if buffer is empty
  if (m_messageBufferHead == m_messageBufferTail) {
    return false;  // No messages
  }
  
  // Get message from buffer
  *message = m_messageBuffer[m_messageBufferTail];
  
  // Update buffer tail
  m_messageBufferTail = (m_messageBufferTail + 1) % 10;
  
  return true;
}

void NetworkManager::broadcastDiscovery() {
  // For compute nodes to announce themselves to coordinator
  uint8_t discoveryData[7];
  discoveryData[0] = 2;  // 2 = discovery announcement
  
  // Add MAC address
  WiFi.macAddress(&discoveryData[1]);
  
  broadcastMessage(MSG_COMMAND, discoveryData, 7);
}

void NetworkManager::broadcastStatus() {
  if (!m_isCoordinator) {
    return;  // Only coordinator broadcasts status
  }
  
  // Prepare status data
  uint8_t statusData[2];
  statusData[0] = m_activeNodeCount;
  statusData[1] = m_totalNodeCount;
  
  broadcastMessage(MSG_STATUS_UPDATE, statusData, 2);
}

void NetworkManager::broadcastCommand(uint8_t command) {
  if (!m_isCoordinator) {
    return;  // Only coordinator broadcasts commands
  }
  
  broadcastMessage(MSG_COMMAND, &command, 1);
}

void NetworkManager::broadcastConfiguration(SystemConfig* config) {
  if (!m_isCoordinator) {
    return;  // Only coordinator broadcasts configuration
  }
  
  broadcastMessage(MSG_SYSTEM_CONFIG, config, sizeof(SystemConfig));
}

void NetworkManager::sendStatus() {
  // For compute nodes to send status to coordinator
  if (m_isCoordinator) {
    return;  // Coordinator doesn't send status
  }
  
  // Prepare status data
  uint8_t statusData[4];
  statusData[0] = 1;  // Node is alive
  statusData[1] = 0;  // Error code (0 = no error)
  statusData[2] = analogRead(PHASE_DETECTOR_1_PIN) / 16;  // Simple diagnostic value
  statusData[3] = analogRead(PHASE_DETECTOR_2_PIN) / 16;  // Simple diagnostic value
  
  sendMessage(m_coordinatorId, MSG_STATUS_UPDATE, statusData, 4);
}

void NetworkManager::sendPhaseData(PhaseData* phaseData) {
  if (m_isCoordinator) {
    return;  // Coordinator doesn't send phase data
  }
  
  // Set node ID in phase data
  phaseData->nodeId = m_nodeId;
  
  sendMessage(m_coordinatorId, MSG_PHASE_DATA, phaseData, sizeof(PhaseData));
}

void NetworkManager::sendAlert(uint8_t alertCode) {
  if (m_isCoordinator) {
    return;  // Coordinator doesn't send alerts
  }
  
  sendMessage(m_coordinatorId, MSG_COMMAND, &alertCode, 1);
}

void NetworkManager::sendError(uint8_t errorCode, const char* errorMessage) {
  if (m_isCoordinator) {
    return;  // Coordinator logs errors locally
  }
  
  // Prepare error data
  uint8_t errorData[32];
  errorData[0] = errorCode;
  
  // Copy error message (max 31 bytes)
  size_t msgLen = strlen(errorMessage);
  if (msgLen > 31) msgLen = 31;
  memcpy(&errorData[1], errorMessage, msgLen);
  
  sendMessage(m_coordinatorId, MSG_ERROR_REPORT, errorData, msgLen + 1);
}

void NetworkManager::sendCalibrationResult(bool success) {
  if (m_isCoordinator) {
    return;  // Coordinator doesn't send calibration results
  }
  
  uint8_t resultData = success ? 1 : 0;
  sendMessage(m_coordinatorId, MSG_CALIBRATION_RESULT, &resultData, 1);
}

void NetworkManager::updateNodeStatus(NetworkMessage &message) {
  // Update node status based on received message
  uint8_t nodeId = message.sourceId;
  
  // Update last seen timestamp
  updateNodeLastSeen(nodeId);
  
  // For coordinator: check if this is a new node
  if (m_isCoordinator) {
    bool nodeExists = false;
    
    for (int i = 0; i < MAX_NODES; i++) {
      if (m_nodes[i].isActive && m_nodes[i].id == nodeId) {
        nodeExists = true;
        break;
      }
    }
    
    if (!nodeExists && message.messageType == MSG_COMMAND && 
        message.payloadLength >= 7 && message.payload[0] == 2) {
      // This is a discovery announcement from a new node
      // Extract MAC address from payload
      addNode(nodeId, &message.payload[1]);
      
      // Send configuration to new node
      SystemConfig* config = (SystemConfig*)malloc(sizeof(SystemConfig));
      if (config) {
        // Fill with current configuration
        // This should be replaced with actual configuration data
        memset(config, 0, sizeof(SystemConfig));
        config->version = CONFIG_VERSION;
        
        // Sample oscillator settings
        config->oscillatorSettings[0].targetFrequency = 2.0f;
        config->oscillatorSettings[1].targetFrequency = 3.0f;
        config->oscillatorSettings[2].targetFrequency = 5.0f;
        
        sendMessage(nodeId, MSG_SYSTEM_CONFIG, config, sizeof(SystemConfig));
        free(config);
      }
    }
  }
}

void NetworkManager::addMessageToBuffer(NetworkMessage &message) {
  // Ignore messages from self
  if (message.sourceId == m_nodeId) {
    return;
  }
  
  // Check if it's a duplicate (we've already seen this sequence number)
  if (message.sourceId < MAX_NODES) {
    if (message.sequenceNumber <= m_rxSequence[message.sourceId]) {
      // Duplicate message, ignore
      return;
    }
    
    // Update sequence number
    m_rxSequence[message.sourceId] = message.sequenceNumber;
  }
  
  // Check if buffer is full
  uint8_t nextHead = (m_messageBufferHead + 1) % 10;
  if (nextHead == m_messageBufferTail) {
    Serial.println("Message buffer full, dropping oldest message");
    m_messageBufferTail = (m_messageBufferTail + 1) % 10;
  }
  
  // Add to buffer
  m_messageBuffer[m_messageBufferHead] = message;
  m_messageBufferHead = nextHead;
  
  // Update connection state
  m_connected = true;
  m_lastNetworkActivity = millis();
}

bool NetworkManager::isMacEqual(const uint8_t *mac1, const uint8_t *mac2) {
  for (int i = 0; i < 6; i++) {
    if (mac1[i] != mac2[i]) {
      return false;
    }
  }
  return true;
}

// Static callback for ESP-NOW data sent
void NetworkManager::onDataSent(const uint8_t *macAddr, esp_now_send_status_t status) {
  if (s_instance) {
    if (status != ESP_OK) {
      Serial.println("Failed to deliver message");
    }
  }
}

// Static callback for ESP-NOW data received
void NetworkManager::onDataReceived(const uint8_t *macAddr, const uint8_t *data, int dataLen) {
  if (s_instance) {
    // Check if data size is valid
    if (dataLen >= 4) {  // Minimum message size
      // Copy to a message structure
      NetworkMessage message;
      size_t copySize = min((size_t)dataLen, sizeof(NetworkMessage));
      memcpy(&message, data, copySize);
      
      // Add to buffer
      s_instance->addMessageToBuffer(message);
    }
  }
}
```

## Prime Resonance Algorithms

### prime_resonance.h

```cpp
/*
 * Prime Resonance Computer
 * Prime Resonance Detection System
 * 
 * This module implements the core prime resonance algorithms.
 */

#ifndef PRIME_RESONANCE_H
#define PRIME_RESONANCE_H

#include <Arduino.h>
#include <vector>
#include "config.h"

// Maximum prime number to detect
#define MAX_PRIME_VALUE 100

class PrimeResonance {
private:
  // Configuration
  ResonanceSettings m_settings;
  
  // Current state
  float m_resonanceStrength;
  float m_currentEntropy;
  uint32_t m_iterationCount;
  bool m_resultAvailable;
  
  // Remote node data
  struct NodePhaseData {
    PhaseData data;
    uint32_t timestamp;
    bool active;
  };
  
  NodePhaseData m_nodeData[MAX_NODES];
  
  // Results
  PrimeResult m_result;
  
  // Prime pattern database
  float m_primePatterns[MAX_PRIME_VALUE][NUM_PHASE_DETECTORS];
  bool m_patternsInitialized;
  
  // Private methods
  void initializePrimePatterns();
  float calculateEntropy();
  float comparePatterns(float* pattern1, float* pattern2);
  void detectPrimes();
  void updateStateFromPhaseData(PhaseData &phaseData);
  void updateStateFromNetworkData();

public:
  PrimeResonance();
  
  // Initialization
  bool begin();
  void configure(const ResonanceSettings &settings);
  
  // Data processing
  void processPhaseData(const PhaseData &phaseData);
  void processRemotePhaseData(const PhaseData &phaseData, uint8_t nodeId);
  void update();
  
  // Results
  float getResonanceStrength() const { return m_resonanceStrength; }
  float getCurrentEntropy() const { return m_currentEntropy; }
  bool resultAvailable() const { return m_resultAvailable; }
  PrimeResult getResult();
  std::vector<uint8_t> getDetectedPrimes() const;
};

#endif // PRIME_RESONANCE_H
```

### prime_resonance.cpp

```cpp
/*
 * Prime Resonance Computer
 * Prime Resonance Detection System Implementation
 */

#include "prime_resonance.h"

PrimeResonance::PrimeResonance() {
  // Initialize with default settings
  m_settings.threshold = DEFAULT_RESONANCE_THRESHOLD;
  m_settings.minIterations = DEFAULT_MIN_ITERATIONS;
  m_settings.entropyThreshold = DEFAULT_ENTROPY_THRESHOLD;
  
  // Initialize state
  m_resonanceStrength = 0.0f;
  m_currentEntropy = 1.0f;  // Start with maximum entropy
  m_iterationCount = 0;
  m_resultAvailable = false;
  m_patternsInitialized = false;
  
  // Initialize node data
  for (int i = 0; i < MAX_NODES; i++) {
    m_nodeData[i].active = false;
    m_nodeData[i].timestamp = 0;
  }
  
  // Initialize result
  m_result.primeCount = 0;
  for (int i = 0; i < 16; i++) {
    m_result.primes[i] = 0;
  }
  m_result.confidence = 0.0f;
  m_result.computeTime = 0;
}

bool PrimeResonance::begin() {
  // Initialize prime patterns
  initializePrimePatterns();
  
  return true;
}

void PrimeResonance::configure(const ResonanceSettings &settings) {
  m_settings = settings;
}

void PrimeResonance::initializePrimePatterns() {
  if (m_patternsInitialized) {
    return;
  }
  
  // This function initializes the expected phase patterns for each prime number
  // These patterns represent the theoretical phase relationships when 
  // a system is resonating at a frequency related to a prime number
  
  // In a real implementation, these would be derived from mathematical models
  // For this example, we'll use simplified patterns based on prime properties
  
  for (int prime = 2; prime < MAX_PRIME_VALUE; prime++) {
    // Check if this number is prime
    bool isPrime = true;
    for (int divisor = 2; divisor * divisor <= prime; divisor++) {
      if (prime % divisor == 0) {
        isPrime = false;
        break;
      }
    }
    
    if (isPrime) {
      // Create a distinctive phase pattern for this prime
      for (int detector = 0; detector < NUM_PHASE_DETECTORS; detector++) {
        // This is a simplified pattern generation
        // In a real system, this would be based on actual resonance physics
        
        // Each prime maps to a specific phase relationship pattern
        // We'll use mathematical functions of prime to generate patterns
        float patternValue = 0.0f;
        
        switch (detector) {
          case 0:  // 2Hz-3Hz detector
            patternValue = fmod((prime * 12.5f), 360.0f);
            break;
          case 1:  // 2Hz-5Hz detector
            patternValue = fmod((prime * 37.2f), 360.0f);
            break;
          case 2:  // 3Hz-5Hz detector
            patternValue = fmod((prime * 58.3f), 360.0f);
            break;
        }
        
        m_primePatterns[prime][detector] = patternValue;
      }
    } else {
      // Non-prime numbers don't have resonance patterns
      // We'll use NaN to indicate this
      for (int detector = 0; detector < NUM_PHASE_DETECTORS; detector++) {
        m_primePatterns[prime][detector] = NAN;
      }
    }
  }
  
  m_patternsInitialized = true;
}

void PrimeResonance::processPhaseData(const PhaseData &phaseData) {
  // Process the local phase data
  updateStateFromPhaseData(const_cast<PhaseData&>(phaseData));
  
  // Increment iteration count
  m_iterationCount++;
  
  // Check for resonance conditions
  if (m_iterationCount >= m_settings.minIterations && 
      m_resonanceStrength >= m_settings.threshold &&
      m_currentEntropy <= m_settings.entropyThreshold) {
    
    // We have a stable resonance pattern, detect primes
    detectPrimes();
  }
}

void PrimeResonance::processRemotePhaseData(const PhaseData &phaseData, uint8_t nodeId) {
  if (nodeId >= MAX_NODES) {
    return;
  }
  
  // Store remote node data
  m_nodeData[nodeId].data = phaseData;
  m_nodeData[nodeId].timestamp = millis();
  m_nodeData[nodeId].active = true;
  
  // Update system state with all node data
  updateStateFromNetworkData();
}

void PrimeResonance::update() {
  // This is called periodically to update the system state
  // We'll check if any results are available
  
  // Check if any node data has expired
  uint32_t currentTime = millis();
  for (int i = 0; i < MAX_NODES; i++) {
    if (m_nodeData[i].active) {
      // Check if data is older than 5 seconds
      if (currentTime - m_nodeData[i].timestamp > 5000) {
        // Data expired
        m_nodeData[i].active = false;
      }
    }
  }
}

void PrimeResonance::updateStateFromPhaseData(PhaseData &phaseData) {
  // Update resonance strength based on current phase data
  
  // In a real implementation, this would involve complex analysis of
  // how the phase relationships match expected patterns for various primes
  
  // For this example, we'll use a simplified approach
  
  // Calculate entropy
  m_currentEntropy = calculateEntropy();
  
  // The resonance strength is inversely related to entropy
  // Lower entropy means stronger resonance
  m_resonanceStrength = 1.0f - m_currentEntropy;
  
  // Clamp to valid range
  if (m_resonanceStrength < 0.0f) m_resonanceStrength = 0.0f;
  if (m_resonanceStrength > 1.0f) m_resonanceStrength = 1.0f;
}

void PrimeResonance::updateStateFromNetworkData() {
  // Combine phase data from all active nodes
  
  // In a real implementation, this would involve sophisticated
  // data fusion algorithms to combine phase data from multiple sources
  
  // For this example, we'll use a simple averaging approach
  
  // Count active nodes
  int activeNodeCount = 0;
  for (int i = 0; i < MAX_NODES; i++) {
    if (m_nodeData[i].active) {
      activeNodeCount++;
    }
  }
  
  if (activeNodeCount == 0) {
    return;  // No active nodes
  }
  
  // Average phase values across nodes
  PhaseData averageData;
  memset(&averageData, 0, sizeof(PhaseData));
  
  for (int i = 0; i < MAX_NODES; i++) {
    if (m_nodeData[i].active) {
      // Add oscillator frequencies
      for (int j = 0; j < NUM_OSCILLATORS; j++) {
        averageData.oscillatorFreq[j] += m_nodeData[i].data.oscillatorFreq[j];
      }
      
      // Add phase values
      for (int j = 0; j < NUM_PHASE_DETECTORS; j++) {
        // Phase averaging needs to handle wraparound
        float phase = m_nodeData[i].data.phaseValues[j];
        
        // Convert to radians for vector averaging
        float phaseRad = phase * (PI / 180.0f);
        
        // Convert to vector components
        float x = cos(phaseRad);
        float y = sin(phaseRad);
        
        // Add to average components
        averageData.phaseValues[j] += x;
        // Use the extra space in the array to store the y component
        if (j + NUM_PHASE_DETECTORS < NUM_PHASE_DETECTORS * 2) {
          averageData.phaseValues[j + NUM_PHASE_DETECTORS] += y;
        }
      }
    }
  }
  
  // Compute final averages
  for (int j = 0; j < NUM_OSCILLATORS; j++) {
    averageData.oscillatorFreq[j] /= activeNodeCount;
  }
  
  for (int j = 0; j < NUM_PHASE_DETECTORS; j++) {
    // Average x and y components
    float x = averageData.phaseValues[j] / activeNodeCount;
    float y = 0.0f;
    if (j + NUM_PHASE_DETECTORS < NUM_PHASE_DETECTORS * 2) {
      y = averageData.phaseValues[j + NUM_PHASE_DETECTORS] / activeNodeCount;
    }
    
    // Convert back to phase angle
    float phaseRad = atan2(y, x);
    float phase = phaseRad * (180.0f / PI);
    
    // Normalize to 0-360 range
    if (phase < 0) {
      phase += 360.0f;
    }
    
    averageData.phaseValues[j] = phase;
  }
  
  // Update system state based on average data
  updateStateFromPhaseData(averageData);
}

float PrimeResonance::calculateEntropy() {
  // Calculate the entropy of the current phase distribution
  // Entropy is a measure of disorder in the system
  // Lower entropy indicates more organized, resonant states
  
  // In a real implementation, this would involve sophisticated
  // information theory calculations based on the phase distributions
  
  // For this example, we'll use a simplified approach based on
  // how consistent the phase relationships are over time
  
  // Simulate entropy decreasing over time to represent system stabilizing
  // This would be replaced with actual entropy calculation in a real system
  float simulatedEntropy = 1.0f - (float)m_iterationCount / (float)(m_settings.minIterations * 2);
  
  // Ensure entropy stays in valid range
  if (simulatedEntropy < 0.0f) simulatedEntropy = 0.0f;
  if (simulatedEntropy > 1.0f) simulatedEntropy = 1.0f;
  
  return simulatedEntropy;
}

float PrimeResonance::comparePatterns(float* pattern1, float* pattern2) {
  // Compare two phase patterns and return a similarity score (0-1)
  // 1 = perfect match, 0 = no similarity
  
  float similarity = 0.0f;
  
  for (int i = 0; i < NUM_PHASE_DETECTORS; i++) {
    // Calculate phase difference, accounting for wraparound
    float diff = fabs(pattern1[i] - pattern2[i]);
    if (diff > 180.0f) {
      diff = 360.0f - diff;
    }
    
    // Convert to similarity (0-1)
    // 0° difference = 1.0 similarity
    // 180° difference = 0.0 similarity
    float phaseSimilarity = 1.0f - (diff / 180.0f);
    
    // Add to total similarity
    similarity += phaseSimilarity;
  }
  
  // Average across all detectors
  return similarity / NUM_PHASE_DETECTORS;
}

void PrimeResonance::detectPrimes() {
  // Compare current phase pattern against known prime patterns
  
  // Get current phase values
  float currentPattern[NUM_PHASE_DETECTORS];
  PhaseData localData = m_nodeData[0].data;  // Use local node's data
  for (int i = 0; i < NUM_PHASE_DETECTORS; i++) {
    currentPattern[i] = localData.phaseValues[i];
  }
  
  // Find matches with prime patterns
  std::vector<uint8_t> primeMatches;
  std::vector<float> matchStrengths;
  
  for (int prime = 2; prime < MAX_PRIME_VALUE; prime++) {
    // Check if this is a prime number (pattern will have valid values)
    if (!isnan(m_primePatterns[prime][0])) {
      // Compare current pattern with this prime's pattern
      float matchStrength = comparePatterns(currentPattern, m_primePatterns[prime]);
      
      // If match exceeds threshold, add to results
      if (matchStrength > m_settings.threshold) {
        primeMatches.push_back(prime);
        matchStrengths.push_back(matchStrength);
      }
    }
  }
  
  // Store results
  m_result.primeCount = min((size_t)16, primeMatches.size());
  m_result.confidence = 0.0f;
  
  for (int i = 0; i < m_result.primeCount; i++) {
    m_result.primes[i] = primeMatches[i];
    m_result.confidence += matchStrengths[i];
  }
  
  if (m_result.primeCount > 0) {
    m_result.confidence /= m_result.primeCount;
  }
  
  m_result.computeTime = m_iterationCount;
  m_resultAvailable = true;
}

PrimeResult PrimeResonance::getResult() {
  m_resultAvailable = false;  // Mark result as consumed
  return m_result;
}

std::vector<uint8_t> PrimeResonance::getDetectedPrimes() const {
  std::vector<uint8_t> primes;
  
  for (int i = 0; i < m_result.primeCount; i++) {
    primes.push_back(m_result.primes[i]);
  }
  
  return primes;
}
```

## User Interface

### user_interface.h

```cpp
/*
 * Prime Resonance Computer
 * User Interface Module
 * 
 * This module handles the OLED display and user input controls.
 */

#ifndef USER_INTERFACE_H
#define USER_INTERFACE_H

#include <Arduino.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include "config.h"

// Screen pages
#define SCREEN_STATUS 0
#define SCREEN_PHASE 1
#define SCREEN_OSCILLATOR 2
#define SCREEN_NETWORK 3
#define SCREEN_MENU 4

class UserInterface {
private:
  // Display
  Adafruit_SSD1306 m_display;
  
  // User input
  uint8_t m_buttonState[3];
  uint8_t m_buttonPins[3] = {BUTTON_1_PIN, BUTTON_2_PIN, BUTTON_3_PIN};
  uint8_t m_lastButtonState[3];
  unsigned long m_lastButtonTime[3];
  
  // Rotary encoder
  int8_t m_encoderPosition;
  uint8_t m_encoderLastState;
  
  // UI state
  uint8_t m_currentScreen;
  bool m_menuActive;
  uint8_t m_menuPosition;
  uint8_t m_menuItems;
  bool m_messageActive;
  unsigned long m_messageTimeout;
  
  // Interface strings
  char m_menuText[5][32];
  char m_messageText[64];
  
  // Stream for text output
  //Print* m_outputStream;
  
  // Private methods
  void drawStatusScreen(const SystemStatus &status);
  void drawPhaseScreen(const SystemStatus &status);
  void drawOscillatorScreen(const SystemStatus &status);
  void drawNetworkScreen(const SystemStatus &status);
  void drawMenuScreen();
  
  void checkButtons();
  void checkEncoder();
  void scrollMenu(int8_t direction);

public:
  UserInterface();
  
  // Initialization
  bool begin();
  
  // Display operations
  void updateStatus(const SystemStatus &status);
  void showMessage(const char* message, int timeout = 0);
  void showError(uint8_t errorCode, const char* errorMessage);
  void showPrimeResult(const PrimeResult &result);
  
  // User input
  UserInput getInput();
  void handleInput(const UserInput &input);
  
  // Menu management
  void setMenuItems(const char* items[], uint8_t count);
  uint8_t getMenuSelection();
};

#endif // USER_INTERFACE_H
```

### user_interface.cpp

```cpp
/*
 * Prime Resonance Computer
 * User Interface Implementation
 */

#include "user_interface.h"

UserInterface::UserInterface()
  : m_display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET_PIN)
{
  // Initialize state
  m_currentScreen = SCREEN_STATUS;
  m_menuActive = false;
  m_menuPosition = 0;
  m_menuItems = 0;
  m_messageActive = false;
  m_messageTimeout = 0;
  
  // Initialize encoder state
  m_encoderPosition = 0;
  m_encoderLastState = 0;
  
  // Initialize button state
  for (int i = 0; i < 3; i++) {
    m_buttonState[i] = HIGH;  // Buttons are active LOW
    m_lastButtonState[i] = HIGH;
    m_lastButtonTime[i] = 0;
  }
  
  // Initialize menu text
  memset(m_menuText, 0, sizeof(m_menuText));
  memset(m_messageText, 0, sizeof(m_messageText));
}

bool UserInterface::begin() {
  // Initialize display
  if (!m_display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
    Serial.println("SSD1306 allocation failed");
    return false;
  }
  
  // Setup display
  m_display.clearDisplay();
  m_display.setTextSize(1);
  m_display.setTextColor(SSD1306_WHITE);
  m_display.setCursor(0, 0);
  m_display.println("Prime Resonance Computer");
  m_display.println("Initializing...");
  m_display.display();
  
  // Configure buttons
  for (int i = 0; i < 3; i++) {
    pinMode(m_buttonPins[i], INPUT_PULLUP);
  }
  
  // Configure rotary encoder
  pinMode(ROTARY_A_PIN, INPUT_PULLUP);
  pinMode(ROTARY_B_PIN, INPUT_PULLUP);
  pinMode(ROTARY_BUTTON_PIN, INPUT_PULLUP);
  m_encoderLastState = digitalRead(ROTARY_A_PIN);
  
  return true;
}

void UserInterface::updateStatus(const SystemStatus &status) {
  // Update appropriate screen
  switch (m_currentScreen) {
    case SCREEN_STATUS:
      drawStatusScreen(status);
      break;
    case SCREEN_PHASE:
      drawPhaseScreen(status);
      break;
    case SCREEN_OSCILLATOR:
      drawOscillatorScreen(status);
      break;
    case SCREEN_NETWORK:
      drawNetworkScreen(status);
      break;
    case SCREEN_MENU:
      drawMenuScreen();
      break;
  }
}

void UserInterface::showMessage(const char* message, int timeout) {
  // Display a temporary message
  m_display.clearDisplay();
  m_display.setTextSize(1);
  m_display.setTextColor(SSD1306_WHITE);
  m_display.setCursor(0, 0);
  m_display.println("Message:");
  m_display.println();
  m_display.println(message);
  m_display.display();
  
  // If timeout is specified, message will disappear after timeout
  if (timeout > 0) {
    m_messageActive = true;
    m_messageTimeout = millis() + timeout;
    strncpy(m_messageText, message, sizeof(m_messageText) - 1);
    m_messageText[sizeof(m_messageText) - 1] = '\0';
  }
}

void UserInterface::showError(uint8_t errorCode, const char* errorMessage) {
  // Display an error message
  m_display.clearDisplay();
  m_display.setTextSize(1);
  m_display.setTextColor(SSD1306_WHITE);
  m_display.setCursor(0, 0);
  m_display.println("ERROR");
  m_display.print("Code: ");
  m_display.println(errorCode);
  m_display.println();
  m_display.println(errorMessage);
  m_display.display();
  
  // Error messages stay until cleared
  m_messageActive = true;
  m_messageTimeout = 0;  // No timeout
  
  // Store message
  snprintf(m_messageText, sizeof(m_messageText), "ERR %d: %s", errorCode, errorMessage);
}

void UserInterface::showPrimeResult(const PrimeResult &result) {
  // Display prime detection results
  m_display.clearDisplay();
  m_display.setTextSize(1);
  m_display.setTextColor(SSD1306_WHITE);
  m_display.setCursor(0, 0);
  m_display.println("Prime Result");
  
  // Show detected primes
  m_display.print("Primes: ");
  for (int i = 0; i < result.primeCount && i < 8; i++) {
    m_display.print(result.primes[i]);
    m_display.print(" ");
  }
  
  m_display.println();
  
  // Show more primes on next line if needed
  if (result.primeCount > 8) {
    m_display.print("       ");
    for (int i = 8; i < result.primeCount && i < 16; i++) {
      m_display.print(result.primes[i]);
      m_display.print(" ");
    }
    m_display.println();
  }
  
  // Show confidence and computation time
  m_display.print("Confidence: ");
  m_display.print(result.confidence * 100.0f, 1);
  m_display.println("%");
  
  m_display.print("Compute time: ");
  m_display.print(result.computeTime);
  m_display.println(" iter");
  
  m_display.display();
  
  // Display for a while
  m_messageActive = true;
  m_messageTimeout = millis() + 5000;  // 5 second timeout
}

UserInput UserInterface::getInput() {
  UserInput input;
  input.type = INPUT_NONE;
  input.buttonId = 0;
  input.encoderDelta = 0;
  input.command = 0;
  
  // Check for message timeout
  if (m_messageActive && m_messageTimeout > 0 && millis() > m_messageTimeout) {
    m_messageActive = false;
    return input;  // Clear message, but don't process other inputs
  }
  
  // Check buttons
  checkButtons();
  
  // Check encoder
  checkEncoder();
  
  // Process button presses
  for (int i = 0; i < 3; i++) {
    if (m_buttonState[i] == LOW && m_lastButtonState[i] == HIGH) {
      // Button press detected
      input.type = INPUT_BUTTON;
      input.buttonId = i + 1;  // Buttons are 1-based
      
      // Clear message if active
      if (m_messageActive) {
        m_messageActive = false;
        return input;  // Clear message, but don't process the press
      }
      
      // Process based on screen and button
      if (m_currentScreen == SCREEN_MENU) {
        if (i == 0) {
          // Select current menu item
          input.command = m_menuPosition + 1;
        } else if (i == 1) {
          // Next menu item
          scrollMenu(1);
        } else if (i == 2) {
          // Back to status screen
          m_currentScreen = SCREEN_STATUS;
        }
      } else {
        // On other screens
        if (i == 0) {
          // Switch to next screen
          m_currentScreen = (m_currentScreen + 1) % 5;  // Cycle through screens
        } else if (i == 1) {
          // Special action for screen
          if (m_currentScreen == SCREEN_STATUS) {
            // Start/stop resonance detection
            input.command = CMD_START_DETECTION;
          } else if (m_currentScreen == SCREEN_OSCILLATOR) {
            // Start calibration
            input.command = CMD_CALIBRATE;
          }
        } else if (i == 2) {
          // Show menu
          m_currentScreen = SCREEN_MENU;
          m_menuPosition = 0;
        }
      }
      
      break;  // Only process one button at a time
    }
    
    // Update last button state
    m_lastButtonState[i] = m_buttonState[i];
  }
  
  // Process encoder movement
  if (m_encoderPosition != 0) {
    input.type = INPUT_ENCODER;
    input.encoderDelta = m_encoderPosition;
    
    // Clear message if active
    if (m_messageActive) {
      m_messageActive = false;
      m_encoderPosition = 0;
      return input;  // Clear message, but don't process the rotation
    }
    
    // Process based on screen
    if (m_currentScreen == SCREEN_MENU) {
      scrollMenu(m_encoderPosition);
    }
    
    // Reset encoder position
    m_encoderPosition = 0;
  }
  
  return input;
}

void UserInterface::handleInput(const UserInput &input) {
  // This method is called by the main program to handle user input
  // Most input handling is done inside getInput(), but additional
  // handling can be done here
}

void UserInterface::setMenuItems(const char* items[], uint8_t count) {
  // Set menu items
  m_menuItems = min(count, (uint8_t)5);
  
  for (int i = 0; i < m_menuItems; i++) {
    strncpy(m_menuText[i], items[i], sizeof(m_menuText[i]) - 1);
    m_menuText[i][sizeof(m_menuText[i]) - 1] = '\0';
  }
}

uint8_t UserInterface::getMenuSelection() {
  return m_menuPosition;
}

void UserInterface::drawStatusScreen(const SystemStatus &status) {
  // Skip if message is active
  if (m_messageActive) {
    return;
  }
  
  // Draw status screen
  m_display.clearDisplay();
  m_display.setTextSize(1);
  m_display.setTextColor(SSD1306_WHITE);
  m_display.setCursor(0, 0);
  
  // Display header
  m_display.println("Prime Resonance Computer");
  
  // Mode and network status
  m_display.print("Mode: ");
  switch (status.mode) {
    case MODE_STANDBY:
      m_display.print("Standby");
      break;
    case MODE_DISCOVERY:
      m_display.print("Discovery");
      break;
    case MODE_NORMAL:
      m_display.print("Normal");
      break;
    case MODE_CALIBRATION:
      m_display.print("Calibration");
      break;
    case MODE_RESONANCE_DETECTION:
      m_display.print("Resonance");
      break;
    default:
      m_display.print("Unknown");
      break;
  }
  
  m_display.print(" Net:");
  m_display.print(status.networkNodes);
  m_display.print("/");
  m_display.println(status.totalNodes);
  
  // Oscillator frequencies
  m_display.print("2Hz:");
  m_display.print(status.oscillatorFreq[0], 3);
  
  m_display.print(" 3Hz:");
  m_display.print(status.oscillatorFreq[1], 3);
  
  m_display.print(" 5Hz:");
  m_display.println(status.oscillatorFreq[2], 3);
  
  // Phase relationships
  m_display.print("Ph1:");
  m_display.print(status.phaseValues[0], 1);
  
  m_display.print(" Ph2:");
  m_display.print(status.phaseValues[1], 1);
  
  m_display.print(" Ph3:");
  m_display.println(status.phaseValues[2], 1);
  
  // Resonance and entropy
  m_display.print("Res:");
  m_display.print(status.resonanceStrength * 100.0f, 1);
  m_display.print("%");
  
  m_display.print(" Ent:");
  m_display.println(status.entropyValue, 2);
  
  // Display control hints
  m_display.println();
  m_display.println("Next  Action  Menu");
  
  m_display.display();
}

void UserInterface::drawPhaseScreen(const SystemStatus &status) {
  // Skip if message is active
  if (m_messageActive) {
    return;
  }
  
  // Draw phase relationships screen
  m_display.clearDisplay();
  m_display.setTextSize(1);
  m_display.setTextColor(SSD1306_WHITE);
  m_display.setCursor(0, 0);
  
  m_display.println("Phase Relationships");
  
  // Phase differences with bar graph
  for (int i = 0; i < NUM_PHASE_DETECTORS; i++) {
    // Draw label based on detector
    switch (i) {
      case 0:
        m_display.print("2-3: ");
        break;
      case 1:
        m_display.print("2-5: ");
        break;
      case 2:
        m_display.print("3-5: ");
        break;
    }
    
    // Draw bar graph
    int barLength = (int)(status.phaseValues[i] / 360.0f * 50.0f);
    for (int j = 0; j < barLength; j++) {
      m_display.print("*");
    }
    
    // Draw value
    m_display.print(" ");
    m_display.print(status.phaseValues[i], 1);
    m_display.println("°");
  }
  
  // Resonance and entropy
  m_display.println();
  m_display.print("Resonance: ");
  m_display.print(status.resonanceStrength * 100.0f, 1);
  m_display.println("%");
  
  m_display.print("Entropy: ");
  m_display.println(status.entropyValue, 3);
  
  // Display control hints
  m_display.println();
  m_display.println("Next  Action  Menu");
  
  m_display.display();
}

void UserInterface::drawOscillatorScreen(const SystemStatus &status) {
  // Skip if message is active
  if (m_messageActive) {
    return;
  }
  
  // Draw oscillator status screen
  m_display.clearDisplay();
  m_display.setTextSize(1);
  m_display.setTextColor(SSD1306_WHITE);
  m_display.setCursor(0, 0);
  
  m_display.println("Oscillator Status");
  
  // Display oscillator frequencies
  for (int i = 0; i < NUM_OSCILLATORS; i++) {
    float target = 0.0f;
    switch (i) {
      case 0: target = 2.0f; break;
      case 1: target = 3.0f; break;
      case 2: target = 5.0f; break;
    }
    
    m_display.print("Osc ");
    m_display.print(i + 1);
    m_display.print(": ");
    m_display.print(status.oscillatorFreq[i], 3);
    m_display.print(" Hz (");
    
    // Calculate error percentage
    float error = 100.0f * (status.oscillatorFreq[i] - target) / target;
    m_display.print(error, 1);
    m_display.println("%)");
  }
  
  // Display calibration status
  m_display.println();
  if (status.mode == MODE_CALIBRATION) {
    m_display.println("Calibration in progress...");
  } else {
    m_display.println("Press Action to calibrate");
  }
  
  // Display control hints
  m_display.println();
  m_display.println("Next  Calib  Menu");
  
  m_display.display();
}

void UserInterface::drawNetworkScreen(const SystemStatus &status) {
  // Skip if message is active
  if (m_messageActive) {
    return;
  }
  
  // Draw network status screen
  m_display.clearDisplay();
  m_display.setTextSize(1);
  m_display.setTextColor(SSD1306_WHITE);
  m_display.setCursor(0, 0);
  
  m_display.println("Network Status");
  
  // Display network information
  m_display.print("Node ID: ");
  m_display.println(status.nodeId);
  
  m_display.print("Active nodes: ");
  m_display.print(status.networkNodes);
  m_display.print("/");
  m_display.println(status.totalNodes);
  
  m_display.print("Status: ");
  if (status.networkConnected) {
    m_display.println("Connected");
  } else {
    m_display.println("Disconnected");
  }
  
  // Display WiFi information
  m_display.print("MAC: ");
  m_display.println(WiFi.macAddress());
  
  // Display control hints
  m_display.println();
  m_display.println("Next  Action  Menu");
  
  m_display.display();
}

void UserInterface::drawMenuScreen() {
  // Skip if message is active
  if (m_messageActive) {
    return;
  }
  
  // Draw menu screen
  m_display.clearDisplay();
  m_display.setTextSize(1);
  m_display.setTextColor(SSD1306_WHITE);
  m_display.setCursor(0, 0);
  
  m_display.println("Menu");
  m_display.println();
  
  // Display menu items
  for (int i = 0; i < m_menuItems; i++) {
    if (i == m_menuPosition) {
      m_display.print("> ");
    } else {
      m_display.print("  ");
    }
    
    m_display.println(m_menuText[i]);
  }
  
  // Display control hints
  m_display.println();
  m_display.println("Select Next  Back");
  
  m_display.display();
}

void UserInterface::checkButtons() {
  // Read button states with debouncing
  unsigned long currentTime = millis();
  
  for (int i = 0; i < 3; i++) {
    // Check if enough time has passed since last change
    if (currentTime - m_lastButtonTime[i] > 50) {
      // Read button state
      uint8_t reading = digitalRead(m_buttonPins[i]);
      
      // If button state has changed
      if (reading != m_buttonState[i]) {
        m_buttonState[i] = reading;
        m_lastButtonTime[i] = currentTime;
      }
    }
  }
}

void UserInterface::checkEncoder() {
  // Read encoder state
  uint8_t currentStateA = digitalRead(ROTARY_A_PIN);
  uint8_t currentStateB = digitalRead(ROTARY_B_PIN);
  
  // Detect rotation
  if (currentStateA != m_encoderLastState) {
    // If A changed, check B to determine direction
    if (currentStateB != currentStateA) {
      // Clockwise
      m_encoderPosition++;
    } else {
      // Counter-clockwise
      m_encoderPosition--;
    }
    
    m_encoderLastState = currentStateA;
  }
}

void UserInterface::scrollMenu(int8_t direction) {
  // Scroll the menu position
  m_menuPosition += direction;
  
  // Wrap around
  if (m_menuPosition >= m_menuItems) {
    m_menuPosition = 0;
  } else if (m_menuPosition < 0) {
    m_menuPosition = m_menuItems - 1;
  }
}
```

## Helper Utilities

### system_log.h

```cpp
/*
 * Prime Resonance Computer
 * System Log Module
 * 
 * This module handles logging of system events and errors.
 */

#ifndef SYSTEM_LOG_H
#define SYSTEM_LOG_H

#include <Arduino.h>
#include "config.h"

// Maximum log entries in RAM
#define MAX_LOG_ENTRIES 32

// Log entry structure
struct LogEntry {
  uint32_t timestamp;
  LogLevel level;
  uint8_t code;
  char message[32];
};

class SystemLog {
private:
  // Circular buffer of log entries
  LogEntry m_entries[MAX_LOG_ENTRIES];
  uint8_t m_head;
  uint8_t m_count;
  
  // Storage settings
  bool m_persistToFlash;
  
public:
  SystemLog();
  
  // Log operations
  void addEntry(LogLevel level, uint8_t code, const char* message);
  LogEntry getEntry(uint8_t index);
  uint8_t getEntryCount();
  
  // Log retrieval
  void dumpToSerial();
  void clearLog();
  
  // Persistence
  void enablePersistence(bool enable);
  bool saveToFlash();
  bool loadFromFlash();
};

// Global log instance
extern SystemLog systemLog;

#endif // SYSTEM_LOG_H
```

### system_log.cpp

```cpp
/*
 * Prime Resonance Computer
 * System Log Implementation
 */

#include "system_log.h"
#include <EEPROM.h>

// Global log instance
SystemLog systemLog;

SystemLog::SystemLog() {
  // Initialize log buffer
  m_head = 0;
  m_count = 0;
  m_persistToFlash = false;
  
  // Clear log entries
  memset(m_entries, 0, sizeof(m_entries));
}

void SystemLog::addEntry(LogLevel level, uint8_t code, const char* message) {
  // Create new log entry
  LogEntry entry;
  entry.timestamp = millis();
  entry.level = level;
  entry.code = code;
  
  // Copy message (truncate if needed)
  strncpy(entry.message, message, sizeof(entry.message) - 1);
  entry.message[sizeof(entry.message) - 1] = '\0';
  
  // Add to circular buffer
  m_entries[m_head] = entry;
  
  // Update head and count
  m_head = (m_head + 1) % MAX_LOG_ENTRIES;
  if (m_count < MAX_LOG_ENTRIES) {
    m_count++;
  }
  
  // Log to serial for debugging
  Serial.print("[");
  Serial.print(entry.timestamp);
  Serial.print("] ");
  
  switch (level) {
    case LOG_ERROR:
      Serial.print("ERROR ");
      break;
    case LOG_WARNING:
      Serial.print("WARN  ");
      break;
    case LOG_INFO:
      Serial.print("INFO  ");
      break;
    case LOG_DEBUG:
      Serial.print("DEBUG ");
      break;
  }
  
  Serial.print("[");
  Serial.print(code);
  Serial.print("] ");
  Serial.println(message);
  
  // Persist if enabled (only for important logs)
  if (m_persistToFlash && (level == LOG_ERROR || level == LOG_WARNING)) {
    saveToFlash();
  }
}

LogEntry SystemLog::getEntry(uint8_t index) {
  // Return empty entry if invalid index
  if (index >= m_count) {
    LogEntry emptyEntry;
    memset(&emptyEntry, 0, sizeof(LogEntry));
    return emptyEntry;
  }
  
  // Calculate actual index in circular buffer
  uint8_t actualIndex;
  if (m_count < MAX_LOG_ENTRIES) {
    actualIndex = index;
  } else {
    actualIndex = (m_head + index) % MAX_LOG_ENTRIES;
  }
  
  return m_entries[actualIndex];
}

uint8_t SystemLog::getEntryCount() {
  return m_count;
}

void SystemLog::dumpToSerial() {
  Serial.println("System Log:");
  Serial.println("----------");
  
  for (uint8_t i = 0; i < m_count; i++) {
    LogEntry entry = getEntry(i);
    
    Serial.print("[");
    Serial.print(entry.timestamp);
    Serial.print("] ");
    
    switch (entry.level) {
      case LOG_ERROR:
        Serial.print("ERROR ");
        break;
      case LOG_WARNING:
        Serial.print("WARN  ");
        break;
      case LOG_INFO:
        Serial.print("INFO  ");
        break;
      case LOG_DEBUG:
        Serial.print("DEBUG ");
        break;
    }
    
    Serial.print("[");
    Serial.print(entry.code);
    Serial.print("] ");
    Serial.println(entry.message);
  }
  
  Serial.println("----------");
}

void SystemLog::clearLog() {
  m_head = 0;
  m_count = 0;
  
  // Clear log entries
  memset(m_entries, 0, sizeof(m_entries));
  
  // Clear persisted log if enabled
  if (m_persistToFlash) {
    // Write zero count to EEPROM
    EEPROM.write(512, 0);
    EEPROM.commit();
  }
}

void SystemLog::enablePersistence(bool enable) {
  m_persistToFlash = enable;
}

bool SystemLog::saveToFlash() {
  // Save log to EEPROM
  // We'll use EEPROM starting at address 512
  
  // Write log count
  EEPROM.write(512, m_count);
  
  // Write entries
  for (uint8_t i = 0; i < m_count && i < MAX_LOG_ENTRIES; i++) {
    LogEntry entry = getEntry(i);
    
    // Calculate EEPROM address
    uint16_t addr = 513 + (i * sizeof(LogEntry));
    
    // Check if enough space in EEPROM
    if (addr + sizeof(LogEntry) > EEPROM_SIZE) {
      break;
    }
    
    // Write entry
    EEPROM.writeBytes(addr, &entry, sizeof(LogEntry));
  }
  
  // Commit changes
  return EEPROM.commit();
}

bool SystemLog::loadFromFlash() {
  // Load log from EEPROM
  
  // Read log count
  uint8_t count = EEPROM.read(512);
  
  // Validate
  if (count > MAX_LOG_ENTRIES) {
    count = MAX_LOG_ENTRIES;
  }
  
  // Clear current log
  m_head = 0;
  m_count = 0;
  memset(m_entries, 0, sizeof(m_entries));
  
  // Read entries
  for (uint8_t i = 0; i < count; i++) {
    // Calculate EEPROM address
    uint16_t addr = 513 + (i * sizeof(LogEntry));
    
    // Check if enough space in EEPROM
    if (addr + sizeof(LogEntry) > EEPROM_SIZE) {
      break;
    }
    
    // Read entry
    LogEntry entry;
    EEPROM.readBytes(addr, &entry, sizeof(LogEntry));
    
    // Add to log
    m_entries[m_head] = entry;
    m_head = (m_head + 1) % MAX_LOG_ENTRIES;
    m_count++;
  }
  
  return true;
}
```

## Implementation Notes

### File Organization

The firmware is organized into modular components that can be placed in separate files:

1. **Core System Files:**
   - main.cpp (Two versions: Coordinator and Compute nodes)
   - config.h (System-wide configuration)

2. **Hardware Control:**
   - oscillator_controller.h/cpp (Manages oscillators)
   - phase_detector.h/cpp (Handles phase detection)

3. **Communication:**
   - network_manager.h/cpp (Handles ESP-NOW communication)

4. **User Interface:**
   - user_interface.h/cpp (Handles display and user input)

5. **Prime Resonance Algorithms:**
   - prime_resonance.h/cpp (Core resonance detection algorithms)

6. **Utilities:**
   - system_log.h/cpp (System logging facility)

### Build Instructions

1. **Install Arduino IDE:**
   - Download from https://www.arduino.cc/en/software
   - Install ESP32 board package using Boards Manager

2. **Install Required Libraries:**
   - Adafruit SSD1306
   - Adafruit GFX
   - Wire (Built-in)
   - SPI (Built-in)
   - EEPROM (Built-in)
   - WiFi (Built-in with ESP32)
   - ESP-NOW (Built-in with ESP32)

3. **Create Project Structure:**
   - Create a new folder for the project
   - Copy each of the files listed above into the project folder
   - Ensure each .cpp file has a corresponding .h file

4. **Configure Arduino IDE:**
   - Set board to "ESP32 Dev Module"
   - Set Flash Size to "4MB"
   - Set Upload Speed to "921600"

5. **Compile and Upload:**
   - For Coordinator node: Ensure NODE_ID is set to 1
   - For Compute nodes: Set NODE_ID to unique values (2, 3, etc.)
   - Compile and upload to respective ESP32 boards

### Hardware Testing

1. **Initial Power Test:**
   - Connect power to ESP32
   - Check OLED display initializes
   - Verify serial output shows initialization messages

2. **Oscillator Test:**
   - Connect oscilloscope to oscillator outputs
   - Verify frequencies match expected values
   - Test frequency adjustment via PWM control

3. **Phase Detector Test:**
   - Inject known signals into phase detector inputs
   - Verify ADC readings match expected values

4. **Network Test:**
   - Power up multiple nodes
   - Verify they discover each other
   - Check data transmission between nodes

5. **Complete System Test:**
   - Run system with known resonance patterns
   - Verify prime detection accuracy
   - Test various failure modes and recovery

### Custom Adjustments

The firmware may need adjustments based on specific hardware implementation:

1. **Oscillator Frequency Range:**
   - Modify the PWM parameters in oscillator_controller.cpp
   - Adjust calibration algorithm for specific VCO characteristics

2. **Phase Detector Scaling:**
   - Calibrate voltage-to-phase conversion in phase_detector.cpp
   - Adjust filtering parameters based on signal quality

3. **Display Resolution:**
   - Modify SCREEN_WIDTH and SCREEN_HEIGHT in config.h
   - Adjust UI layouts in user_interface.cpp

4. **Network Parameters:**
   - Change WiFi channel in network_manager.cpp if interference occurs
   - Adjust message buffer sizes based on data volume

5. **Memory Optimization:**
   - Reduce MAX_LOG_ENTRIES if memory is constrained
   - Minimize string constants to save program memory