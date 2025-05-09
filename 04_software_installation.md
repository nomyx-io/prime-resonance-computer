# Software Installation and Configuration

## Introduction

This section guides you through the process of setting up the development environment, installing firmware on your Prime Resonance Computer nodes, configuring the system, and performing the necessary calibration procedures to ensure accurate operation.

## Development Environment Setup

### Software Requirements

To program and configure your Prime Resonance Computer, you'll need:

1. **Arduino IDE** (version 2.0 or later)
   - The firmware is based on the Arduino framework for simplicity and accessibility
   - Download from [arduino.cc](https://www.arduino.cc/en/software)

2. **ESP32 Board Support**
   - This adds ESP32 compatibility to the Arduino IDE
   - Follow these steps to install:
     - Open Arduino IDE
     - Go to File > Preferences
     - Add this URL to Additional Boards Manager URLs:
       ```
       https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
       ```
     - Go to Tools > Board > Boards Manager
     - Search for "ESP32" and install "ESP32 by Espressif Systems"

3. **Required Libraries**
   - These libraries provide necessary functionality for the firmware
   - Install through Arduino IDE (Tools > Manage Libraries):
     - Adafruit GFX (for display graphics)
     - Adafruit SSD1306 (for OLED display)
     - ArduinoJson (version 6.x or later)
     - ESP-NOW (included with ESP32 package)

### Alternative Development Options

If you prefer not to use the Arduino IDE, you can also use:

1. **PlatformIO**
   - A more professional development environment
   - Install as an extension in Visual Studio Code
   - Create a new project with ESP32 platform
   - Add required libraries to platformio.ini

2. **ESP-IDF**
   - Espressif's official development framework
   - Provides lower-level control but requires more expertise
   - Suitable for advanced users who need maximum performance

## Firmware Installation

### Download the Firmware

1. **Clone or download the repository**
   ```bash
   git clone https://github.com/prime-resonance/prime-computer-firmware.git
   ```
   
   Alternatively, download the zip file from the project repository or use the files provided in the `firmware` directory of this guide.

### Configure the Firmware

Before uploading, you need to configure the firmware for each node:

1. **Open the project**
   - In Arduino IDE: File > Open > Navigate to the downloaded firmware > Select `main.ino`
   - In PlatformIO: Open the project folder

2. **Edit the configuration file**
   - Open `config.h`
   - Modify the following parameters for each node (creating a separate copy for each):

   ```cpp
   // Node configuration
   #define NODE_ID 1                // Change to 1, 2, or 3 for each node
   #define IS_COORDINATOR true      // Set to true only for Node 1, false for others
   
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

3. **Configure network peer addresses**
   - Inside the `main.ino` file, locate the network initialization section
   - Add the MAC addresses of your ESP32 modules
   
   ```cpp
   // For coordinator node:
   void setupNetwork() {
     networkManager.begin();
     
     // Add peer nodes (use actual MAC addresses from your ESP32 devices)
     uint8_t peer1[] = {0x24, 0x6F, 0x28, 0xB1, 0xC3, 0x54}; // Node 2 MAC
     uint8_t peer2[] = {0x24, 0x6F, 0x28, 0xB2, 0xD7, 0x18}; // Node 3 MAC
     
     networkManager.addPeer(peer1);
     networkManager.addPeer(peer2);
   }
   
   // For non-coordinator nodes:
   void setupNetwork() {
     networkManager.begin();
     
     // Add only coordinator node as peer
     uint8_t coordinator[] = {0x24, 0x6F, 0x28, 0xA5, 0xE2, 0x9F}; // Node 1 MAC
     
     networkManager.addPeer(coordinator);
   }
   ```

### Finding MAC Addresses

To find the MAC address of each ESP32:

1. Upload this simple sketch to each ESP32:

   ```cpp
   #include <WiFi.h>
   
   void setup() {
     Serial.begin(115200);
     WiFi.mode(WIFI_MODE_STA);
     
     Serial.println();
     Serial.print("ESP32 MAC Address: ");
     Serial.println(WiFi.macAddress());
   }
   
   void loop() {
     // Nothing here
   }
   ```

2. Open Serial Monitor at 115200 baud
3. Note the MAC address displayed for each device
4. Update the firmware configuration with these addresses

### Upload the Firmware

1. **Connect your ESP32**
   - Connect the first ESP32 node to your computer via USB
   - Select the correct board in Arduino IDE:
     - Tools > Board > ESP32 Arduino > ESP32 Dev Module
   - Select the correct port:
     - Tools > Port > (Select the COM port or /dev/tty device where ESP32 is connected)

2. **Upload the configured firmware**
   - Click the Upload button in Arduino IDE
   - Or run `platformio run --target upload` if using PlatformIO
   - Wait for the upload to complete

3. **Verify the installation**
   - Open Serial Monitor at 115200 baud
   - You should see initialization messages from the firmware
   - The OLED display should show the startup screen

4. **Repeat for other nodes**
   - Modify the configuration for each node (NODE_ID and IS_COORDINATOR)
   - Connect each ESP32 and upload its specific firmware version

## System Configuration

### Basic Configuration

After installing the firmware, you can configure additional settings through either:

1. **Serial Console** - Connect via USB and use the built-in command interface
2. **Web Interface** - Connect to the access point generated by the coordinator node

#### Serial Console Configuration

1. Connect to the device with a serial monitor at 115200 baud
2. Type `help` to see available commands
3. Use the following commands to configure settings:

```
set nodename "Prime Node 1"      # Set a custom name for this node
set oscillator1_trim 0.05        # Fine-tune oscillator frequencies
set phase_threshold 0.25         # Adjust phase detection sensitivity
set display_brightness 200       # Set OLED brightness (0-255)
save                            # Save configuration to flash memory
reboot                          # Restart the node to apply changes
```

#### Web Interface Configuration

1. Power on the coordinator node while holding the MODE button
2. Connect to the WiFi network "PrimeComputer_Config" (password: "primeconfig")
3. Open a web browser and navigate to http://192.168.4.1
4. Use the web interface to modify settings
5. Click "Save Configuration" when finished
6. Reboot the device to apply changes

### Advanced Configuration Options

The following advanced settings can be configured through either interface:

| Setting | Description | Default | Range |
|---------|-------------|---------|-------|
| Oscillator Trim 1-3 | Fine adjustment for oscillators | 0.0 | -5.0 to 5.0 |
| Phase Threshold | Detection threshold | 0.2 | 0.01-0.5 |
| Phase Stability | Required stability time (ms) | 1000 | 100-10000 |
| Network Timeout | Peer timeout (ms) | 5000 | 1000-30000 |
| Entropy Weight | Impact of entropy injection | 0.1 | 0.0-1.0 |

## Calibration Procedure

Calibration is essential to ensure your Prime Resonance Computer functions properly. The system provides both automated and manual calibration options.

### Automated Calibration

1. **Enter calibration mode**
   - Power on the node while holding the CALIBRATE button
   - Or enter `calibrate` in the serial console
   - The display will show "Calibration Mode"

2. **Run automatic calibration**
   - Select "Auto Calibrate" on the display using navigation buttons
   - Or enter `autocalibrate` in the serial console
   - The system will:
     - Measure oscillator frequencies
     - Adjust trim potentiometers using feedback algorithm
     - Test phase detector readings
     - Verify entropy source

3. **Save calibration**
   - When calibration completes successfully, select "Save & Exit"
   - Or enter `save` in the serial console
   - Calibration values will be stored in flash memory

### Manual Calibration

For more precise control, you can calibrate each component individually:

#### Oscillator Calibration

1. Connect an oscilloscope or frequency counter to the oscillator output test point
2. Enter `calibrate oscillator 1` in the serial console
3. Adjust the oscillator's trim potentiometer until the frequency matches the target (2Hz for OSC1)
4. Enter the measured frequency: `set oscillator1_freq 2.00`
5. Repeat for oscillators 2 (3Hz) and 3 (5Hz)

#### Phase Detector Calibration

1. Enter `calibrate phase` in the serial console
2. The system will force oscillators to fixed frequencies temporarily
3. Monitor the phase detector outputs (ADC readings)
4. Adjust phase offset potentiometers until ADC reads mid-range (~2048)
5. Enter `done` when finished

#### Entropy Source Verification

1. Enter `test entropy` in the serial console
2. The system will sample the entropy source repeatedly
3. Verify outputs show good randomness (chi-squared test)
4. Results should show "PASS" for entropy quality

### Network Calibration

After calibrating each node individually, perform network calibration:

1. Power up all three nodes
2. On the coordinator node, enter `network sync` in the serial console
3. The coordinator will establish connections with all nodes
4. Verify that all nodes appear in the network list
5. Test communication with `network ping all`
6. All nodes should respond with round-trip times less than 100ms

## Verification and Testing

### System Self-Test

Run a comprehensive self-test on each node:

1. Enter `selftest` in the serial console
2. The system will test:
   - Oscillator function and stability
   - Phase detector operation
   - Entropy source quality
   - Memory integrity
   - Network connectivity
   - Display functionality

3. All tests should pass with "OK" status

### Simple Computation Test

Verify computational capabilities:

1. Enter `compute test` in the serial console
2. The system will run a simple prime number verification test
3. Results should match expected outputs provided in the test documentation
4. LED indicators will show computation activity

## Troubleshooting Software Issues

### Common Issues

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| Upload fails | Incorrect board selection | Verify ESP32 Dev Module is selected |
| | Connection issue | Check USB cable and port |
| | ESP32 in boot loop | Hold BOOT button while uploading |
| OLED not displaying | Connection issue | Check I2C wiring and pull-up resistors |
| | Wrong I2C address | Try alternative address (0x3C or 0x3D) |
| Network not connecting | Wrong MAC addresses | Verify MAC addresses in configuration |
| | Channel interference | Try different WiFi channel |
| Unstable readings | Noise interference | Improve power filtering, separate analog/digital grounds |
| | Code timing issues | Reduce loop frequency, increase averaging |

### Serial Debug Output

Enable detailed debug output for troubleshooting:

1. In `config.h`, set:
   ```cpp
   #define DEBUG_LEVEL 2  // 0=none, 1=errors, 2=warnings, 3=info, 4=verbose
   ```

2. Recompile and upload firmware
3. Monitor the Serial output at 115200 baud
4. Look for error messages or warnings

### Firmware Recovery

If the device becomes unresponsive:

1. Press and hold RESET button while connecting USB
2. Use ESP32 Flash Download Tool to restore firmware
3. Flash the blank.bin file to address 0x1000
4. Then upload the full firmware again

## Next Steps

After successfully installing, configuring, and calibrating the firmware on all three nodes, you're ready to move on to the Operation and Usage section, which will teach you how to use your Prime Resonance Computer for various computational tasks.

Before proceeding, ensure:

1. All nodes have proper firmware installed
2. All oscillators are calibrated to their prime frequencies
3. Network communication is established between nodes
4. Self-tests pass successfully

In the next section, you'll learn how to operate your Prime Resonance Computer and run various computational tasks.