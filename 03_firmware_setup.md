# 3. Firmware Setup

## 3.1 Introduction to Firmware

The Prime Resonance Computer firmware is the software that powers all operations of the device. It is responsible for controlling the oscillators, measuring phase relationships, analyzing resonance patterns, and implementing the computational algorithms that make the device function. This section provides comprehensive guidance for installing, configuring, and using the firmware.

### 3.1.1 Firmware Architecture Overview

The firmware is built around a modular architecture that separates different functional areas:

1. **Core System**: Handles initialization, configuration, and system management
2. **Oscillator Control**: Manages hardware oscillators and frequency measurement
3. **Phase Detection**: Processes ADC readings and calculates phase relationships
4. **Resonance Engine**: Implements the mathematical algorithms for prime computations
5. **User Interface**: Manages display output and button input
6. **Command Interface**: Provides serial console for control and data export

This architecture allows for flexibility, maintainability, and the ability to modify specific aspects of operation without affecting the entire system.

### 3.1.2 Development Environment Requirements

To install and customize the firmware, you will need:

**Hardware Requirements**:
- Computer with USB port
- USB cable compatible with your ESP32 board
- Internet connection (for downloading software)

**Software Requirements**:
- Arduino IDE (version 1.8.19 or later) or PlatformIO
- ESP32 board support package
- Required libraries (specified in Section 3.2.2)
- Terminal emulator (for serial communication)

## 3.2 Firmware Installation

### 3.2.1 Development Environment Setup

#### Arduino IDE Setup

1. **Download and install Arduino IDE**
   - Visit [arduino.cc/en/software](https://arduino.cc/en/software)
   - Download version 1.8.19 or later for your operating system
   - Follow the installation instructions for your platform

2. **Install ESP32 board support**
   - Open Arduino IDE
   - Go to File > Preferences
   - Add the following URL to "Additional Boards Manager URLs":
     ```
     https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
     ```
   - Go to Tools > Board > Boards Manager
   - Search for "esp32"
   - Install "ESP32 by Espressif Systems" (version 2.0.0 or later)

3. **Select your board**
   - Go to Tools > Board > ESP32 Arduino
   - Select your specific ESP32 board model (typically "ESP32 Dev Module")
   - Configure port settings as needed:
     - Tools > Port: Select the COM port or device path for your ESP32
     - Tools > Upload Speed: 921600 recommended
     - Tools > Flash Frequency: 80MHz
     - Tools > Flash Mode: QIO
     - Tools > Partition Scheme: Default (4MB with spiffs)

#### PlatformIO Setup (Alternative)

For more advanced users, PlatformIO offers enhanced development capabilities:

1. **Install Visual Studio Code**
   - Download from [code.visualstudio.com](https://code.visualstudio.com)
   - Install following the instructions for your platform

2. **Install PlatformIO Extension**
   - Open VS Code
   - Go to Extensions panel (or press Ctrl+Shift+X)
   - Search for "PlatformIO"
   - Install "PlatformIO IDE" extension

3. **Create new project**
   - Click the PlatformIO icon in the sidebar
   - Select "New Project"
   - Enter "PrimeResonance" as the project name
   - Select "Espressif ESP32 Dev Module" as the board
   - Select "Arduino" as the framework
   - Click "Finish"

### 3.2.2 Required Libraries

Install the following libraries for Arduino IDE:

1. **Open Arduino IDE Library Manager**
   - Go to Sketch > Include Library > Manage Libraries

2. **Install each required library**:
   - **Adafruit SSD1306** (version 2.5.0 or later) - Display driver
   - **Adafruit GFX** (version 1.11.0 or later) - Graphics library
   - **ArduinoJSON** (version 6.19.0 or later) - JSON parsing and generation
   - **ESP32 FFT** - Fast Fourier Transform library
   - **RunningMedian** - Signal filtering

For PlatformIO, add these to your project's `platformio.ini` file:

```ini
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino
monitor_speed = 115200
lib_deps =
    adafruit/Adafruit SSD1306@^2.5.0
    adafruit/Adafruit GFX Library@^1.11.0
    bblanchon/ArduinoJson@^6.19.0
    kosme/ESP32 FFT
    robtillaart/RunningMedian@^0.3.5
```

### 3.2.3 Downloading the Firmware

The Prime Resonance Computer firmware is available through multiple methods:

1. **GitHub Repository**
   - Visit [github.com/prime-resonance/firmware](https://github.com/prime-resonance/firmware)
   - Clone or download the repository
   - Extract the files to a convenient location

2. **Pre-packaged Release Archive**
   - Visit the Releases page on GitHub
   - Download the latest release archive
   - Extract to a convenient location

3. **From the Project Documentation**
   - The complete source code is included in Appendix E
   - Copy the code files to appropriate locations in your project

### 3.2.4 Configuring the Firmware

Before uploading, you may need to modify configuration settings:

1. **Open the config.h file**
   - Locate in the main folder of the firmware

2. **Adjust hardware-specific settings**
   - Modify pin assignments if your wiring differs from the standard design
   - Update display settings if using a different display model
   - Configure ADC parameters to match your specific ESP32 model

Example configuration modifications:

```cpp
// Pin Assignments - modify if your wiring differs
#define PIN_OSC1 0        // First oscillator input
#define PIN_OSC2 2        // Second oscillator input
#define PIN_OSC3 15       // Third oscillator input
#define PIN_BUTTON1 16    // First button
#define PIN_BUTTON2 17    // Second button
#define PIN_LED1 18       // Status LED 1
#define PIN_LED2 19       // Status LED 2

// ADC Channels - modify if your wiring differs
#define ADC_PHASE1_2 36   // Phase detector for osc 1 & 2
#define ADC_PHASE1_3 39   // Phase detector for osc 1 & 3
#define ADC_PHASE2_3 34   // Phase detector for osc 2 & 3
#define ADC_ENTROPY 35    // Entropy source input

// Frequency targets
#define TARGET_FREQ1 2.0f // Target frequency for oscillator 1 (Hz)
#define TARGET_FREQ2 3.0f // Target frequency for oscillator 2 (Hz)
#define TARGET_FREQ3 5.0f // Target frequency for oscillator 3 (Hz)
```

### 3.2.5 Compiling and Uploading

#### Via Arduino IDE

1. **Open the main sketch**
   - Navigate to the firmware folder
   - Open `prime_resonance.ino`

2. **Verify/compile the code**
   - Click the Verify button (checkmark icon) or press Ctrl+R
   - Wait for compilation to complete
   - Fix any errors that may appear

3. **Upload to the ESP32**
   - Connect your ESP32 board via USB
   - Click the Upload button (right arrow icon) or press Ctrl+U
   - Wait for upload to complete
   - Note: You may need to press the BOOT button on your ESP32 board during upload

#### Via PlatformIO

1. **Import the project**
   - In VS Code with PlatformIO
   - Click "Open Project" and select the firmware folder
   - Alternatively, copy source files to your PlatformIO project

2. **Build the project**
   - Click the checkmark icon in the PlatformIO toolbar
   - Or use the PlatformIO menu: "Build"

3. **Upload to the ESP32**
   - Click the arrow icon in the PlatformIO toolbar
   - Or use the PlatformIO menu: "Upload"

### 3.2.6 Verifying Installation

After uploading, confirm the firmware is working correctly:

1. **Open serial monitor**
   - Arduino IDE: Tools > Serial Monitor (set to 115200 baud)
   - PlatformIO: Click the plug icon in the toolbar

2. **Check for boot messages**
   - You should see initialization messages
   - System should report "Prime Resonance Computer Initialized"

3. **Verify hardware detection**
   - System should report oscillator frequencies
   - Display should initialize and show the main interface
   - LEDs should light up during the boot sequence

## 3.3 Firmware Configuration

### 3.3.1 System Configuration Parameters

The system configuration can be modified through the serial console or by editing config.h before upload. Key parameters include:

| Parameter | Description | Default | Range |
|-----------|-------------|---------|-------|
| `SYSTEM_NAME` | Device name | "PrimeResonance" | String |
| `SERIAL_BAUD` | Serial port speed | 115200 | 9600-921600 |
| `DISPLAY_ROTATION` | Screen orientation | 0 | 0, 1, 2, 3 |
| `DISPLAY_CONTRAST` | Screen brightness | 127 | 0-255 |
| `AUTO_CALIBRATION` | Enable auto-calibration | true | true/false |
| `COMPUTE_INTENSITY` | Computation depth | 3 | 1-5 |
| `COMPUTE_ITERATIONS` | Verification iterations | 3 | 1-10 |
| `FREQUENCY_TOLERANCE` | Acceptable oscillator deviation | 0.01f | 0.001-0.1 |
| `PHASE_THRESHOLD` | Phase detection threshold | 0.1f | 0.01-0.5 |
| `ADC_AVERAGING` | ADC sample averaging | 64 | 1-256 |

### 3.3.2 Command-Line Configuration

The firmware provides a command-line interface for real-time configuration:

1. **Connect to the serial console** at 115200 baud

2. **Use the config commands**:
   - `config list` - Show all current settings
   - `set <parameter> <value>` - Change a setting
   - `get <parameter>` - Display current value
   - `config save` - Save settings to flash memory
   - `config reset` - Restore default settings

Example commands:

```
set compute_intensity 4
set frequency_tolerance 0.02
config save
```

### 3.3.3 Advanced Configuration Files

For more complex configurations, the system supports JSON configuration files:

1. **Generate template configuration**
   - Run command: `config export template.json`

2. **Edit the JSON file**
   - Modify parameters as needed
   - Example JSON configuration:

```json
{
  "system": {
    "name": "PrimeResonance",
    "auto_calibration": true,
    "serial_baud": 115200,
    "display_rotation": 0,
    "display_contrast": 127
  },
  "compute": {
    "intensity": 3,
    "iterations": 3,
    "confidence_threshold": 0.75,
    "max_computation_time": 300
  },
  "oscillator": {
    "frequency_tolerance": 0.01,
    "calibration_interval": 3600,
    "phase_detect_samples": 100
  },
  "adc": {
    "averaging": 64,
    "sample_rate": 1000
  }
}
```

3. **Import configuration**
   - Run command: `config import my_config.json`

## 3.4 System Calibration

### 3.4.1 Oscillator Calibration

Proper calibration is essential for accurate computation:

1. **Automatic calibration**
   - System performs calibration on startup
   - Measures actual oscillator frequencies
   - Adjusts internal parameters accordingly

2. **Manual calibration**
   - Enter calibration mode: `calibration mode enter`
   - Follow on-screen instructions
   - Displays actual vs. target frequencies
   - Save calibration: `calibration save`

3. **Fine-tuning hardware oscillators**
   - Use calibration data to adjust trimmer potentiometers
   - Target frequencies: 2.0Hz, 3.0Hz, 5.0Hz
   - Acceptable tolerance: ±0.5%

### 3.4.2 ADC Calibration

The analog-to-digital converter requires calibration for accurate phase detection:

1. **Voltage reference calibration**
   - Run command: `calibration adc`
   - System measures internal reference voltage
   - Adjusts ADC readings accordingly

2. **Phase detector offset calibration**
   - Run command: `calibration phase_detector`
   - Measures baseline voltage levels
   - Compensates for component variations

### 3.4.3 System Verification

After calibration, verify overall system function:

1. **Run self-test**
   - Command: `test all`
   - Checks all subsystems
   - Reports any issues detected

2. **Verify oscillator stability**
   - Command: `oscillator stability`
   - Monitors frequency over time
   - Reports stability metrics

3. **Verify phase detection**
   - Command: `test phase_detector`
   - Measures all phase relationships
   - Confirms expected patterns

## 3.5 Basic Operation

### 3.5.1 User Interface Overview

The user interface consists of:

1. **OLED Display**: Shows current status and results
2. **Two Buttons**: For navigation and control
3. **Serial Console**: For advanced commands and data export
4. **Status LEDs**: Visual indicators of system state

### 3.5.2 Display Interface

The display shows different screens depending on the current mode:

1. **Main Screen**:
   - System status (top line)
   - Current operation (middle)
   - Navigation hints (bottom)

2. **Results Screen**:
   - Computation results
   - Confidence score
   - Elapsed time

3. **Diagnostic Screen**:
   - Oscillator frequencies
   - Phase relationships
   - System parameters

### 3.5.3 Button Controls

The two buttons provide simple navigation:

1. **Button 1 (Mode)**:
   - Short press: Cycle through available screens
   - Long press (2s): Enter menu mode

2. **Button 2 (Action)**:
   - Short press: Select/Confirm/Next
   - Long press (2s): Back/Cancel

3. **Button Combinations**:
   - Both buttons (2s): Enter system menu
   - Both buttons (5s): Reset to defaults

### 3.5.4 LED Indicators

Status LEDs provide quick visual feedback:

1. **LED 1 (Green)**:
   - Steady: System ready
   - Slow blink: Computing
   - Fast blink: Error state

2. **LED 2 (Red)**:
   - Off: Normal operation
   - On: Attention needed
   - Blinking: System busy

### 3.5.5 Basic Commands

The serial console accepts various commands:

1. **System Commands**:
   - `help` - Display available commands
   - `system status` - Show system information
   - `system reset` - Restart the system

2. **Operational Commands**:
   - `compute prime <number>` - Test if a number is prime
   - `compute factorize <number>` - Find prime factors
   - `compute prime_sequence <start> <end>` - Find primes in range

3. **Diagnostic Commands**:
   - `oscillator status` - Show oscillator information
   - `data export phase_raw 10` - Export 10 seconds of phase data
   - `visualize phase_space 17` - Display phase space visualization

## 3.6 Computation Features

### 3.6.1 Primality Testing

To determine if a number is prime:

1. **Via Serial Console**
   - Command: `compute prime <number>`
   - Example: `compute prime 17`
   - Returns: Prime status and confidence score

2. **Via Display Interface**
   - Navigate to "Test Prime" screen
   - Use buttons to input number
   - Press Action button to compute
   - Result appears with confidence score

**Example Output**:
```
Computing primality for: 17
Configuration: intensity=3, iterations=3
Phase detection active...
Analysis complete
RESULT: 17 is PRIME
Confidence: 0.982
Computation time: 3.21s
```

### 3.6.2 Factorization

To find the prime factors of a number:

1. **Via Serial Console**
   - Command: `compute factorize <number>`
   - Example: `compute factorize 15`
   - Returns: List of prime factors

2. **Via Display Interface**
   - Navigate to "Factorize" screen
   - Use buttons to input number
   - Press Action button to compute
   - Results show factors and confidence

**Example Output**:
```
Computing factors for: 15
Configuration: intensity=3, iterations=3
Phase detection active...
Analyzing resonance patterns...
RESULT: 15 = 3 × 5
Confidence: 0.975
Computation time: 2.84s
```

### 3.6.3 Prime Sequence Generation

To find all primes within a range:

1. **Via Serial Console**
   - Command: `compute prime_sequence <start> <end>`
   - Example: `compute prime_sequence 10 20`
   - Returns: List of primes in range

2. **Via Display Interface**
   - Navigate to "Prime Sequence" screen
   - Use buttons to input range
   - Press Action button to compute
   - Results show found primes

**Example Output**:
```
Computing primes in range: 10 to 20
Phase detection active...
RESULT: 4 primes found
11, 13, 17, 19
Computation time: 8.95s
```

### 3.6.4 Pattern Analysis

To analyze numerical patterns:

1. **Via Serial Console**
   - Command: `compute pattern <sequence>`
   - Example: `compute pattern 2,4,6,8,10`
   - Returns: Pattern description and next elements

**Example Output**:
```
Analyzing pattern: 2,4,6,8,10
Pattern type: Arithmetic progression
Formula: a(n) = 2n
Next elements: 12,14,16
Confidence: 0.997
Computation time: 1.52s
```

## 3.7 Data Visualization and Export

### 3.7.1 Visualization Commands

The system provides several visualization options:

1. **Phase Space Visualization**
   - Command: `visualize phase_space <number>`
   - Displays 2D or 3D phase relationships
   - Useful for understanding resonance patterns

2. **Frequency Spectrum Analysis**
   - Command: `visualize spectrum <number>`
   - Shows frequency domain representation of resonance
   - Helps identify key harmonic components

3. **Entropy Surface Visualization**
   - Command: `visualize entropy <number>`
   - Maps entropy changes during computation
   - Critical for understanding the computational process

### 3.7.2 Data Export Functions

For deeper analysis, export raw data:

1. **Phase Data Export**
   - Command: `data export phase_raw <duration>`
   - Exports raw phase detector readings
   - Format: CSV with timestamp, 3 phase values per row

2. **CSV Export**
   - Command: `export csv <data_type> <filename>`
   - Exports selected data type to CSV file
   - Example: `export csv phase_data phases.csv`

3. **JSON Export**
   - Command: `export json <data_type> <filename>`
   - Exports data in JSON format
   - Includes metadata and configuration information
   - Example: `export json prime_test results.json`

### 3.7.3 Real-time Data Streaming

For continuous monitoring:

1. **Serial Streaming**
   - Command: `stream <data_type> <interval>`
   - Outputs data at specified interval (ms)
   - Example: `stream phase_angles 100`

2. **Network Streaming (if WiFi enabled)**
   - Command: `stream network <data_type> <interval> <port>`
   - Streams data over network socket
   - Example: `stream network resonance 200 8888`

## 3.8 Advanced Features

### 3.8.1 Custom Algorithm Development

The firmware supports custom computation algorithms:

1. **Algorithm Structure**
   - Create .alg file with JSON metadata and base64-encoded code
   - Implement the required entry point function
   - Use the ResonanceEngine API for hardware access

2. **Loading Custom Algorithms**
   - Command: `compute load <filename>`
   - Example: `compute load factor_sieves.alg`

3. **Executing Custom Algorithms**
   - Command: `compute custom <algorithm> [params...]`
   - Example: `compute custom factor_sieves 123`

Example custom algorithm file structure:

```json
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
    }
  ],
  "code": "BASE64_ENCODED_CPP_CODE_HERE",
  "entrypoint": "processAlgorithm"
}
```

### 3.8.2 Configuration Profiles

For different usage scenarios:

1. **Saving Profiles**
   - Command: `config save_profile <name>`
   - Example: `config save_profile research`

2. **Loading Profiles**
   - Command: `config load_profile <name>`
   - Example: `config load_profile low_power`

3. **Managing Profiles**
   - Command: `config list_profiles`
   - Command: `config delete_profile <name>`

### 3.8.3 Advanced Analysis Tools

For research and experimentation:

1. **Compare Mode**
   - Command: `analyze compare <number1> <number2>`
   - Compares resonance patterns between numbers
   - Useful for understanding mathematical relationships

2. **Trend Analysis**
   - Command: `analyze trend <start> <end> <step>`
   - Studies patterns across a range of numbers
   - Example: `analyze trend 100 200 10`

3. **Correlation Analysis**
   - Command: `analyze correlate <param1> <param2>`
   - Studies relationships between different parameters
   - Example: `analyze correlate entropy_symbolic phase_stability`

## 3.9 Network and Multi-Node Operation

### 3.9.1 WiFi Configuration (if supported)

If your ESP32 firmware includes WiFi support:

1. **Enable WiFi**
   - Command: `set wifi_mode "station"` or `set wifi_mode "ap"`
   - Configure credentials: `set wifi_ssid "YourSSID"`
   - Set password: `set wifi_password "YourPassword"`

2. **Web Interface**
   - Enable web server: `web enable`
   - Set port: `web port 8080`
   - Access via browser at http://[device-ip]:8080

3. **REST API**
   - Enable API: `api enable`
   - Set authentication: `api auth secretkey123`
   - Access via HTTP requests to http://[device-ip]:8080/api/

### 3.9.2 Multi-Node Configuration

For distributed computing with multiple devices:

1. **Node Role Configuration**
   - Set node role: `set node_role "coordinator"` or `set node_role "worker"`
   - Set node name: `set node_name "PRC-Node1"`

2. **Network Setup**
   - Enable networking: `set network_enabled true`
   - Configure channel: `set network_channel 1`
   - Start network: `network reset`

3. **Node Discovery**
   - Scan for nodes: `network scan`
   - Check connection: `network check`
   - Monitor network: `network monitor`

4. **Distributed Computing**
   - Execute on specific node: `node2 compute prime 17`
   - Execute on all nodes: `broadcast compute prime 17`
   - Distributed operation: `compute distributed factorize 1273`

## 3.10 Troubleshooting Firmware Issues

### 3.10.1 Boot and Initialization Problems

**Issue: Firmware doesn't start**
- Check USB connection and power
- Verify serial monitor is set to 115200 baud
- Try pressing the reset button on the ESP32

**Issue: "Failed to initialize" messages**
- Check hardware connections
- Verify all components are properly connected
- Check configuration settings

### 3.10.2 Calibration Issues

**Issue: "Oscillator not detected" error**
- Verify oscillator circuits are working
- Check digital pins are correctly configured
- Try manual frequency setting: `set osc1_frequency 2.0`

**Issue: Cannot calibrate accurately**
- Allow system to warm up (5-10 minutes)
- Increase tolerance temporarily: `set frequency_tolerance 0.05`
- Try manual calibration: `calibration mode enter`

### 3.10.3 Computation Problems

**Issue: Low confidence results**
- Increase computation intensity: `set compute_intensity 4`
- Increase iterations: `set compute_iterations 5`
- Check oscillator stability
- Recalibrate system

**Issue: "Computation timeout" messages**
- Increase timeout setting: `set max_computation_time 600`
- Reduce computation intensity for faster results
- Check for hardware issues affecting performance

### 3.10.4 Serial Communication Problems

**Issue: No response from serial commands**
- Check baud rate settings
- Verify correct line ending (CR+LF)
- Try resetting the device
- Check if system is busy with computation

**Issue: Garbled serial output**
- Verify baud rate is correct (115200)
- Check USB cable quality
- Close other programs that might use the serial port

## 3.11 Firmware Updates and Maintenance

### 3.11.1 Checking Current Version

Verify your current firmware version:

```
system info
```

Output includes:
- Firmware version
- Build date
- Hardware compatibility
- Configuration status

### 3.11.2 Updating the Firmware

1. **Download the latest firmware**
   - From the repository or project website
   - Verify it's compatible with your hardware version

2. **Update Methods**:
   - Standard: Upload new firmware via Arduino IDE or PlatformIO
   - OTA (if supported): `system update` command with WiFi enabled

3. **Verify Update**
   - Check version after update: `system info`
   - Run diagnostics: `test all`

### 3.11.3 Backup and Restore

Protect your configuration and data:

1. **Backup Configuration**
   - Command: `config backup config.json`
   - Saves all settings to file

2. **Restore Configuration**
   - Command: `config restore config.json`
   - Applies saved settings

3. **Factory Reset**
   - Command: `system factory_reset`
   - Returns all settings to defaults
   - Use with caution!

## 3.12 Next Steps

With the firmware successfully installed, configured, and tested, you can now:

1. **Explore basic operations**
   - Try primality testing with different numbers
   - Experiment with factorization
   - Generate prime sequences

2. **Develop custom algorithms**
   - Study the provided examples in Appendix E
   - Create specialized algorithms for your research
   - Share your developments with the community

3. **Proceed to Section 4: Operation Guide**
   - Learn about advanced operational techniques
   - Explore research applications
   - Understand the mathematical principles in practice

Remember that firmware development is an ongoing process. Check for updates regularly and consider contributing your improvements to the project.