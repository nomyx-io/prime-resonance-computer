# Appendix C: Firmware Reference

## Introduction

This appendix provides comprehensive documentation for the Prime Resonance Computer firmware. It includes a complete command reference, details on configuration parameters, API documentation, guidance on developing custom algorithms, and an overview of the firmware structure. This information is particularly valuable for users who wish to modify the firmware, extend its capabilities, or integrate it with other systems.

The firmware is written in C++ using the Arduino framework for ESP32. It is designed with a modular architecture to facilitate customization and extension. All source code is available in the project repository referenced in the introduction.

## Command Reference

The Prime Resonance Computer communicates via a command-line interface over serial (USB) or WiFi (if enabled). Commands follow a consistent syntax pattern:

```
<command> <subcommand> [parameters...]
```

### General System Commands

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `help` | [command] | Display help information | `help compute` |
| `system` | `status` | Show system status and information | `system status` |
| `system` | `reset` | Perform soft reset of the system | `system reset` |
| `system` | `factory_reset` | Reset to factory defaults | `system factory_reset` |
| `system` | `update` | Check for and apply firmware updates | `system update` |
| `system` | `info` | Show detailed system information | `system info` |
| `mode` | `<mode_name>` | Switch to specified operation mode | `mode research` |
| `set` | `<parameter> <value>` | Set configuration parameter | `set compute_intensity 3` |
| `get` | `<parameter>` | Get configuration parameter value | `get compute_intensity` |
| `config` | `save` | Save current configuration to flash | `config save` |
| `config` | `load` | Load configuration from flash | `config load` |
| `config` | `list` | List all configuration parameters | `config list` |
| `config` | `backup` | Backup configuration to file | `config backup config.json` |
| `config` | `restore <filename>` | Restore configuration from file | `config restore config.json` |

### Compute Operations

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `compute` | `prime <number>` | Test if number is prime | `compute prime 17` |
| `compute` | `prime <number> extended` | Perform extended prime test | `compute prime 37 extended` |
| `compute` | `factorize <number>` | Find prime factors of number | `compute factorize 15` |
| `compute` | `prime_sequence <start> <end>` | Find primes in range | `compute prime_sequence 10 100` |
| `compute` | `pattern <sequence>` | Analyze number sequence pattern | `compute pattern 2,4,6,8,10` |
| `compute` | `pattern_deep <sequence>` | Deep pattern analysis | `compute pattern_deep 2,3,5,8,13` |
| `compute` | `load <filename>` | Load custom algorithm | `compute load factor_sieves.alg` |
| `compute` | `custom <algorithm> [params...]` | Run custom algorithm | `compute custom factor_sieves 125` |
| `compute` | `distributed <subcommand> [params...]` | Distributed computation | `compute distributed factorize 1273` |

### Oscillator Control

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `oscillator` | `status` | Show oscillator status | `oscillator status` |
| `oscillator` | `frequency <n> <freq>` | Set oscillator n frequency | `oscillator frequency 1 2.0` |
| `oscillator` | `enable <n>` | Enable oscillator n | `oscillator enable 2` |
| `oscillator` | `disable <n>` | Disable oscillator n | `oscillator disable 3` |
| `oscillator` | `stability <n>` | Test oscillator stability | `oscillator stability 1` |
| `oscillator` | `tune <n>` | Auto-tune oscillator to exact freq | `oscillator tune 2` |

### Data Management

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `log` | `start <filename>` | Start logging to file | `log start experiment.log` |
| `log` | `stop` | Stop logging | `log stop` |
| `log` | `level <level>` | Set log detail level | `log level detailed` |
| `export` | `csv <data> <filename>` | Export data as CSV | `export csv phase_data phases.csv` |
| `export` | `json <data> <filename>` | Export data as JSON | `export json prime_test results.json` |
| `export` | `raw <sensor> <duration> <filename>` | Export raw sensor data | `export raw phase_detector1 30 raw.dat` |
| `stream` | `<data_type> <interval>` | Stream data over serial | `stream phase_angles 100` |
| `stream` | `network <data_type> <interval> <port>` | Stream data over network | `stream network resonance 200 8888` |
| `data` | `export <parameter> <duration>` | Export parameter data | `data export phase_raw 10` |

### Visualization

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `visualize` | `<parameter>` | Visualize parameter | `visualize phase_space` |
| `visualize` | `phase_space <number>` | Show phase space for number | `visualize phase_space 17` |
| `visualize` | `spectrum <number>` | Show frequency spectrum | `visualize spectrum 17` |
| `visualize` | `entropy <number>` | Show entropy surface | `visualize entropy 17` |

### Analysis Commands

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `analyze` | `compare <number1> <number2>` | Compare resonance patterns | `analyze compare 17 19` |
| `analyze` | `trend <start> <end> <step>` | Analyze trend across range | `analyze trend 100 200 10` |
| `analyze` | `factors <number>` | Map factor relationships | `analyze factors 84` |
| `analyze` | `correlate <param1> <param2>` | Correlation analysis | `analyze correlate entropy_symbolic phase_stability` |

### Network and Multi-Node Commands

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `network` | `status` | Show network status | `network status` |
| `network` | `scan` | Scan for other nodes | `network scan` |
| `network` | `sync` | Synchronize with other nodes | `network sync` |
| `network` | `check` | Check node connectivity | `network check` |
| `network` | `reset` | Reset network configuration | `network reset` |
| `network` | `set <parameter> <value>` | Set network parameter | `network set node_name PRC001` |
| `network` | `monitor` | Monitor network activity | `network monitor` |
| `node<id>` | `<command> [params...]` | Execute command on specific node | `node2 compute prime 17` |
| `broadcast` | `<command> [params...]` | Execute on all nodes | `broadcast reset` |

### File System Commands

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `fs` | `list` | List files in SPIFFS | `fs list` |
| `fs` | `delete <filename>` | Delete file | `fs delete results.csv` |
| `fs` | `format` | Format file system | `fs format` |
| `fs` | `info` | Show file system info | `fs info` |
| `fs` | `cat <filename>` | Display file contents | `fs cat config.json` |
| `fs` | `mkdir <dirname>` | Create directory | `fs mkdir experiments` |

### Benchmark and Testing

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `benchmark` | `run` | Run full benchmark suite | `benchmark run` |
| `benchmark` | `<test_name>` | Run specific benchmark | `benchmark prime` |
| `benchmark` | `optimize` | Find optimal settings | `benchmark optimize` |
| `benchmark` | `apply` | Apply benchmark recommendations | `benchmark apply` |
| `test` | `adc` | Test ADC functionality | `test adc` |
| `test` | `gpio` | Test GPIO pins | `test gpio` |
| `test` | `memory` | Test memory allocation | `test memory` |
| `debug` | `oscillator <n>` | Debug oscillator | `debug oscillator 2` |
| `debug` | `enable <module>` | Enable debug for module | `debug enable phase_detector` |
| `debug` | `disable <module>` | Disable debug for module | `debug disable phase_detector` |

### Web and API Interface (if WiFi enabled)

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `web` | `enable` | Enable web server | `web enable` |
| `web` | `disable` | Disable web server | `web disable` |
| `web` | `port <port>` | Set web server port | `web port 8080` |
| `api` | `enable` | Enable REST API | `api enable` |
| `api` | `disable` | Disable REST API | `api disable` |
| `api` | `auth <token>` | Set API auth token | `api auth secretkey123` |

### Plugin System

| Command | Parameters | Description | Example |
|---------|------------|-------------|---------|
| `plugin` | `list` | List installed plugins | `plugin list` |
| `plugin` | `install <filename>` | Install plugin | `plugin install custom_viz.plugin` |
| `plugin` | `uninstall <name>` | Remove plugin | `plugin uninstall custom_viz` |
| `plugin` | `enable <name>` | Enable plugin | `plugin enable custom_viz` |
| `plugin` | `disable <name>` | Disable plugin | `plugin disable custom_viz` |
| `plugin` | `register <name>` | Register new plugin | `plugin register factor_methods` |

## Configuration Parameters

The Prime Resonance Computer firmware has numerous configuration parameters that control its behavior. These parameters can be accessed using the `set`, `get`, and `config` commands.

### System Parameters

| Parameter | Type | Range/Options | Default | Description |
|-----------|------|--------------|---------|-------------|
| `system_name` | String | Up to 32 chars | "PrimeResonance" | Device name |
| `auto_calibration` | Boolean | true/false | true | Enable auto-calibration on startup |
| `boot_mode` | Enum | "normal", "safe", "debug" | "normal" | System boot mode |
| `display_rotation` | Integer | 0, 90, 180, 270 | 0 | Display orientation in degrees |
| `display_contrast` | Integer | 0-255 | 127 | Display contrast |
| `display_timeout` | Integer | 0-3600 | 300 | Screen timeout in seconds (0=never) |
| `led_brightness` | Integer | 0-255 | 128 | LED brightness |
| `button_debounce` | Integer | 10-1000 | 50 | Button debounce time (ms) |
| `serial_baud` | Integer | 9600-921600 | 115200 | Serial port baud rate |
| `power_save` | Boolean | true/false | true | Enable power saving features |
| `deep_sleep_timeout` | Integer | 0-86400 | 3600 | Deep sleep timeout in seconds (0=never) |

### Computation Parameters

| Parameter | Type | Range/Options | Default | Description |
|-----------|------|--------------|---------|-------------|
| `compute_intensity` | Integer | 1-5 | 3 | Computation intensity level |
| `compute_iterations` | Integer | 1-100 | 3 | Number of iterations for validation |
| `confidence_threshold` | Float | 0.0-1.0 | 0.75 | Minimum confidence for valid result |
| `max_computation_time` | Integer | 1-3600 | 300 | Maximum computation time (seconds) |
| `auto_retry` | Boolean | true/false | true | Auto-retry on low confidence |
| `resonance_sensitivity` | Float | 0.1-10.0 | 1.0 | Sensitivity to resonance patterns |
| `phase_threshold` | Float | 0.01-0.5 | 0.1 | Threshold for phase detection |
| `entropy_weight` | Float | 0.0-1.0 | 0.5 | Weight of entropy in calculations |

### Oscillator Parameters

| Parameter | Type | Range/Options | Default | Description |
|-----------|------|--------------|---------|-------------|
| `osc1_frequency` | Float | 1.0-10.0 | 2.0 | Target frequency for oscillator 1 (Hz) |
| `osc2_frequency` | Float | 1.0-10.0 | 3.0 | Target frequency for oscillator 2 (Hz) |
| `osc3_frequency` | Float | 1.0-10.0 | 5.0 | Target frequency for oscillator 3 (Hz) |
| `frequency_tolerance` | Float | 0.001-0.1 | 0.01 | Acceptable frequency deviation (%) |
| `calibration_interval` | Integer | 0-86400 | 3600 | Automatic calibration interval (sec) |
| `phase_detect_samples` | Integer | 10-1000 | 100 | Samples per phase measurement |
| `entropy_source_gain` | Float | 0.1-10.0 | 1.0 | Gain applied to entropy source |
| `adc_averaging` | Integer | 1-256 | 64 | Number of ADC samples to average |

### Network Parameters (for multi-node systems)

| Parameter | Type | Range/Options | Default | Description |
|-----------|------|--------------|---------|-------------|
| `node_role` | Enum | "standalone", "coordinator", "worker" | "standalone" | Role in multi-node setup |
| `node_name` | String | Up to 32 chars | "PRC-Node" | Node identifier |
| `network_enabled` | Boolean | true/false | false | Enable networking |
| `network_channel` | Integer | 1-14 | 6 | WiFi/ESP-NOW channel |
| `max_nodes` | Integer | 1-10 | 4 | Maximum nodes in network |
| `sync_interval` | Integer | 1-3600 | 60 | Node synchronization interval (sec) |

### WiFi Parameters (if enabled)

| Parameter | Type | Range/Options | Default | Description |
|-----------|------|--------------|---------|-------------|
| `wifi_mode` | Enum | "off", "station", "ap", "ap+sta" | "off" | WiFi operation mode |
| `wifi_ssid` | String | Up to 32 chars | "PrimeResonance" | Network SSID/name |
| `wifi_password` | String | Up to 64 chars | "resonance123" | WiFi password |
| `ap_channel` | Integer | 1-14 | 6 | Access point channel |
| `ap_hidden` | Boolean | true/false | false | Hide access point SSID |
| `ap_max_clients` | Integer | 1-10 | 4 | Max simultaneous connections |
| `enable_mdns` | Boolean | true/false | true | Enable mDNS discovery |
| `hostname` | String | Up to 32 chars | "prime-resonance" | mDNS hostname |

### Logging Parameters

| Parameter | Type | Range/Options | Default | Description |
|-----------|------|--------------|---------|-------------|
| `log_level` | Enum | "none", "basic", "standard", "detailed", "debug" | "standard" | Default logging detail level |
| `log_to_serial` | Boolean | true/false | true | Output logs to serial port |
| `log_to_file` | Boolean | true/false | false | Save logs to filesystem |
| `log_max_size` | Integer | 1-1024 | 64 | Maximum log file size (KB) |
| `log_rotation` | Boolean | true/false | true | Enable log rotation |
| `log_timestamps` | Boolean | true/false | true | Include timestamps in logs |

### Advanced Parameters

| Parameter | Type | Range/Options | Default | Description |
|-----------|------|--------------|---------|-------------|
| `enable_experimental` | Boolean | true/false | false | Enable experimental features |
| `algorithm_variant` | Enum | "standard", "fast", "precise" | "standard" | Algorithm optimization variant |
| `heap_reserve` | Integer | 10-50 | 20 | Minimum free heap percentage |
| `task_priority` | Integer | 1-24 | 10 | Main task priority |
| `watchdog_timeout` | Integer | 0-120 | 30 | Watchdog timeout (seconds) |
| `parallel_operations` | Boolean | true/false | true | Enable parallel operations |

## API Documentation

The Prime Resonance Computer offers a RESTful API when WiFi and the API are enabled. This section documents the available endpoints and their usage.

### API Authentication

API requests require authentication with a token, which can be set using the `api auth` command. Include the token in an Authorization header:

```
Authorization: Bearer [token]
```

### API Endpoints

#### System Information

**GET /api/system/info**

Returns system information and status.

Example response:
```json
{
  "system": {
    "name": "PrimeResonance",
    "firmware_version": "1.0.0",
    "uptime": 3642,
    "heap_free": 123456,
    "flash_size": 4194304,
    "flash_used": 2097152
  },
  "status": {
    "operational": true,
    "calibrated": true, 
    "oscillators": [
      {"id": 1, "frequency": 2.002, "enabled": true, "stable": true},
      {"id": 2, "frequency": 3.001, "enabled": true, "stable": true},
      {"id": 3, "frequency": 4.998, "enabled": true, "stable": true}
    ]
  }
}
```

#### Computation Operations

**GET /api/compute/prime/{number}**

Test if a number is prime.

Example response for `/api/compute/prime/17`:
```json
{
  "number": 17,
  "is_prime": true,
  "confidence": 0.993,
  "computation_time_ms": 3452,
  "resonance_score": 8.7
}
```

**GET /api/compute/factorize/{number}**

Factorize a number into its prime components.

Example response for `/api/compute/factorize/15`:
```json
{
  "number": 15,
  "factors": [3, 5],
  "confidence": 0.997,
  "computation_time_ms": 2782
}
```

**GET /api/compute/prime_sequence/{start}/{end}**

Find prime numbers within a range.

Example response for `/api/compute/prime_sequence/10/20`:
```json
{
  "range": {"start": 10, "end": 20},
  "primes": [11, 13, 17, 19],
  "count": 4,
  "computation_time_ms": 5341
}
```

**POST /api/compute/pattern**

Analyze pattern in a sequence of numbers.

Request body:
```json
{
  "sequence": [2, 4, 6, 8, 10]
}
```

Example response:
```json
{
  "pattern_type": "arithmetic_progression",
  "formula": "a(n) = 2n",
  "next_elements": [12, 14, 16],
  "confidence": 0.974,
  "computation_time_ms": 1243
}
```

#### Configuration

**GET /api/config**

Get all configuration parameters.

**GET /api/config/{parameter}**

Get a specific configuration parameter.

**PUT /api/config/{parameter}**

Update a configuration parameter.

Request body:
```json
{
  "value": 3
}
```

**POST /api/config/save**

Save current configuration to flash.

#### Data Export

**GET /api/data/{type}/{duration}**

Export data of specified type for the given duration.

Example for `/api/data/phase_raw/5`:
```json
{
  "data_type": "phase_raw",
  "duration": 5,
  "timestamp": 1620000000,
  "sampling_rate": 100,
  "samples": [
    [0.23, 0.45, 0.67],
    [0.24, 0.46, 0.68],
    // ... more samples
  ]
}
```

#### Visualization

**GET /api/visualize/{type}/{number}**

Get visualization data for a specific number.

Example for `/api/visualize/phase_space/17`:
```json
{
  "type": "phase_space",
  "number": 17,
  "dimensions": 3,
  "points": [
    [0.23, 0.45, 0.67],
    [0.25, 0.47, 0.68],
    // ... more points
  ]
}
```

#### Multi-Node Operations

**GET /api/network/status**

Get status of all nodes in the network.

**POST /api/network/command**

Send command to specific node or broadcast.

Request body:
```json
{
  "target": "node2", // or "broadcast"
  "command": "compute prime 17"
}
```

### WebSocket API

For real-time data, connect to the WebSocket endpoint:

```
ws://[device-ip]/ws
```

Available message types:

| Type | Description | Example Payload |
|------|-------------|----------------|
| `system_status` | System status updates | `{"heap_free": 123456, "operational": true}` |
| `compute_progress` | Computation progress | `{"operation": "factorize", "number": 123, "progress": 0.45}` |
| `oscillator_data` | Real-time oscillator data | `{"oscillators": [[time, osc1, osc2, osc3], ...]}` |
| `phase_data` | Real-time phase relationships | `{"phases": [[time, phase1_2, phase1_3, phase2_3], ...]}` |
| `error` | Error messages | `{"code": "CAL_FAILED", "message": "Calibration failed"}` |

To subscribe to specific events:

```json
{
  "action": "subscribe",
  "topics": ["system_status", "phase_data"]
}
```

## Custom Algorithm Development Guide

The Prime Resonance Computer firmware supports custom algorithms for specialized computation or research purposes. This section outlines how to develop and integrate custom algorithms.

### Algorithm Structure

Custom algorithms should be defined in a .alg file with the following structure:

```javascript
{
  "name": "example_algorithm",
  "version": "1.0.0",
  "description": "Example custom algorithm",
  "author": "Your Name",
  "parameters": [
    {
      "name": "input_number",
      "type": "integer",
      "description": "The input number to process"
    },
    {
      "name": "iterations",
      "type": "integer",
      "default": 10,
      "description": "Number of iterations to perform"
    }
  ],
  "code": "...[Base64 encoded C++ code]...",
  "entrypoint": "processAlgorithm"
}
```

### Algorithm Interface

Custom algorithms must implement the following C++ function signature:

```cpp
ComputeResult processAlgorithm(JsonObject params, ResonanceEngine* engine);
```

Where `ComputeResult` is a structure defined as:

```cpp
struct ComputeResult {
  bool success;
  float confidence;
  JsonDocument result;
  String error_message;
};
```

### Resonance Engine API

Custom algorithms can access the resonance engine through the provided pointer, which offers these key methods:

```cpp
class ResonanceEngine {
public:
  // Core resonance methods
  float measureResonance(int number);
  float[] getPhaseAngles(int number);
  float getEntropy(int number);
  
  // Prime testing and factorization
  bool isPrime(int number, float& confidence);
  std::vector<int> factorize(int number, float& confidence);
  
  // Raw oscillator control
  void setOscillatorFrequency(int oscillator, float frequency);
  float measurePhase(int oscillator1, int oscillator2);
  
  // Utility methods
  float computeResonanceScore(float[] phases);
  void beginResonanceSequence(int number);
  void endResonanceSequence();
};
```

### Example Custom Algorithm

Here's a simple example of a custom algorithm that checks if a number exhibits strong resonance with specific patterns:

```cpp
#include "resonance_engine.h"

ComputeResult processAlgorithm(JsonObject params, ResonanceEngine* engine) {
  ComputeResult result;
  result.success = false;
  
  // Get parameters
  if (!params.containsKey("input_number")) {
    result.error_message = "Missing required parameter: input_number";
    return result;
  }
  
  int number = params["input_number"];
  int iterations = params.containsKey("iterations") ? params["iterations"] : 10;
  
  // Begin measurement sequence
  engine->beginResonanceSequence(number);
  
  float totalResonance = 0.0f;
  for (int i = 0; i < iterations; i++) {
    float resonance = engine->measureResonance(number);
    totalResonance += resonance;
  }
  
  // End measurement sequence
  engine->endResonanceSequence();
  
  // Calculate average resonance
  float avgResonance = totalResonance / iterations;
  
  // Create result
  result.success = true;
  result.confidence = 0.9f;
  result.result["number"] = number;
  result.result["average_resonance"] = avgResonance;
  result.result["iterations"] = iterations;
  result.result["is_significant"] = (avgResonance > 7.0f);
  
  return result;
}
```

### Testing and Deploying Custom Algorithms

1. Write your algorithm in a C++ file
2. Base64 encode the content and create the .alg JSON file
3. Upload the algorithm file to the Prime Resonance Computer using the web interface or `fs` commands
4. Load the algorithm with `compute load <filename>`
5. Execute it with `compute custom <algorithm_name> [parameters...]`

### Tips for Custom Algorithm Development

1. **Memory Management**: ESP32 has limited RAM. Use static allocation when possible and avoid large arrays
2. **Computation Time**: Include progress reporting for long-running operations
3. **Error Handling**: Validate all inputs and provide clear error messages
4. **Power Awareness**: For battery operations, optimize algorithms to minimize active time
5. **Testing**: Test with various inputs, including edge cases and invalid data

## Firmware Structure Overview

The Prime Resonance Computer firmware follows a modular architecture to facilitate maintenance and extension. This section provides an overview of the main components and their interactions.

### Main Components

![Firmware Structure Diagram](images/firmware_structure.png)

1. **Core System**
   - `main.cpp`: Entry point, setup and loop functions
   - `SystemManager`: Overall system coordination
   - `ConfigurationManager`: Parameter management
   - `CommandProcessor`: Command parsing and execution

2. **Resonance Engine**
   - `ResonanceEngine`: Core computational algorithms
   - `OscillatorController`: Manages hardware oscillators
   - `PhaseDetector`: Phase relationship measurement
   - `EntropySource`: Entropy generation and management

3. **Hardware Abstraction**
   - `HardwareManager`: Device-specific implementations
   - `Display`: Interface for OLED/LCD displays
   - `InputManager`: Button and sensor handling
   - `ADCController`: Analog reading with calibration

4. **Communication**
   - `SerialInterface`: Command-line interface
   - `NetworkManager`: WiFi and ESP-NOW functionality
   - `APIServer`: REST API implementation
   - `WebSocketServer`: Real-time data streaming

5. **Storage**
   - `FileSystemManager`: SPIFFS file operations
   - `LogManager`: Logging functionality
   - `DataExporter`: Export data in various formats

6. **Extension Systems**
   - `AlgorithmLoader`: Custom algorithm support
   - `PluginManager`: Plugin system implementation
   - `VisualizationEngine`: Data visualization

### Key Files and Directories

```
/src
  /core
    main.cpp                 // Entry point
    SystemManager.cpp/h      // Core system management
    ConfigurationManager.cpp/h // Parameter handling
    CommandProcessor.cpp/h   // Command parsing
  /resonance
    ResonanceEngine.cpp/h    // Core computation
    OscillatorController.cpp/h // Oscillator management
    PhaseDetector.cpp/h      // Phase measurement
    EntropySource.cpp/h      // Entropy handling
  /hardware
    HardwareManager.cpp/h    // Hardware abstraction
    Display.cpp/h           // Display interface
    InputManager.cpp/h      // Button handling
    ADCController.cpp/h     // ADC management
  /communication
    SerialInterface.cpp/h   // Serial communication
    NetworkManager.cpp/h    // Networking
    APIServer.cpp/h         // REST API
    WebSocketServer.cpp/h   // WebSockets
  /storage
    FileSystemManager.cpp/h // File operations
    LogManager.cpp/h        // Logging
    DataExporter.cpp/h      // Data export
  /extensions
    AlgorithmLoader.cpp/h   // Custom algorithms
    PluginManager.cpp/h     // Plugin system
    VisualizationEngine.cpp/h // Visualization
  /utils
    MathUtils.cpp/h         // Mathematical utilities
    StringUtils.cpp/h       // String processing
    JSONUtils.cpp/h         // JSON handling
    TimeUtils.cpp/h         // Time functions
/lib
  /external              // External libraries
/data
  /web                   // Web interface files
  /default               // Default configuration
/include                 // Global headers
```

### Execution Flow

1. **Initialization**:
   - Hardware initialization
   - Configuration loading
   - Subsystem startup
   - Oscillator calibration

2. **Main Loop**:
   - Command processing
   - Scheduled tasks execution
   - Communication handling
   - Power management

3. **Command Execution**:
   - Command parsing
   - Parameter validation
   - Subsystem API calls
   - Result formatting and output

4. **Computation Flow**:
   - Input validation
   - Resonance engine setup
   - Oscillator configuration
   - Measurement and analysis
   - Result generation

### Memory Management

The firmware employs several strategies to manage the ESP32's limited memory:

1. **Static Allocation**: Pre-allocated buffers for frequent operations
2. **Memory Pools**: Dedicated memory regions for specific subsystems
3. **Reference Counting**: For shared resources
4. **Memory Guards**: Detection of leaks and corruption
5. **Heap Fragmentation Management**: Periodically compacting heap when possible

### Task Management

The firmware uses FreeRTOS tasks for parallel operation:

1. **Main Task**: UI and command processing
2. **Computation Task**: Heavy computational work 
3. **Network Task**: Communication handling
4. **Monitoring Task**: System health and status
5. **Background Task**: Housekeeping operations

### Extending the Firmware

To extend the firmware with new functionality:

1. **Adding Commands**:
   - Extend the `CommandProcessor` class
   - Register new command handlers
   - Implement command logic

2. **Adding Parameters**:
   - Define parameter in `ConfigurationManager`
   - Set default, range, and type
   - Implement any special handling needed

3. **New Algorithms**:
   - Either use the custom algorithm system or
   - Extend `ResonanceEngine` with new methods
   - Update command interfaces to expose functionality

4. **Hardware Extensions**:
   - Implement drivers in `HardwareManager`
   - Create appropriate abstraction interfaces
   - Update initialization and management code

## Conclusion

This firmware reference provides a comprehensive overview of the Prime Resonance Computer's software capabilities. With this information, you should be able to effectively use all features of the system, modify its behavior through configuration, extend its functionality with custom algorithms, and understand its internal architecture.

For the latest updates, bug fixes, and community contributions, visit the project repository. We encourage you to contribute improvements and extensions to the firmware, especially in areas such as new computational algorithms, visualization methods, and hardware support.

Remember that the firmware is designed to be flexible and extensible, allowing you to adapt the Prime Resonance Computer to your specific research and experimental needs.