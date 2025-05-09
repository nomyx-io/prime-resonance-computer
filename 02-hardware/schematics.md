# Prime Resonance Computer: Circuit Schematics

## Overview

This document contains detailed circuit schematics for the Prime Resonance Computer. These schematics provide the electrical designs needed to build the hardware components of the system. Each section includes circuit diagrams, component specifications, and technical notes to ensure proper implementation.

## Table of Contents

1. [Power Supply Circuit](#power-supply-circuit)
2. [Oscillator Circuits](#oscillator-circuits)
3. [Phase Detector Circuits](#phase-detector-circuits)
4. [ESP32 Integration Circuit](#esp32-integration-circuit)
5. [User Interface Circuit](#user-interface-circuit)
6. [Node Network Interface](#node-network-interface)
7. [Complete System Schematic](#complete-system-schematic)
8. [PCB Layout Recommendations](#pcb-layout-recommendations)

## Power Supply Circuit

### 5V and 3.3V Regulated Supply

```
                         +----+
                         |    |
    DC IN                |    |
   5.5x2.1mm    +---+    |7805|     +---+     +-------+    +-----+
  +----------+  |   |    |    |     |   |     |AMS1117|    |     |
  |   9-12V  +--+ SW+----+IN  OUT+---+10µF+----+IN OUT+-----+3.3V|
  |          |  |   |    |    |     |   |     |      |     |     |
  +----------+  +---+    | GND|     +---+     |  GND |     +-----+
       |                 |    |      |        |      |        |
       |                 +--+-+      |        +--+---+        |
       |                    |        |           |            |
       +--------------------+--------+-----------+------------+
                            |                    |
                            +                    +
                           GND                  GND

  DC INPUT --> POWER SWITCH --> 7805 --> 5V RAIL --> AMS1117 --> 3.3V RAIL
```

#### Component Specifications

| Component | Specifications | Notes |
|-----------|---------------|-------|
| DC Power Jack | 5.5mm x 2.1mm, Center positive | Panel mount |
| Power Switch | SPST, 2A rating | Panel mount |
| 7805 Regulator | TO-220 package, 1A max | Heat sink recommended |
| AMS1117-3.3V | SOT-223 package, 800mA max | No heat sink needed for normal operation |
| Capacitors | 10µF (input and output of each regulator) | Electrolytic |
| Capacitors | 0.1µF (input and output of each regulator) | Ceramic, not shown in diagram |
| Power Indicator LEDs | 3mm or 5mm, with resistors | 5V (Green), 3.3V (Blue) |

#### Technical Notes

1. Input voltage should be 7-12V DC for optimal operation.
2. The 7805 may require a heat sink if input voltage exceeds 9V.
3. Total current draw should not exceed 800mA from the 3.3V rail.
4. Ensure good filtering with both electrolytic and ceramic capacitors.
5. Add reverse polarity protection diode if desired (1N4001 or similar).

## Oscillator Circuits

### 2 Hz Oscillator (Prime 2)

```
          +5V
           |
           +
           |
           |
        +--+--+
        |     |
        |10K  |
        |Trim |
        |     |
        +--+--+
           |
           |                   +5V
           |                    |
           |    +-----+         +
           +----|7    |         |
                |     |         |
      +---------|6 8  |         |
      |         |     |     +---+---+
      |  +------|2 555|-----|220Ω   |---> To Phase Detector
      |  |      |     |     +---+---+     and ESP32 GPIO25
      |  |      |  4  |         |
      |  |      |     |        LED
      |  |      +--+--+         |
 100kΩ =        |  |          |
      |  |      |  |          |
      |  |      |  |          |
      |  |     GND GND       GND
      |  |
      |  |
      |  |
      +--+
      |  |
      |  |
4.7µF ===
      |  |
      |  |
      |  |
     GND
```

### 3 Hz Oscillator (Prime 3)

```
          +5V
           |
           +
           |
           |
        +--+--+
        |     |
        |10K  |
        |Trim |
        |     |
        +--+--+
           |
           |                   +5V
           |                    |
           |    +-----+         +
           +----|7    |         |
                |     |         |
      +---------|6 8  |         |
      |         |     |     +---+---+
      |  +------|2 555|-----|220Ω   |---> To Phase Detector
      |  |      |     |     +---+---+     and ESP32 GPIO26
      |  |      |  4  |         |
      |  |      |     |        LED
      |  |      +--+--+         |
  68kΩ =        |  |          |
      |  |      |  |          |
      |  |      |  |          |
      |  |     GND GND       GND
      |  |
      |  |
      |  |
      +--+
      |  |
      |  |
3.3µF ===
      |  |
      |  |
      |  |
     GND
```

### 5 Hz Oscillator (Prime 5)

```
          +5V
           |
           +
           |
           |
        +--+--+
        |     |
        |10K  |
        |Trim |
        |     |
        +--+--+
           |
           |                   +5V
           |                    |
           |    +-----+         +
           +----|7    |         |
                |     |         |
      +---------|6 8  |         |
      |         |     |     +---+---+
      |  +------|2 555|-----|220Ω   |---> To Phase Detector
      |  |      |     |     +---+---+     and ESP32 GPIO27
      |  |      |  4  |         |
      |  |      |     |        LED
      |  |      +--+--+         |
  47kΩ =        |  |          |
      |  |      |  |          |
      |  |      |  |          |
      |  |     GND GND       GND
      |  |
      |  |
      |  |
      +--+
      |  |
      |  |
2.2µF ===
      |  |
      |  |
      |  |
     GND
```

#### Component Specifications

| Component | 2 Hz Oscillator | 3 Hz Oscillator | 5 Hz Oscillator |
|-----------|-----------------|-----------------|-----------------|
| IC | NE555 or equivalent | NE555 or equivalent | NE555 or equivalent |
| R1 (fixed) | 100kΩ, 1/4W | 68kΩ, 1/4W | 47kΩ, 1/4W |
| R2 (variable) | 10kΩ multi-turn trimmer | 10kΩ multi-turn trimmer | 10kΩ multi-turn trimmer |
| C1 | 4.7µF, 16V | 3.3µF, 16V | 2.2µF, 16V |
| C2 (pin 5) | 0.01µF ceramic (not shown) | 0.01µF ceramic (not shown) | 0.01µF ceramic (not shown) |
| LED current limiting resistor | 220Ω, 1/4W | 220Ω, 1/4W | 220Ω, 1/4W |
| Indicator LED | 3mm or 5mm, any color | 3mm or 5mm, any color | 3mm or 5mm, any color |

#### Technical Notes

1. The oscillator frequency is determined by the formula: f = 1.44/((R1+2*R2)*C1)
2. Component values are chosen to center the frequency at the target with R2 at mid-position
3. Use low-tolerance components (±5% or better) for stability
4. The trimmer potentiometer allows fine-tuning of the frequency during calibration
5. The 0.01µF capacitor on pin 5 improves stability by filtering noise
6. The output signal is a square wave with approximately 50% duty cycle
7. The LED provides visual confirmation of oscillator operation

## Phase Detector Circuits

### Phase Detector for 2 Hz and 3 Hz Oscillators

```
                              +5V
                               |
                               +
                               |
                           +---+---+
                           |       |
 From 2Hz   +----+         |       |         +---+---+
 Oscillator | 1  |   +-----|14     |         |       |
 ---------->+    +---|     |       |         |  10KΩ |
            |    | 2 |     |       |    +----+       |
 From 3Hz   | XOR+---+  3  |       |    |    |       |
 Oscillator |    |     +---|CD4070 |----+----+       |
 ---------->+ 2  |         |       |         +---+---+
            +----+         |       |             |
                           |       |             |
                           |       |             |
                           |  7    |             |     +------+
                           +---+---+             +-----|      |
                               |                       | LM358|----> To ESP32
                               |                +------|      |      GPIO34
                               |                |      |      |
                               |                |      +------+
                               |               === 1µF
                              GND               |
                                               GND
```

### Phase Detector for 2 Hz and 5 Hz Oscillators

```
                              +5V
                               |
                               +
                               |
                           +---+---+
                           |       |
 From 2Hz   +----+         |       |         +---+---+
 Oscillator | 5  |   +-----|14     |         |       |
 ---------->+    +---|     |       |         |  10KΩ |
            |    | 6 |     |       |    +----+       |
 From 5Hz   | XOR+---+  8  |       |    |    |       |
 Oscillator |    |     +---|CD4070 |----+----+       |
 ---------->+ 6  |         |       |         +---+---+
            +----+         |       |             |
                           |       |             |
                           |       |             |
                           |  7    |             |     +------+
                           +---+---+             +-----|      |
                               |                       | LM358|----> To ESP32
                               |                +------|      |      GPIO35
                               |                |      |      |
                               |                |      +------+
                               |               === 1µF
                              GND               |
                                               GND
```

### Phase Detector for 3 Hz and 5 Hz Oscillators

```
                              +5V
                               |
                               +
                               |
                           +---+---+
                           |       |
 From 3Hz   +----+         |       |         +---+---+
 Oscillator | 9  |   +-----|14     |         |       |
 ---------->+    +---|     |       |         |  10KΩ |
            |    | 10|     |       |    +----+       |
 From 5Hz   | XOR+---+  8  |       |    |    |       |
 Oscillator |    |     +---|CD4070 |----+----+       |
 ---------->+ 8  |         |       |         +---+---+
            +----+         |       |             |
                           |       |             |
                           |       |             |
                           |  7    |             |     +------+
                           +---+---+             +-----|      |
                               |                       | LM358|----> To ESP32
                               |                +------|      |      GPIO32
                               |                |      |      |
                               |                |      +------+
                               |               === 1µF
                              GND               |
                                               GND
```

#### Component Specifications

| Component | Specifications | Notes |
|-----------|---------------|-------|
| XOR Gate IC | CD4070 (Quad XOR) | One IC handles all three phase detectors |
| Resistor | 10kΩ, 1/4W | Low-pass filter |
| Capacitor | 1µF, 16V | Low-pass filter |
| Buffer Op-Amp | LM358 (Dual) | One package handles two outputs |
| Decoupling Capacitor | 0.1µF ceramic | Add near IC power pins (not shown) |

#### Technical Notes

1. The XOR gate outputs high (5V) when inputs differ and low (0V) when inputs match
2. The RC low-pass filter converts the XOR digital output to an analog voltage proportional to phase difference
3. The time constant (RC) should be much larger than the oscillator periods
4. The op-amp buffers the filtered signal for the ESP32's ADC input
5. The output voltage range is approximately 0V to 5V, scaled to the ESP32's 0-3.3V with a voltage divider if needed

## ESP32 Integration Circuit

```
                                   +3.3V
                                     |
                                     +
                                     |
+-----------------------------+      |      +---------------+
|                             |      |      |               |
|                    3.3V Pin+------++      |      OLED     |
|                             |             |    Display    |
|        Oscillator 1 (2Hz)   |      +------+SCL            |
|        ----------------->25 |      |      |               |
|                             |      |      |               |
|        Oscillator 2 (3Hz)   |      |  +---+SDA            |
|        ----------------->26 |      |  |   |               |
|                             |      |  |   |               |
|        Oscillator 3 (5Hz)   |      |  |   |     GND      |
|        ----------------->27 |      |  |   +------+-------+
|                             |      |  |          |
|        Phase Detector 1     |      |  |          |
|        ----------------->34 |      |  |          |
|                             |      |  |          |
|        Phase Detector 2     |      |  |      +---+---+  +3.3V
|        ----------------->35 |      |  |      |       |    |
|                             |      |  |      | 4.7KΩ |    +
|        Phase Detector 3     |      |  |      |       |    |
|        ----------------->32 |      |  |      +---+---+    |
|                             |      |  |          |        |
|                             |      |  |          |        |
|            ESP32            |      |  |     +----+----+   |
|                             |   22 +------+|         |    |
|                             |      |       | 4.7KΩ  |    |
|                             |      |       |         |    |
|                             |   21 +------++         |    |
|                             |             +----+-----+    |
|         Buttons             |                  |          |
|        ------------->13,12,14                  |          |
|                             |                  |          |
|        Rotary Encoder       |                  |          |
|        ------------->17,16,4|                  |          |
|                             |                  |          |
+-------------+---------------+                  |          |
              |                                 GND        GND
             GND
```

#### Component Specifications

| Component | Specifications | Notes |
|-----------|---------------|-------|
| Microcontroller | ESP32 DevKit C | 38 pins, built-in WiFi/BT |
| I2C Pull-up Resistors | 4.7kΩ, 1/4W | One for each I2C line |
| Button Pull-up Resistors | 10kΩ, 1/4W | One for each button (not shown) |
| Encoder Pull-up Resistors | 10kΩ, 1/4W | For encoder lines (not shown) |
| Decoupling Capacitor | 10µF, 16V | Near ESP32 power pin (not shown) |
| Decoupling Capacitor | 0.1µF ceramic | Near ESP32 power pin (not shown) |

#### Technical Notes

1. ESP32 ADC inputs are limited to 0-3.3V; ensure phase detector outputs don't exceed this
2. GPIO pins 34, 35 are input-only on the ESP32
3. I2C pull-up resistors connect the SCL and SDA lines to 3.3V
4. All digital inputs should have appropriate pull-up or pull-down resistors
5. The ESP32 DevKit includes USB interface for programming and serial communication
6. The ESP32's RTC pins can be used for accurate timing if required

## User Interface Circuit

### OLED Display Connection

```
          +3.3V
           |
           +
           |
       +---+---+                     +----------------+
       |       |                     |                |
       | 4.7KΩ |                     |                |
       |       |                     |                |
       +---+---+                     |                |
           |                         |                |
           +-------------------------+ SCL            |
                                     |                |
       +---+---+                     |   OLED Display |
       |       |                     |   128x64 I2C   |
       | 4.7KΩ |                     |                |
       |       |                     |                |
       +---+---+                     |                |
           |                         |                |
           +-------------------------+ SDA            |
                                     |                |
                                     |                |
                                     |                |
                                     |                |
           +-----------------------+ GND              |
           |                         |                |
           |                         +----------------+
          GND
```

### Button Connections

```
           +3.3V
            |
            +
            |
        +---+---+
        |       |
        | 10KΩ  |         +--------+
        |       |         |        |
        +---+---+         |        |
            |             |  BTN1  |
            +-------------|        |----> To ESP32 GPIO13
                          |        |
                          +--------+
                              |
                              |
                             GND
```

#### Component Specifications

| Component | Specifications | Notes |
|-----------|---------------|-------|
| OLED Display | 0.96", 128x64 pixels, I2C | SSD1306 controller |
| Buttons | Tactile push buttons | Panel mount |
| Button Pull-up Resistors | 10kΩ, 1/4W | One for each button |
| Rotary Encoder | 20 detents per rotation | With push button |
| RGB LEDs (optional) | WS2812B | Programmable color indicator |

#### Technical Notes

1. The OLED uses I2C communication protocol with the ESP32
2. Default I2C address for SSD1306 is 0x3C or 0x3D (selectable via hardware)
3. Buttons connect to ESP32 GPIO pins through pull-up resistors
4. Button inputs should be debounced in software
5. Rotary encoder requires two GPIO pins plus one for the push button
6. For RGB LEDs, use a data pin with a 470Ω series resistor

## Node Network Interface

### ESP-NOW Wireless Communication (Built into ESP32)

```
              +-----------------+
              |                 |
              |                 |
              |                 |
              |     ESP32       |
              |   Built-in      |
              |    Antenna      |
              |                 |
              |                 |
              |                 |
              +-----------------+

                  ESP-NOW
                 Wireless
              Communication

 +--------+     +--------+     +--------+
 |        |     |        |     |        |
 | Node 1 |<--->| Node 2 |<--->| Node 3 |
 |        |     |        |     |        |
 +--------+     +--------+     +--------+
      ^                            ^
      |                            |
      +----------------------------+
           Direct connection
```

#### Technical Notes

1. ESP-NOW is a connectionless protocol developed by Espressif
2. Maximum range: approximately 100 meters in open space
3. Each ESP32 node has a unique MAC address for identification
4. One node can be configured as the coordinator (central control)
5. Maximum payload size: 250 bytes per message
6. Low power consumption compared to full WiFi
7. No router or access point required

## Complete System Schematic

### Single Node Block Diagram

```
+---------------------------------------------------------------------+
|                                                                     |
|  +---------------+     +---------------+     +---------------+      |
|  |               |     |               |     |               |      |
|  |  Oscillator   |---->|  Oscillator   |---->|  Oscillator   |      |
|  |    2 Hz       |     |    3 Hz       |     |    5 Hz       |      |
|  |               |     |               |     |               |      |
|  +-------+-------+     +-------+-------+     +-------+-------+      |
|          |                     |                     |              |
|          v                     v                     v              |
|  +-------+-------+     +-------+-------+     +-------+-------+      |
|  |               |     |               |     |               |      |
|  |  Phase Det.   |---->|  Phase Det.   |---->|  Phase Det.   |      |
|  |   2Hz-3Hz     |     |   2Hz-5Hz     |     |   3Hz-5Hz     |      |
|  |               |     |               |     |               |      |
|  +-------+-------+     +-------+-------+     +-------+-------+      |
|          |                     |                     |              |
|          v                     v                     v              |
|  +-------+---------------------+---------------------+--------+     |
|  |                                                            |     |
|  |                          ESP32                             |     |
|  |                                                            |     |
|  +----+----------------------------+-------------------------+|     |
|       |                            |                          |     |
|       v                            v                          v     |
|  +----+-------+             +------+------+            +------+--+  |
|  |            |             |             |            |         |  |
|  |   OLED     |             |  User       |            | Network |  |
|  |  Display   |             |  Controls   |            |Interface|  |
|  |            |             |             |            |         |  |
|  +------------+             +-------------+            +---------+  |
|                                                                     |
+---------------------------------------------------------------------+
```

#### Component Count Per Node

| Component Type | Quantity | Notes |
|----------------|----------|-------|
| ESP32 | 1 | Main controller |
| 555 Timer | 3 | One per oscillator |
| CD4070 | 1 | Contains four XOR gates (three used) |
| LM358 | 2 | Dual op-amp (three channels used) |
| OLED Display | 1 | User interface |
| Resistors | ~25 | Various values |
| Capacitors | ~20 | Various values |
| Buttons | 3-5 | User input |
| LEDs | 5-10 | Status indicators |
| Power Components | 5-7 | Regulators, filters |
| Connectors | 3-5 | Power, programming |

## PCB Layout Recommendations

### Component Placement Guidelines

```
+-----------------------------------------------------------------------+
|                                                                       |
|  +--------------------------------------------------+                 |
|  |                  Power Supply Section             |   DC Input     |
|  +--------------------------------------------------+      Jack      |
|                                                                       |
|  +----------------+   +----------------+   +----------------+         |
|  |                |   |                |   |                |   Power |
|  |  Oscillator 1  |   |  Oscillator 2  |   |  Oscillator 3  |  Switch |
|  |     (2Hz)      |   |     (3Hz)      |   |     (5Hz)      |         |
|  |                |   |                |   |                |         |
|  +----------------+   +----------------+   +----------------+         |
|                                                                       |
|  +----------------+   +----------------+   +----------------+         |
|  |                |   |                |   |                |         |
|  | Phase Detector |   | Phase Detector |   | Phase Detector |         |
|  |    (2Hz-3Hz)   |   |    (2Hz-5Hz)   |   |    (3Hz-5Hz)   |         |
|  |                |   |                |   |                |         |
|  +----------------+   +----------------+   +----------------+         |
|                                                                       |
|  +--------------------------------------------------+                 |
|  |                                                  |                 |
|  |                      ESP32                       |                 |
|  |                                                  |                 |
|  +--------------------------------------------------+                 |
|                                                                       |
|  +------------------+                  +-------------------+          |
|  |                  |                  |                   |          |
|  |   OLED Display   |                  |   User Controls   |          |
|  |    Connector     |                  |                   |          |
|  |                  |                  |                   |          |
|  +------------------+                  +-------------------+          |
|                                                                       |
+-----------------------------------------------------------------------+
```

#### PCB Layout Considerations

1. **Power Supply Section:**
   - Keep away from sensitive analog circuits
   - Use wide traces for power lines (40 mil minimum)
   - Place decoupling capacitors close to ICs
   - Consider a ground plane for better noise performance

2. **Oscillator Section:**
   - Separate oscillators to minimize interference
   - Keep traces short for timing components
   - Shield from external noise sources
   - Add test points for frequency measurement

3. **Phase Detector Section:**
   - Maintain equal trace lengths for signal pairs
   - Use ground plane between detectors
   - Place filter components close to XOR outputs
   - Add test points for signal monitoring

4. **ESP32 Section:**
   - Follow reference design guidelines
   - Keep antenna area clear of components and ground plane
   - Route sensitive signals away from noisy digital lines
   - Consider thermal management

5. **General Layout:**
   - Separate analog and digital ground planes with single star connection point
   - Route I2C lines together with controlled impedance if possible
   - Place controls and connectors near board edges for accessibility
   - Add mounting holes in corners

### Layer Stackup (2-layer PCB)

```
+-----------------------------------------------------------+
|                    Component Side                         |
|                    (Top Layer)                            |
+-----------------------------------------------------------+
|                                                           |
|              FR4 Substrate (1.6mm)                        |
|                                                           |
+-----------------------------------------------------------+
|                    Solder Side                            |
|                    (Bottom Layer)                         |
+-----------------------------------------------------------+
```

#### Layer Guidelines

1. **Top Layer:**
   - Component placement and primary signal routing
   - Power distribution for non-critical components
   - Signal traces for digital communication

2. **Bottom Layer:**
   - Ground plane (80%+ coverage)
   - Some signal routing where necessary
   - Power distribution for critical components

## Conclusion

These circuit schematics provide the electrical designs needed to build a functional Prime Resonance Computer. The modular approach allows for building the system in stages, testing each component before final integration.

For physical implementation details and assembly instructions, please refer to the Parts & Assembly Guide. For PCB manufacturing files and Gerber outputs, refer to the PCB Design document.