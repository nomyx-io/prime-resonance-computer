# Prime Resonance Computer: Parts List & Assembly Guide

## Overview

This document provides a comprehensive list of parts needed to build a Prime Resonance Computer, along with detailed assembly instructions. The design focuses on affordability (under $500 total budget) while maintaining functionality and reliability. Assembly is divided into logical sections, with testing procedures at each stage to ensure proper operation.

## Table of Contents

1. [Complete Parts List](#complete-parts-list)
2. [Tools Required](#tools-required)
3. [Power Supply Assembly](#power-supply-assembly)
4. [Oscillator Circuit Assembly](#oscillator-circuit-assembly)
5. [Phase Detector Circuit Assembly](#phase-detector-circuit-assembly)
6. [Controller Board Assembly](#controller-board-assembly)
7. [Enclosure Assembly](#enclosure-assembly)
8. [Testing Procedures](#testing-procedures)
9. [Troubleshooting Guide](#troubleshooting-guide)
10. [Final System Assembly](#final-system-assembly)

## Complete Parts List

### Core Components

| Item | Quantity | Description | Estimated Cost |
|------|----------|-------------|----------------|
| ESP32 Development Board | 1-3 | NodeMCU ESP32S or similar, dual-core, with WiFi/BT | $10-15 each |
| Perfboard/Prototype Board | 3 | 7x9cm or larger, double-sided | $8-12 |
| PCB (Optional) | 1 set | Custom PCB for cleaner build (optional) | $20-40 |
| Project Box/Enclosure | 1 | Plastic case, approximately 20x15x5cm | $15-20 |

### Oscillator Components

| Item | Quantity | Description | Estimated Cost |
|------|----------|-------------|----------------|
| NE555 Timer IC | 3 | Standard 555 timer in DIP-8 package | $3-5 |
| 10kΩ Multi-turn Trimmer | 3 | Precision adjustment potentiometer | $6-9 |
| Resistors (Through-hole) | - | Various values, 1/4W, 5% tolerance | $5-8 |
| - 47kΩ | 1 | For 5Hz oscillator | - |
| - 68kΩ | 1 | For 3Hz oscillator | - |
| - 100kΩ | 1 | For 2Hz oscillator | - |
| - 220Ω | 3 | LED current limiting | - |
| - 10kΩ | 8 | Pull-up and general use | - |
| Capacitors | - | Various values | $8-12 |
| - 2.2µF | 1 | For 5Hz oscillator, electrolytic | - |
| - 3.3µF | 1 | For 3Hz oscillator, electrolytic | - |
| - 4.7µF | 1 | For 2Hz oscillator, electrolytic | - |
| - 0.01µF | 3 | Ceramic, stabilization | - |
| - 0.1µF | 10 | Ceramic, decoupling | - |
| - 10µF | 4 | Electrolytic, power filtering | - |
| LED Indicator | 10 | 3mm or 5mm, various colors | $3-5 |

### Phase Detector Components

| Item | Quantity | Description | Estimated Cost |
|------|----------|-------------|----------------|
| CD4070 Quad XOR Gate IC | 1 | XOR gates for phase detection | $1-2 |
| LM358 Dual Op-Amp | 2 | Buffer amplifiers for phase outputs | $2-4 |
| 10kΩ Resistor | 3 | Phase detector low-pass filtering | $1-2 |
| 1µF Capacitor | 3 | Phase detector low-pass filtering | $2-3 |

### Power Supply Components

| Item | Quantity | Description | Estimated Cost |
|------|----------|-------------|----------------|
| DC Power Jack | 1 | 5.5x2.1mm panel mount | $1-2 |
| Power Switch | 1 | SPST toggle or rocker switch | $2-3 |
| 7805 Voltage Regulator | 1 | 5V regulator, TO-220 package | $1-2 |
| AMS1117-3.3V Regulator | 1 | 3.3V regulator, SOT-223 package | $1-2 |
| Terminal Blocks | 4 | 2-position screw terminals | $3-5 |
| Heat Sink | 1 | For 7805 regulator | $1-2 |
| DC Power Supply | 1 | 9V-12V, 1A, center positive | $8-12 |

### User Interface Components

| Item | Quantity | Description | Estimated Cost |
|------|----------|-------------|----------------|
| OLED Display | 1 | 128x64 I2C, SSD1306 controller | $8-10 |
| Push Buttons | 3 | Tactile switches with caps | $3-5 |
| Rotary Encoder | 1 | With push button function | $3-5 |
| WS2812B RGB LEDs (Optional) | 5 | Programmable indicators | $3-5 |

### Connectors and Wiring

| Item | Quantity | Description | Estimated Cost |
|------|----------|-------------|----------------|
| Jumper Wires | 1 pack | Various lengths, M-M, M-F, F-F | $5-8 |
| Pin Headers | 2 packs | Male and female, straight | $3-5 |
| Heat Shrink Tubing | 1 pack | Various sizes | $3-5 |
| Hookup Wire | 1 roll | 22 AWG solid core | $5-8 |
| Ribbon Cable | 1 | 10-conductor, 1ft | $3-5 |

### Hardware and Misc

| Item | Quantity | Description | Estimated Cost |
|------|----------|-------------|----------------|
| M3 Screws | 1 pack | Various lengths | $3-5 |
| M3 Standoffs | 1 pack | Various lengths | $4-6 |
| M3 Nuts | 1 pack | Standard and lock nuts | $2-3 |
| Cable Ties | 1 pack | Small zip ties | $2-3 |
| Rubber Feet | 4 | Self-adhesive | $2-3 |
| Labels/Stickers | 1 sheet | For marking controls | $3-5 |

### Optional Expansion Components

| Item | Quantity | Description | Estimated Cost |
|------|----------|-------------|----------------|
| Additional ESP32 Boards | 2 | For multi-node configuration | $20-30 |
| SD Card Module | 1 | For data logging | $3-5 |
| Real-time Clock Module | 1 | DS3231 for accurate timing | $3-5 |
| USB-to-Serial Adapter | 1 | For programming/debugging | $5-8 |

### Estimated Total Cost

| Category | Cost Range |
|----------|------------|
| Core Components | $53-87 |
| Oscillator Components | $25-39 |
| Phase Detector Components | $6-11 |
| Power Supply Components | $17-28 |
| User Interface Components | $14-20 |
| Connectors and Wiring | $19-31 |
| Hardware and Misc | $16-25 |
| Subtotal (Basic System) | $150-241 |
| Optional Expansion | $31-48 |
| **Total Cost Range** | **$181-289** |

**Note:** Prices may vary based on supplier, quantity purchased, and regional availability. Significant cost savings can be achieved by purchasing components in bulk or as part of kits.

## Tools Required

| Tool | Purpose |
|------|---------|
| Soldering Iron | Component assembly |
| Solder | Electronic connections |
| Wire Cutters | Trimming component leads |
| Wire Strippers | Preparing wires |
| Small Screwdrivers | Assembly and adjustments |
| Multimeter | Testing and troubleshooting |
| Oscilloscope (Optional) | Calibration and advanced troubleshooting |
| Tweezers | Handling small components |
| Helping Hands or PCB Holder | Holding boards during soldering |
| Desoldering Pump | Correcting soldering mistakes |
| Digital Calipers (Optional) | Precise measurements |
| USB Cable | Programming ESP32 |
| Hot Glue Gun | Securing components |
| Drill with Bits | Enclosure modifications |

## Power Supply Assembly

### Components Needed
- DC Power Jack
- Power Switch
- 7805 Voltage Regulator
- AMS1117-3.3V Regulator
- Heat Sink
- Terminal Blocks
- Various Capacitors
- Perfboard
- Hookup Wire
- Power LED indicators (optional)

### Assembly Steps

1. **Prepare the Perfboard:**
   - Cut the perfboard to approximately 5cm x 5cm
   - Plan component placement to ensure proper spacing and airflow
   - Mark the positions for key components

2. **Install the Power Input Section:**
   - Mount the DC power jack on the edge of the perfboard
   - Connect the power switch in series with the positive line from the jack
   - Install a 10μF electrolytic capacitor between the power lines (observe polarity)

3. **Install the 5V Regulator Section:**
   - Mount the 7805 regulator on the perfboard
   - Attach the heat sink to the 7805 using thermal paste or adhesive
   - Connect the input of the 7805 to the power switch output
   - Add 0.1μF ceramic capacitor near the input pin
   - Add 10μF electrolytic capacitor at the output (observe polarity)
   - Connect the ground pin to the common ground line

4. **Install the 3.3V Regulator Section:**
   - Mount the AMS1117-3.3V regulator on the perfboard
   - Connect the input of the AMS1117 to the 5V output of the 7805
   - Add 0.1μF ceramic capacitor near the input pin
   - Add 10μF electrolytic capacitor at the output (observe polarity)
   - Connect the ground pin to the common ground line

5. **Install Output Terminal Blocks:**
   - Mount terminal blocks for 5V, 3.3V, and GND connections
   - Connect terminal blocks to the appropriate power rails
   - Label the terminals clearly

6. **Add Power Indicators (Optional):**
   - Add LEDs with appropriate current-limiting resistors (220Ω)
   - Connect LEDs to 5V and 3.3V lines to indicate power presence
   - Ensure LED polarity is correct (longer lead is positive)

7. **Final Connections:**
   - Ensure all ground connections are properly tied together
   - Double-check polarity of electrolytic capacitors
   - Verify all solder joints are clean and secure
   - Trim excess component leads

### Testing the Power Supply

1. **Visual Inspection:**
   - Check for proper component placement
   - Verify absence of solder bridges or cold joints
   - Ensure capacitor polarities are correct

2. **Initial Power-Up:**
   - Set multimeter to DC voltage mode
   - Connect the specified DC power supply (9-12V)
   - Measure the voltage at the 5V terminal (should be 4.9-5.1V)
   - Measure the voltage at the 3.3V terminal (should be 3.2-3.4V)

3. **Load Testing:**
   - Connect a 100Ω resistor to the 5V output (creates ~50mA load)
   - Verify voltage remains stable under load
   - Check that the regulator doesn't overheat
   - Repeat for the 3.3V output with a 68Ω resistor

## Oscillator Circuit Assembly

### Components Needed (for each oscillator)
- NE555 Timer IC
- Timing Resistors (specific to frequency)
- Timing Capacitor (specific to frequency)
- 10kΩ Trimmer Potentiometer
- 0.01μF Ceramic Capacitor
- 220Ω Resistor
- LED Indicator
- Hookup Wire
- Perfboard

### Assembly Steps

1. **Prepare the Perfboard:**
   - Cut the perfboard to approximately 8cm x 6cm to fit all three oscillators
   - Plan layout with adequate spacing between oscillators
   - Mark positions for the three 555 timer ICs

2. **Install the 555 Timer ICs:**
   - Place each 555 IC on the perfboard, ensuring pin 1 is correctly oriented
   - Solder the ICs to the board
   - Add 0.1μF decoupling capacitor between pin 1 (GND) and pin 8 (VCC) for each IC

3. **Assemble the 2Hz Oscillator:**
   ```
   - Connect pin 8 (VCC) and pin 4 (Reset) to the 5V rail
   - Connect pin 1 (GND) to ground
   - Connect 100kΩ resistor from pin 7 (Discharge) to pin 8 (VCC)
   - Connect 10kΩ trimmer from pin 7 (Discharge) to pin 6 (Threshold)
   - Connect pin 6 (Threshold) to pin 2 (Trigger)
   - Connect 4.7μF capacitor from pin 2 (Trigger) to ground (observe polarity)
   - Connect 0.01μF capacitor from pin 5 (Control) to ground
   - Connect 220Ω resistor from pin 3 (Output) to LED anode
   - Connect LED cathode to ground
   - Connect output line from pin 3 to terminal for ESP32 connection
   ```

4. **Assemble the 3Hz Oscillator:**
   ```
   - Connect pin 8 (VCC) and pin 4 (Reset) to the 5V rail
   - Connect pin 1 (GND) to ground
   - Connect 68kΩ resistor from pin 7 (Discharge) to pin 8 (VCC)
   - Connect 10kΩ trimmer from pin 7 (Discharge) to pin 6 (Threshold)
   - Connect pin 6 (Threshold) to pin 2 (Trigger)
   - Connect 3.3μF capacitor from pin 2 (Trigger) to ground (observe polarity)
   - Connect 0.01μF capacitor from pin 5 (Control) to ground
   - Connect 220Ω resistor from pin 3 (Output) to LED anode
   - Connect LED cathode to ground
   - Connect output line from pin 3 to terminal for ESP32 connection
   ```

5. **Assemble the 5Hz Oscillator:**
   ```
   - Connect pin 8 (VCC) and pin 4 (Reset) to the 5V rail
   - Connect pin 1 (GND) to ground
   - Connect 47kΩ resistor from pin 7 (Discharge) to pin 8 (VCC)
   - Connect 10kΩ trimmer from pin 7 (Discharge) to pin 6 (Threshold)
   - Connect pin 6 (Threshold) to pin 2 (Trigger)
   - Connect 2.2μF capacitor from pin 2 (Trigger) to ground (observe polarity)
   - Connect 0.01μF capacitor from pin 5 (Control) to ground
   - Connect 220Ω resistor from pin 3 (Output) to LED anode
   - Connect LED cathode to ground
   - Connect output line from pin 3 to terminal for ESP32 connection
   ```

6. **Add Terminal Connections:**
   - Add terminal points for power (5V and GND)
   - Add terminal points for the three oscillator outputs
   - Label all terminals clearly for later reference

### Testing the Oscillators

1. **Visual Inspection:**
   - Check component placement and orientation
   - Verify all connections according to the schematic
   - Look for solder bridges or cold joints

2. **Initial Power-Up:**
   - Apply 5V to the power terminals
   - Verify that all LEDs begin flashing
   - Check that the flashing rates appear different

3. **Frequency Measurement:**
   - Using an oscilloscope or frequency counter:
     - Measure the output of the 2Hz oscillator (should be near 2Hz)
     - Measure the output of the 3Hz oscillator (should be near 3Hz)
     - Measure the output of the 5Hz oscillator (should be near 5Hz)
   - If no oscilloscope is available, count LED flashes over 30 seconds and calculate the frequency

4. **Frequency Adjustment:**
   - Adjust the 10kΩ trimmer for each oscillator to fine-tune the frequencies
   - Aim for:
     - 2Hz oscillator: 2.00 Hz ±0.05 Hz
     - 3Hz oscillator: 3.00 Hz ±0.05 Hz
     - 5Hz oscillator: 5.00 Hz ±0.05 Hz
   - Mark the trimmer positions once calibrated

## Phase Detector Circuit Assembly

### Components Needed
- CD4070 Quad XOR Gate IC
- LM358 Dual Op-Amp IC (2)
- 10kΩ Resistors (3)
- 1μF Capacitors (3)
- 0.1μF Ceramic Capacitors (for decoupling)
- Hookup Wire
- Perfboard

### Assembly Steps

1. **Prepare the Perfboard:**
   - Cut the perfboard to approximately 6cm x 6cm
   - Plan component layout with the CD4070 and LM358 ICs
   - Mark positions for the three phase detector circuits

2. **Install the ICs:**
   - Place the CD4070 on the perfboard, ensuring pin 1 is correctly oriented
   - Place the two LM358 ICs on the perfboard
   - Solder the ICs to the board
   - Add 0.1μF decoupling capacitor between power and ground pins of each IC

3. **Assemble the 2Hz-3Hz Phase Detector:**
   ```
   - Connect CD4070 pin 14 (VCC) to the 5V rail
   - Connect CD4070 pin 7 (GND) to ground
   - Connect 2Hz oscillator output to CD4070 pin 1 (Input A)
   - Connect 3Hz oscillator output to CD4070 pin 2 (Input B)
   - Connect CD4070 pin 3 (Output) to a 10kΩ resistor
   - Connect the other end of the 10kΩ resistor to one lead of a 1μF capacitor
   - Connect the other lead of the 1μF capacitor to ground
   - Connect the junction of the resistor and capacitor to LM358 pin 3 (non-inverting input)
   - Connect LM358 pin 2 (inverting input) to LM358 pin 1 (output)
   - Connect LM358 pin 1 (output) to a terminal for ESP32 connection
   ```

4. **Assemble the 2Hz-5Hz Phase Detector:**
   ```
   - Connect 2Hz oscillator output to CD4070 pin 5 (Input A)
   - Connect 5Hz oscillator output to CD4070 pin 6 (Input B)
   - Connect CD4070 pin 4 (Output) to a 10kΩ resistor
   - Connect the other end of the 10kΩ resistor to one lead of a 1μF capacitor
   - Connect the other lead of the 1μF capacitor to ground
   - Connect the junction of the resistor and capacitor to LM358 pin 5 (non-inverting input)
   - Connect LM358 pin 6 (inverting input) to LM358 pin 7 (output)
   - Connect LM358 pin 7 (output) to a terminal for ESP32 connection
   ```

5. **Assemble the 3Hz-5Hz Phase Detector:**
   ```
   - Connect 3Hz oscillator output to CD4070 pin 8 (Input A)
   - Connect 5Hz oscillator output to CD4070 pin 9 (Input B)
   - Connect CD4070 pin 10 (Output) to a 10kΩ resistor
   - Connect the other end of the 10kΩ resistor to one lead of a 1μF capacitor
   - Connect the other lead of the 1μF capacitor to ground
   - Connect the junction of the resistor and capacitor to second LM358 pin 3 (non-inverting input)
   - Connect second LM358 pin 2 (inverting input) to LM358 pin 1 (output)
   - Connect second LM358 pin 1 (output) to a terminal for ESP32 connection
   ```

6. **Add Terminal Connections:**
   - Add terminal points for power (5V and GND)
   - Add terminal points for the three oscillator inputs
   - Add terminal points for the three phase detector outputs
   - Label all terminals clearly

### Testing the Phase Detectors

1. **Visual Inspection:**
   - Check component placement and orientation
   - Verify all connections according to the schematic
   - Look for solder bridges or cold joints

2. **Initial Power-Up:**
   - Apply 5V to the power terminals
   - Verify that the ICs don't overheat

3. **Functionality Testing:**
   - Connect the oscillators to the phase detector inputs
   - Using an oscilloscope or multimeter:
     - Measure the output of each phase detector
     - The outputs should be varying DC voltages between 0V and 5V
     - The voltages should fluctuate as the oscillators drift in and out of phase
   - If using a multimeter, set it to DC voltage mode and observe the changing values

4. **Signal Verification:**
   - With an oscilloscope, verify that the raw XOR output shows digital pulses
   - Verify that the RC filter converts these pulses to an analog voltage
   - Verify that the op-amp buffer provides a stable output

## Controller Board Assembly

### Components Needed
- ESP32 Development Board
- OLED Display (128x64, I2C interface)
- Push Buttons (3)
- Rotary Encoder with Push Button
- 10kΩ Pull-up Resistors (for buttons and encoder)
- 4.7kΩ Pull-up Resistors (for I2C lines)
- 0.1μF Ceramic Capacitors
- Hookup Wire
- Jumper Wires
- Pin Headers
- Perfboard

### Assembly Steps

1. **Prepare the Perfboard:**
   - Cut the perfboard to approximately 10cm x 8cm
   - Plan layout with the ESP32 board in the center
   - Mark positions for the OLED display and user controls

2. **Mount the ESP32 Board:**
   - Install female pin headers on the perfboard for the ESP32
   - Mount the ESP32 board onto the headers (allows removal if needed)
   - Connect 0.1μF decoupling capacitor between 3.3V and GND

3. **Install OLED Display:**
   - Mount the OLED display on the edge of the perfboard for visibility
   - Connect the I2C lines:
     - OLED GND to ESP32 GND
     - OLED VCC to ESP32 3.3V
     - OLED SCL to ESP32 pin 22 (GPIO22)
     - OLED SDA to ESP32 pin 21 (GPIO21)
   - Add 4.7kΩ pull-up resistors on SCL and SDA lines

4. **Install User Controls:**
   - Mount the three push buttons on the perfboard
   - Connect one side of each button to GND
   - Connect the other side of each button to:
     - Button 1: ESP32 pin 13 (GPIO13)
     - Button 2: ESP32 pin 12 (GPIO12)
     - Button 3: ESP32 pin 14 (GPIO14)
   - Add 10kΩ pull-up resistors between each button input and 3.3V

5. **Install Rotary Encoder:**
   - Mount the rotary encoder on the perfboard
   - Connect the encoder pins:
     - Encoder GND to ESP32 GND
     - Encoder A to ESP32 pin 17 (GPIO17)
     - Encoder B to ESP32 pin 16 (GPIO16)
     - Encoder Button to ESP32 pin 4 (GPIO4)
   - Add 10kΩ pull-up resistors to pins A, B, and Button

6. **Connect Oscillator and Phase Detector Inputs:**
   - Create terminals for the oscillator outputs:
     - 2Hz oscillator to ESP32 pin 25 (GPIO25)
     - 3Hz oscillator to ESP32 pin 26 (GPIO26)
     - 5Hz oscillator to ESP32 pin 27 (GPIO27)
   - Create terminals for the phase detector outputs:
     - 2Hz-3Hz detector to ESP32 pin 34 (GPIO34, ADC6)
     - 2Hz-5Hz detector to ESP32 pin 35 (GPIO35, ADC7)
     - 3Hz-5Hz detector to ESP32 pin 32 (GPIO32, ADC4)

7. **Add Power Connections:**
   - Connect ESP32 VIN to 5V power supply
   - Connect ESP32 GND to power supply ground
   - Add additional power terminals for accessories

8. **Add Status LEDs (Optional):**
   - Add LED with 220Ω resistor to ESP32 pin 2 (GPIO2) for status indication
   - Add additional indicator LEDs as needed

### Testing the Controller Board

1. **Visual Inspection:**
   - Check component placement and orientation
   - Verify all connections according to the schematic
   - Look for solder bridges or cold joints

2. **Initial Power-Up:**
   - Apply power to the ESP32 board
   - Verify the board boots up (power LED illuminates)
   - Check for any signs of overheating or shorts

3. **OLED Display Test:**
   - Upload a simple test program to initialize the OLED display
   - Verify the display shows text or graphics
   - Check for proper contrast and orientation

4. **Button and Encoder Test:**
   - Upload a test program that reads the buttons and encoder
   - Press each button and verify it registers
   - Turn the encoder and verify it counts up and down
   - Test the encoder push button

5. **Input/Output Test:**
   - Upload a test program that reads analog inputs and controls digital outputs
   - Connect test signals to the analog inputs and verify readings
   - Toggle digital outputs and verify with multimeter or LEDs

## Enclosure Assembly

### Components Needed
- Plastic Project Box (approximately 20x15x5cm)
- M3 Screws and Standoffs
- Panel-mount DC Jack
- Panel-mount Power Switch
- Panel-mount Buttons (optional)
- Panel-mount Rotary Encoder (optional)
- Rubber Feet
- Labels/Stickers
- Cable Ties
- Mounting Hardware

### Assembly Steps

1. **Prepare the Enclosure:**
   - Measure and mark holes for:
     - DC power jack
     - Power switch
     - OLED display
     - User controls (if panel-mounted)
     - Ventilation (if needed)
   - Drill and cut the marked holes
   - Deburr all holes and edges
   - Clean the enclosure thoroughly

2. **Mount External Components:**
   - Install the DC power jack
   - Install the power switch
   - Install panel-mount buttons if used
   - Install panel-mount rotary encoder if used
   - Apply labels next to controls

3. **Prepare Internal Mounting:**
   - Install M3 standoffs at appropriate locations for circuit boards
   - Plan cable routing to minimize interference
   - Add additional standoffs for cable management

4. **Install Power Supply Board:**
   - Mount the power supply board using M3 screws
   - Connect the DC jack to the power input
   - Connect the power switch to the circuit

5. **Install Oscillator Board:**
   - Mount the oscillator board using M3 screws
   - Connect power lines from the power supply
   - Route output wires to the phase detector board

6. **Install Phase Detector Board:**
   - Mount the phase detector board using M3 screws
   - Connect power lines from the power supply
   - Connect inputs from the oscillator board
   - Route output wires to the controller board

7. **Install Controller Board:**
   - Mount the controller board using M3 screws
   - Position so the OLED display aligns with the display cutout
   - Connect power lines from the power supply
   - Connect inputs from the phase detector board
   - Connect any panel-mounted controls

8. **Cable Management:**
   - Secure all internal wiring using cable ties
   - Ensure no wires can interfere with moving parts
   - Provide strain relief for external connections

9. **Final Assembly:**
   - Double-check all connections before closing
   - Secure the enclosure with screws
   - Attach rubber feet to the bottom of the enclosure
   - Apply any remaining labels or decorations

### Testing the Assembled Enclosure

1. **Visual Inspection:**
   - Check that all components are securely mounted
   - Verify no wires are pinched or strained
   - Ensure all external controls are accessible

2. **Functional Testing:**
   - Power up the system
   - Test all external controls
   - Verify display visibility and readability
   - Check for proper ventilation during operation

## Testing Procedures

### Power System Tests

1. **Voltage Test:**
   - Measure voltage at all power distribution points:
     - Input: 9-12V DC
     - 5V rail: 4.9-5.1V
     - 3.3V rail: 3.2-3.4V
   - Check voltage stability under load

2. **Current Consumption Test:**
   - Measure total system current draw (typically 150-300mA)
   - Verify current is within power supply capability
   - Check for any abnormal current spikes

### Oscillator Tests

1. **Frequency Test:**
   - Measure each oscillator frequency:
     - 2Hz oscillator: 2.00 Hz ±0.05 Hz
     - 3Hz oscillator: 3.00 Hz ±0.05 Hz
     - 5Hz oscillator: 5.00 Hz ±0.05 Hz
   - Record baseline frequencies for future reference

2. **Waveform Test:**
   - Using an oscilloscope, verify square wave output
   - Check duty cycle (should be approximately 50%)
   - Verify signal rises from 0V to >4.5V

3. **Stability Test:**
   - Run oscillators for at least 1 hour
   - Monitor frequency drift over time
   - Verify drift is less than ±0.05 Hz

### Phase Detector Tests

1. **Voltage Range Test:**
   - Verify output voltage range is approximately 0-3.3V
   - Check that full phase range (0-360°) maps to voltage range

2. **Phase Relationship Test:**
   - Observe voltage changes as oscillators drift in and out of phase
   - Verify voltage corresponds to phase difference between signals

3. **Response Time Test:**
   - Observe how quickly phase detector responds to input changes
   - Verify no excessive lag in phase detection

### Controller System Tests

1. **ESP32 Functionality Test:**
   - Verify serial communication works
   - Test WiFi connectivity if used
   - Check all GPIO pins used in the project

2. **Display Test:**
   - Verify all screen sections display correctly
   - Check visibility from different angles
   - Test all UI screens and transitions

3. **User Input Test:**
   - Verify all buttons register correctly
   - Test rotary encoder functionality
   - Ensure no false triggers or missed inputs

### System Integration Tests

1. **End-to-End Test:**
   - Run the complete system with all components
   - Verify data flows correctly from oscillators to display
   - Check that phase relationships are detected and processed

2. **Calibration Test:**
   - Test the system's calibration procedure
   - Verify oscillators can be fine-tuned to correct frequencies
   - Check calibration persistence after power cycling

3. **Prime Detection Test:**
   - Test the system with known prime factors
   - Verify detection algorithm correctly identifies primes
   - Check confidence levels for detected primes

## Troubleshooting Guide

### Power Supply Issues

| Problem | Possible Causes | Solution |
|---------|----------------|----------|
| No power | Power supply not connected, switch off | Check connections, verify power supply works |
| Low voltage on 5V rail | Excessive load, regulator overheating | Check for shorts, add heat sink, reduce load |
| Low voltage on 3.3V rail | Input voltage too low, excessive load | Check 5V rail, verify 3.3V regulator connection |
| Unstable voltage | Poor filtering, loose connections | Add capacitors, check solder joints |
| Overheating regulator | Excessive current, insufficient cooling | Add heat sink, check for shorts |

### Oscillator Issues

| Problem | Possible Causes | Solution |
|---------|----------------|----------|
| Oscillator not running | Incorrect wiring, bad component | Check connections, verify component values |
| Frequency too high/low | Incorrect RC values, trimmer misadjusted | Adjust trimmer, replace timing components |
| Unstable frequency | Temperature drift, power supply noise | Improve cooling, add filtering capacitors |
| Duty cycle not 50% | Component tolerance issues | Fine-tune with trimmer, replace timing components |
| Insufficient amplitude | Supply voltage issue, load too high | Check power supply, reduce load |

### Phase Detector Issues

| Problem | Possible Causes | Solution |
|---------|----------------|----------|
| No output voltage | Missing input signal, incorrect wiring | Check oscillator outputs, verify connections |
| Output always high/low | Input signal issue, XOR gate fault | Check input signals, replace CD4070 chip |
| Noisy output | Insufficient filtering, ground loops | Add filtering, improve ground connections |
| Limited voltage range | Op-amp power issue, RC values incorrect | Check op-amp power, adjust RC values |
| Non-linear response | RC filter issue, op-amp saturation | Adjust RC values, check op-amp circuit |

### Controller Board Issues

| Problem | Possible Causes | Solution |
|---------|----------------|----------|
| ESP32 won't boot | Power issue, boot pin pulled low | Check power supply, verify GPIO0 not held low |
| Display not working | I2C issues, incorrect address | Check connections, verify I2C address |
| Buttons not registering | Pull-up resistor issues, wiring | Check pull-up resistors, verify pin connections |
| ADC readings incorrect | Reference voltage issue, input range | Check 3.3V reference, adjust input scaling |
| System crashes | Power instability, code issues | Improve power filtering, debug code |

### Software Issues

| Problem | Possible Causes | Solution |
|---------|----------------|----------|
| Upload fails | Connection issue, wrong board selected | Check USB connection, verify board settings |
| WiFi won't connect | Incorrect credentials, signal issues | Verify credentials, check signal strength |
| Inconsistent readings | Sampling rate issues, buffer overflow | Adjust sampling rate, optimize code |
| High CPU usage | Inefficient code, blocking operations | Use non-blocking code, optimize algorithms |
| Memory leaks | Improper memory management | Check dynamic allocations, use static when possible |

## Final System Assembly

### Pre-Assembly Checklist

1. **Verify All Subassemblies:**
   - Power supply board fully tested
   - Oscillator board calibrated and tested
   - Phase detector board tested
   - Controller board programmed and tested

2. **Prepare Installation Materials:**
   - All mounting hardware organized
   - Cable routing plan established
   - Labels and documentation ready

3. **Prepare Tools:**
   - Appropriate screwdrivers
   - Wire cutters/strippers if needed
   - Multimeter for final checks

### Assembly Procedure

1. **Install Power Supply:**
   - Mount power supply board in position
   - Connect DC jack and power switch
   - Secure all power distribution wiring
   - Label power terminals

2. **Install Oscillator Board:**
   - Mount oscillator board in position
   - Connect power lines
   - Verify oscillator outputs are properly labeled
   - Double-check trimmer positions

3. **Install Phase Detector Board:**
   - Mount phase detector board in position
   - Connect power lines
   - Connect inputs from oscillator board
   - Label detector outputs

4. **Install Controller Board:**
   - Mount ESP32 and associated components
   - Connect power lines
   - Connect OLED display and position properly
   - Connect all inputs from phase detectors
   - Connect user interface controls

5. **Cable Management:**
   - Route all cables neatly using cable ties
   - Ensure no interference between analog and digital signals
   - Provide strain relief where needed
   - Label critical connections

6. **Close Enclosure:**
   - Verify all components are secured
   - Check for pinched wires or loose connections
   - Secure enclosure with screws
   - Apply external labels

### Final System Test

1. **Power-On Test:**
   - Apply power to the system
   - Verify all boards receive proper voltage
   - Check for any unusual heat or noise

2. **Functionality Test:**
   - Verify oscillators are running at correct frequencies
   - Check phase detectors respond to phase changes
   - Verify ESP32 processes data correctly
   - Test all user interface features

3. **System Performance Test:**
   - Run the prime detection algorithm
   - Verify resonance patterns are detected
   - Test system stability over extended operation
   - Document baseline performance metrics

4. **Final Adjustments:**
   - Make any necessary calibration adjustments
   - Fine-tune software parameters if needed
   - Update documentation with final settings
   - Prepare user operation manual

The completed Prime Resonance Computer should now be fully assembled and operational. The system can detect prime resonance patterns and provide insight into the fascinating world of prime number relationships and entropic resonance phenomena.