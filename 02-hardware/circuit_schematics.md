# Prime Computer Circuit Schematics

## Overview

This document provides detailed circuit schematics for all components of the Prime Resonance Computer. These schematics can be used in conjunction with the [Assembly Guide](assembly_guide.md) and [Parts List](parts_list.md) to build a complete system. The schematics are provided in both graphical format and as component-level descriptions for maximum clarity.

## Table of Contents

1. [System Block Diagram](#system-block-diagram)
2. [Power Supply Circuit](#power-supply-circuit)
3. [Prime Oscillator Circuits](#prime-oscillator-circuits)
4. [Phase Detector Circuits](#phase-detector-circuits)
5. [Entropy Source Circuit](#entropy-source-circuit)
6. [ESP32 Controller Connections](#esp32-controller-connections)
7. [Display Interface](#display-interface)
8. [Network Interface](#network-interface)
9. [Complete Node Schematic](#complete-node-schematic)
10. [PCB Layout Guidelines](#pcb-layout-guidelines)

## System Block Diagram

The complete Prime Computer consists of three interconnected nodes with the following architecture:

```
                    +-----------------+
                    | COORDINATOR NODE|
                    | (NODE 1)        |
                    |                 |
                    | +-------------+ |         +----------------+
                    | | Prime       | |         | NODE 2         |
                    | | Oscillators | |         |                |
                    | +------+------+ |         | +------------+ |
                    |        |        | <-----> | | Prime      | |
                    | +------+------+ |         | | Oscillators| |
                    | | Phase       | |         | +-----+------+ |
                    | | Detectors   | |         |       |        |
                    | +------+------+ |         | +-----+------+ |
                    |        |        |         | | Phase      | |
+----------------+  | +------+------+ |         | | Detectors  | |
| USER INTERFACE |--| | ESP32       | |         | +-----+------+ |
|                |  | | Controller  | |         |       |        |
| - Display      |  | +------+------+ |         | +-----+------+ |
| - Controls     |  |        |        |         | | ESP32      | |
+----------------+  | +------+------+ |         | | Controller | |
                    | | Network     | |         | +-----+------+ |
                    | | Interface   | |         |       |        |
                    | +-------------+ |         | +-----+------+ |
                    +-----------------+         | | Network    | |
                              ^                 | | Interface  | |
                              |                 | +------------+ |
                              |                 +----------------+
                              |                          ^
                              |                          |
                              v                          |
                    +-----------------+                  |
                    | NODE 3          |                  |
                    |                 |                  |
                    | +-------------+ |                  |
                    | | Prime       | |                  |
                    | | Oscillators | |                  |
                    | +------+------+ |                  |
                    |        |        | <----------------+
                    | +------+------+ |
                    | | Phase       | |
                    | | Detectors   | |
                    | +------+------+ |
                    |        |        |
                    | +------+------+ |
                    | | ESP32       | |
                    | | Controller  | |
                    | +------+------+ |
                    |        |        |
                    | +------+------+ |
                    | | Network     | |
                    | | Interface   | |
                    | +-------------+ |
                    +-----------------+
```

## Power Supply Circuit

### Schematic

```
         +-------+      +-------+
AC Wall  |       |      |       |
Outlet   |  5V   |      |  3.3V |
 ------->|  Power|----->| LDO   |----> 3.3V (to digital circuits)
         |  Adapter    |  Reg   |
         |       |      |       |
         +-------+      +---+---+
                            |
                            v
                           GND
```

### Detailed Power Supply Schematic

```
                                       +-------+
       +----+             +----+       |       |
5V In -+    +----- R1 ----+    +---+--+ VIN   |
       | SW |             | FB |   |  |       |    +--+
       +----+             +----+   |  |       |    |  |
         Power              Ferrite C1 | AMS1117 +--+ C3 +-- 3.3V Out
         Switch              Bead   |  |       |    |  |
                                    |  |       |    +--+
                                    |  |       |
                                   GND | GND   |    +--+
                                       |       +----+  |
                                       +-------+    | C4 +-- GND
                                                    |  |
                                                    +--+
```

**Components:**
- SW: Power switch
- R1: 10Ω resistor (current limiter)
- FB: Ferrite bead (noise suppression)
- C1: 10μF electrolytic capacitor (input filtering)
- C2: 0.1μF ceramic capacitor (input filtering)
- AMS1117: 3.3V voltage regulator
- C3: 10μF electrolytic capacitor (output filtering)
- C4: 0.1μF ceramic capacitor (output filtering)

**Specifications:**
- Input Voltage: 5V DC
- Output Voltage: 3.3V DC
- Maximum Current: 800mA
- Ripple: < 50mV
- Protection: Overcurrent, thermal shutdown

## Prime Oscillator Circuits

Each node contains three oscillators tuned to prime number frequencies (2 Hz, 3 Hz, and 5 Hz).

### Basic 555 Timer Astable Circuit

```
                          VCC
                           |
                           v
                          +-+
                          | |
                          | | R1
                          | |
                          +-+
                           |
                           +----------------+
                           |                |
                           |            8   |
                +-+        +------7     +---+
                | |               |     |
                | | R2            |     |
                | |               |   555
                +-+               |     |
                 |                |     |
                 +------6---------+     |
                 |                |     |
                 |                |     |
                 |                |     |
          +------+------+         |     |
          |      |      |         |     |
          |    +-+      |         |     |
          |    | |      |         |     |
          |    | | R3   |       3 |     |
          |    | |      +---------+-----+---> Output
          |    +-+      |         |
          |      |      |       2 |
          +------+------+----+----+
                 |           |    |
                 |         +-+    |
                 |         | |    |
                 |         | | C1 |
                 |         | |    |
                 |         +-+    |
                 |           |    |
                 |           |  1 |
                 |           +----+
                 |                |
                 |                v
                 +----------------+--- GND
```

**Components for each oscillator:**
- U1: 555 Timer IC
- R1: 10kΩ resistor
- R2: 4.7kΩ resistor
- R3: 10kΩ trimmer potentiometer
- C1: 0.01μF to 10μF capacitor (selected for desired frequency)
- C2: 0.1μF capacitor (connected to pin 5, not shown)
- C3: 0.1μF capacitor (power supply bypass)

**Oscillator Settings:**

| Oscillator | Frequency | R1 | R2 | C1 |
|------------|-----------|-----|-----|-----|
| OSC1 (2 Hz) | 2 Hz | 10kΩ | 100kΩ | 4.7μF |
| OSC2 (3 Hz) | 3 Hz | 10kΩ | 56kΩ | 4.7μF |
| OSC3 (5 Hz) | 5 Hz | 10kΩ | 30kΩ | 4.7μF |

Frequency calculation: f = 1.44 / ((R1 + 2×R2) × C1)

### Precision Version with LM311 Comparator

For more stable operation, an alternative precision oscillator:

```
                      VCC
                       |
                       v
                      +-+
                      | |
                      | | R1
                      | |
                      +-+
                       |
              +--------+--------+
              |        |        |
              |      +-+        |
              |      | |        |
           +--+      | | R2     |
           |  |      | |        |
           |  |      +-+        |
      C1   |  |        |        |
       ||  |  |        |   3    |
       ||  +--+--------+---+    |
       ||     |            |    |
       ||     |       LM311|    |
       ||     |            |    |
       ||     |        +---+    |
       ||     |        |   |    |
       ++-----+--------+   |    |
       ||                  |    |
       ||                  |    |
       ||                  |    |
       GND                 +----+--> Output
                                |
                                v
                               GND
```

**Components:**
- U1: LM311 comparator
- R1: 10kΩ resistor
- R2: Precision resistor (calculated for frequency)
- C1: Precision capacitor (low temperature coefficient)
- R3: 1kΩ pull-up resistor (on output)

## Phase Detector Circuits

The phase detector uses an XOR gate to compare two oscillator outputs. The average value of the XOR output is proportional to the phase difference between the signals.

### Standard XOR Phase Detector

```
     Oscillator A --+
                    |
                 +--+--+
                 |     |
                 | XOR +--+
                 |     |  |
                 +--+--+  |     +--+
                    |     |     |  |
     Oscillator B --+     +-----+ R1+----+
                                |  |     |
                                +--+     |
                                         |
                                      +--+--+
                                      |     |
                                      | C1  |
                                      |     |
                                      +--+--+
                                         |
                                        GND
                                         |
                                         v
                                 To ESP32 ADC Input
```

**Components:**
- U1: CD4070 (Quad XOR gate)
- R1: 10kΩ resistor
- C1: 0.1μF capacitor

### Enhanced Phase Detector with Filtering

For more sensitive phase detection with better noise immunity:

```
     Oscillator A --+
                    |
                 +--+--+
                 |     |          R1     R2
                 | XOR +--+------/\/\---/\/\--+
                 |     |  |                   |
                 +--+--+  |                   |
                    |     |                +--+--+     +-------+
     Oscillator B --+     |                |     |     |       |
                          +---+           | C1  |     | Buffer |--- To ESP32
                              |           |     |  +--+       |    ADC Input
                              |           +--+--+  |  |       |
                              |              |     |  +-------+
                             +-+             |     |
                             | |             |     |
                             | | R3          +-----+
                             | |
                             +-+
                              |
                             GND
```

**Components:**
- U1: CD4070 (Quad XOR gate)
- U2: LM358 or equivalent buffer op-amp
- R1: 10kΩ resistor
- R2: 10kΩ resistor
- R3: 47kΩ resistor (pull-down)
- C1: 0.47μF capacitor

## Entropy Source Circuit

The entropy source provides true randomness for the system by amplifying noise from a reverse-biased semiconductor junction.

```
                  VCC
                   |
                   v
                  +-+
                  | |
                  | | R1
                  | |
                  +-+
                   |
          +--------+
          |        |
          |    +---+---+
          |    |       |
          |    |   D1  |
          |    |       |
          |    +---+---+
          |        |
          |        |     +--------+
          |        +-----+ 3      |
          |              |        |
          |              | LM358  |
          |        +-----+ 2      |
  +---+   |        |     |        |
  |   |   |        |     +----+---+
  | C1|---+--------+          |
  |   |                       +------+
  +---+                              |
    |                                |
   GND                      +---+    |
                            |   |    |
                            | C2|----+---- Output to ESP32 ADC
                            |   |
                            +---+
                              |
                             GND
```

**Components:**
- U1: LM358 op-amp
- D1: Zener diode (3.3V)
- R1: 47kΩ resistor
- C1: 0.1μF capacitor
- C2: 0.01μF capacitor
- R2: 1MΩ resistor (feedback)
- R3: 100kΩ resistor (gain setting)

## ESP32 Controller Connections

The ESP32 serves as the central controller for each node, processing signals from the oscillators and phase detectors, managing network communication, and controlling the display.

```
                                  +-----------+
        Oscillator 1 ------------>| GPIO25    |
        Oscillator 2 ------------>| GPIO26    |
        Oscillator 3 ------------>| GPIO27    |
                                  |           |
        Phase Detector 1 -------->| GPIO34    |
        Phase Detector 2 -------->| GPIO35    |
        Phase Detector 3 -------->| GPIO32    |
                                  |           |
        Entropy Source ---------->| GPIO33    |
                                  |           |
        OLED SCL --------------->| GPIO22    |
        OLED SDA --------------->| GPIO21    |
                                  |           |
        NeoPixel Control -------->| GPIO5     |
                                  |           |
        Tactile Button 1 -------->| GPIO15    |
        Tactile Button 2 -------->| GPIO4     |
                                  |           |           +--------+
                                  |           |           |        |
                                  |      USB +-+----------+ PC USB |
                                  |           |           |        |
                                  | ESP32     |           +--------+
                                  |           |
                                  |           |           +--------+
                                  |       RX0 +---------->|        |
                                  |       TX0 +<----------+ Node2/3|
                                  |           |           |        |
                                  |           |           +--------+
                                  |           |
                                  |    VCC 3V3+-----------+ 3.3V
                                  |       GND +-----------> GND
                                  +-----------+
                                        |
                                        |
                                    Reset Button
                                        |
                                       GND
```

**Key ESP32 Pin Assignments:**

| Signal | ESP32 Pin | Description |
|--------|-----------|-------------|
| OSC1 | GPIO25 | Prime Oscillator 1 Output |
| OSC2 | GPIO26 | Prime Oscillator 2 Output |
| OSC3 | GPIO27 | Prime Oscillator 3 Output |
| PD1 | GPIO34 (ADC6) | Phase Detector 1 Output |
| PD2 | GPIO35 (ADC7) | Phase Detector 2 Output |
| PD3 | GPIO32 (ADC4) | Phase Detector 3 Output |
| ENT | GPIO33 (ADC5) | Entropy Source Output |
| SCL | GPIO22 | I2C Clock for OLED |
| SDA | GPIO21 | I2C Data for OLED |
| NP | GPIO5 | NeoPixel LED Data |
| BTN1 | GPIO15 | User Button 1 |
| BTN2 | GPIO4 | User Button 2 |
| RX0 | GPIO3 | UART Receive |
| TX0 | GPIO1 | UART Transmit |

## Display Interface

### OLED Display Connection

```
                    VCC (3.3V)
                        |
                        v
         +-+            |            +-+
         | |            |            | |
         | | R1         |            | | R2
         | |            |            | |
         +-+            |            +-+
          |             |             |
          |             |             |
          |         +---+----+        |
          +-------->|        |<-------+
      SCL (GPIO22)  |  OLED  |  SDA (GPIO21)
                    | Display |
                    |        |
                    +--------+
                         |
                        GND
```

**Components:**
- Display: 0.96" SSD1306 OLED (128×64 pixels)
- R1, R2: 4.7kΩ pull-up resistors
- Connection: I2C (address 0x3C or 0x3D)

### NeoPixel LED Connection

```
        VCC (5V)
            |
            v
         +--+---+
         |      |
         |  C1  |
         |      |
         +--+---+
            |                  +----------+
            |    +------------>| VCC      |
            |    |             |          |
            |    |             | WS2812B  |
            |    |             |          |
ESP32 GPIO5 +----+------------>| DIN      |
                 |             |          |
                +-+            | GND      |
                | |            +----------+
                | | R1               |
                | |                  |
                +-+                  |
                 |                   |
                GND                 GND
```

**Components:**
- LED: WS2812B RGB LED strip or ring
- R1: 300-500Ω current-limiting resistor
- C1: 100μF capacitor (for power stability)

## Network Interface

### ESP32 Built-in WiFi

The ESP32 includes built-in WiFi and Bluetooth capabilities, which are used for the primary network interface.

### Wired Backup Connection

```
        ESP32 Node 1                  ESP32 Node 2
          +------+                      +------+
          |      |                      |      |
 TX0 (GPIO1) <---+----------------------+---> RX0 (GPIO3)
          |      |                      |      |
 RX0 (GPIO3) <---+----------------------+---> TX0 (GPIO1)
          |      |                      |      |
       GND +-----+----------------------+--->GND
          |      |                      |      |
          +------+                      +------+
```

**Components:**
- 1kΩ series resistors on TX lines (optional, for protection)
- Twisted pair cable for connections
- Common ground connection between nodes

## Complete Node Schematic

The full schematic for a single node integrates all the components described above:

```
                               VCC (3.3V)
                                   |
                +------------------+------------------+
                |                  |                  |
         +------+------+    +------+------+    +------+------+
         | Oscillator  |    | Oscillator  |    | Oscillator  |
         | Circuit 1   |    | Circuit 2   |    | Circuit 3   |
         | (2 Hz)      |    | (3 Hz)      |    | (5 Hz)      |
         +------+------+    +------+------+    +------+------+
                |                  |                  |
                v                  v                  v
             GPIO25             GPIO26              GPIO27
                |                  |                  |
                |    +-------------+                  |
                |    |             |                  |
                v    v             v                  v
         +------+------+    +------+------+    +------+------+
         | Phase       |    | Phase       |    | Phase       |
         | Detector 1  |    | Detector 2  |    | Detector 3  |
         | (OSC1-OSC2) |    | (OSC1-OSC3) |    | (OSC2-OSC3) |
         +------+------+    +------+------+    +------+------+
                |                  |                  |
                v                  v                  v
             GPIO34             GPIO35              GPIO32
                |                  |                  |
         +------+------+          +------------------+
         | Entropy     |          |                  |
         | Source      +---->GPIO33                  |
         | Circuit     |          |                  |
         +-------------+          |                  |
                                  v                  v
                              +---+---------+
                              |             |
                          +-->| ESP32       |
                          |   | Controller  |
                          |   |             |
                          |   +---+---------+
                          |       |         |
                          |       |         |
                  +-------+       v         v
                  |           +---+---+ +---+---+
                  |           | OLED  | |Network|
                  |           |Display| |Intfce |
                  |           +-------+ +-------+
                  v
             +----+---+
             |        |
             |NeoPixel|
             | LEDs   |
             +--------+
```

**Note:** Power connections and bypass capacitors are omitted for clarity but should be included in the actual implementation.

## PCB Layout Guidelines

### Component Placement

For optimal performance, follow these placement guidelines:

1. **Power Section:**
   - Place voltage regulator near power input
   - Add large ground plane
   - Keep power traces wide (20+ mil)

2. **Analog Section:**
   - Group oscillator components together
   - Separate analog and digital grounds (join at single point)
   - Keep analog signals away from digital switching signals
   - Consider adding a guard ring around analog section

3. **Digital Section:**
   - Place ESP32 in center of board
   - Route crystal traces (if used) as short as possible
   - Add ground plane beneath ESP32

4. **Interface Section:**
   - Place connectors on board edges
   - Group I2C components together
   - Keep serial lines short and direct

### Layer Stack

Recommended 2-layer PCB stack:

1. **Top Layer:**
   - Signal traces
   - Component placement
   - Power distribution

2. **Bottom Layer:**
   - Ground plane
   - Some signal routing
   - Power distribution

### PCB Design Rules

For reliable home fabrication or low-cost production:

- Minimum trace width: 10 mil (0.254mm)
- Minimum spacing: 10 mil (0.254mm)
- Minimum hole size: 0.8mm
- Minimum annular ring: 0.2mm
- Recommended trace width for power: 20-30 mil
- Recommended trace width for signals: 10-15 mil

### Thermal Considerations

- Add thermal relief on ground connections for easier soldering
- Include several vias near voltage regulator for heat dissipation
- Consider adding small heat sink to voltage regulator if driving many LEDs

## Special Considerations

### Noise Immunity

To improve noise immunity and stability:

1. Add ferrite beads on power lines between analog and digital sections
2. Use star grounding topology
3. Add 0.1μF bypass capacitors near each IC power pin
4. Consider adding EMI filtering on external connections

### ESD Protection

For better ESD (Electrostatic Discharge) protection:

1. Add TVS (Transient Voltage Suppressor) diodes on external connections
2. Include ESD protection diodes on user-accessible pins
3. Consider proper enclosure grounding

### Testing Points

Include test points for key signals:

1. Each oscillator output
2. Each phase detector output
3. Power rails (3.3V, 5V)
4. Key ESP32 signals

These test points greatly simplify debugging and calibration.

## Technical Specifications

| Parameter | Value | Notes |
|-----------|-------|-------|
| Power Input | 5V DC | Via USB or barrel jack |
| Current Consumption | 150-200mA | Per node |
| Operating Voltage | 3.3V | Main logic voltage |
| Oscillator Frequency Range | 1-10 Hz | Tunable via trimmer |
| Oscillator Stability | ±1% | After warm-up |
| Phase Measurement Resolution | 12-bit | Via ESP32 ADC |
| WiFi Range | ~30m | Indoor, typical |
| Dimensions | 100 × 80 × 30mm | Per node enclosure |
| Operating Temperature | 10-40°C | Standard operation |

## Customization Options

For enhanced capabilities, consider these modifications to the base schematics:

1. **Higher Precision Oscillators**:
   - Replace 555 timers with crystal-based oscillators
   - Add PLL-based frequency synthesizer (e.g., Si5351)
   - Use temperature-compensated components

2. **Enhanced Phase Detection**:
   - Add analog multiplier for better phase detection
   - Implement dual edge detection for improved resolution
   - Add op-amp buffer stage for impedance matching

3. **Expanded I/O**:
   - Add more buttons and LEDs for user interaction
   - Include rotary encoder for menu navigation
   - Add expansion header for additional peripherals

4. **Advanced Network Interface**:
   - Add Ethernet PHY for wired networking
   - Include LoRa module for long-range communication
   - Add mesh networking capabilities

These modifications maintain compatibility with the base firmware while extending capabilities.