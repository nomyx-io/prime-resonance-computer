# Basic Prime Resonator Node - Schematic

## Overview

This document provides the schematic design for a basic prime resonator node. Each node consists of:

1. Three prime-tuned oscillators
2. Phase comparator circuits
3. Entropy injection system
4. Control microcontroller (ESP32)
5. Output display system

## Circuit Diagram

```
                     +--------+
                     |        |
                     |  ESP32 |
                     |        |
                     +---+----+
                         |
            +------------+------------+
            |            |            |
    +-------v------+ +---v----------+ +----v--------+
    |   Prime      | |   Phase      | |  Entropy    |
    | Oscillators  | | Comparators  | |  Source     |
    +-------+------+ +---+----------+ +----+--------+
            |            |                 |
            +------------+-----------------+
                         |
                    +----v-----+
                    |  Output  |
                    |  System  |
                    +----------+
```

## Detailed Schematics

### Prime Oscillators Section

The oscillator section consists of three 555 timer-based oscillators tuned to prime frequencies.

```
                           Vcc
                            |
                            R1
                            |
    +-----+          +------+-------+
    |     |          |      |       |
    |     +----------+ DIS  THR +---+
    |     |          |            |
    |     |    555   |            |
    |   +-+--+ Timer +------+     |
    |   |    |       |      |     |
    |   | RST|       | OUT  |     |
    |   |    |       |      |     |
    |   +--+-+-------+------+     |
    |      |                |     |
    |      |                |     |
    |      v                v     |
    |     GND         Output     C1
    |                             |
    +-----------------------------+
                            |
                           GND
```

**Component Values:**

For a 2Hz oscillator (prime frequency):
- R1: 330kΩ
- C1: 1.5μF

For a 3Hz oscillator (prime frequency):
- R1: 220kΩ
- C1: 1.5μF

For a 5Hz oscillator (prime frequency):
- R1: 133kΩ
- C1: 1.5μF

The frequency is determined by: f = 1/(1.1 × R1 × C1)

### Phase Comparator Circuit

The phase comparator uses XOR gates to detect phase differences between oscillator outputs.

```
           +-----+
Osc1 ----->|     |
           | XOR +----->  Phase Difference
Osc2 ----->|     |        Signal to ESP32 ADC
           +-----+
```

Full comparator network for three oscillators:

```
           +-----+
Osc1 ----->|     |
           | XOR +----> Phase1-2 to ESP32 GPIO32
Osc2 ----->|     |
           +-----+

           +-----+
Osc1 ----->|     |
           | XOR +----> Phase1-3 to ESP32 GPIO33
Osc3 ----->|     |
           +-----+

           +-----+
Osc2 ----->|     |
           | XOR +----> Phase2-3 to ESP32 GPIO34
Osc3 ----->|     |
           +-----+
```

**Component Selection:**
- CD4070 or 74HC86 quad XOR gate IC
- 10kΩ pull-up resistors on outputs

### Entropy Injection System

Entropy (randomness) is injected using a Zener diode noise source:

```
        Vcc
         |
         R1
         |
    +----+----+     +--------+
    |    |    |     |        |
 +--+    Z    |     |        |
 |       |    +---->| Op-Amp +---> To ESP32 DAC Output
 R2      |    |     |        |     (GPIO25)
 |       |    |     |        |
 +-------+----+     +--------+
         |
        GND
```

**Component Values:**
- Z: 3.3V Zener diode
- R1: 10kΩ
- R2: 100kΩ
- Op-Amp: LM358 configured as non-inverting amplifier
- Gain resistors for op-amp: Rf = 100kΩ, Ri = 10kΩ

### ESP32 Microcontroller Connections

```
                        +---------------+
                        |     ESP32     |
                        |     DevKit    |
                        +---+-----------+
                            |
+----------------------+    |    +----------------------+
| GPIO Connections     |    |    | Power Connections    |
+----------------------+    |    +----------------------+
                            |
        +------------------++-----------------+
        |                  |                  |
+-------v------+     +-----v-------+    +----v--------+
|  Oscillator  |     |   Phase     |    |  Display    |
|  Control     |     | Monitoring  |    | Interface   |
+--------------+     +-------------+    +-------------+

```

**Pin Assignments:**
- **Oscillator Control:**
  - GPIO25 (DAC1): Entropy injection
  - GPIO26 (DAC2): Variable oscillator control
  - GPIO12: Oscillator 1 reset/enable
  - GPIO13: Oscillator 2 reset/enable
  - GPIO14: Oscillator 3 reset/enable

- **Phase Monitoring:**
  - GPIO32 (ADC1_CH4): Phase detector 1-2 output
  - GPIO33 (ADC1_CH5): Phase detector 1-3 output
  - GPIO34 (ADC1_CH6): Phase detector 2-3 output
  - GPIO35 (ADC1_CH7): Entropy level monitoring

- **Display Interface:**
  - GPIO21 (SDA): I2C data for OLED
  - GPIO22 (SCL): I2C clock for OLED
  - GPIO5: Neopixel LED data line
  - GPIO18: Additional LED indicator
  - GPIO19: Additional LED indicator

### Output System

The output system consists of an OLED display and RGB LEDs:

```
       +----------------------+
       |      ESP32           |
       |                      |
       |                      |
       | GPIO21 (SDA) +----->+------+
       |                     |      |
       | GPIO22 (SCL) +----->| OLED |
       |                     |      |
       |                     +------+
       |                      
       |                     +------+
       |                     |      |
       | GPIO5 +------------>| RGB  |
       |                     | LEDs |
       |                     |      |
       |                     +------+
       +----------------------+
```

**Component Details:**
- OLED: 0.96" 128x64 I2C display
- RGB LEDs: WS2812B Neopixel strip (8 LEDs)
- I2C Pull-up resistors: 4.7kΩ for SDA and SCL lines
- Power filtering: 100μF electrolytic capacitor and 0.1μF ceramic capacitor

## Power Supply Circuit

```
     +--------+     +-------------+     +-------+
USB  |        |     |             |     |       |
+---->  USB   +---->+  5V to 3.3V +---->+ ESP32 |
     | Bridge |     |  Regulator  |     |       |
     |        |     |             |     |       |
     +--------+     +-------------+     +-------+
                          |
                          v
                    To other components
```

**Component Selection:**
- USB Bridge: Micro USB breakout board
- 5V Regulator: AMS1117-5.0 (optional if using USB power)
- 3.3V Regulator: AMS1117-3.3
- Filtering Capacitors: 10μF on input and output of each regulator

## Full System Integration

The complete node integrates all these components:

```
                      +----------+
                      |          |
         +------------+ ESP32    +--------------+
         |            | DevKit   |              |
         |            |          |              |
         |            +--+-------+              |
         |               |                      |
  +------v-----+   +----v------+         +-----v-----+
  |            |   |           |         |           |
  | Oscillator |   |  Phase    |         | Entropy   |
  | Circuit    |   |  Detect   |         | Source    |
  | (555 x3)   |   |  (XOR)    |         | (Zener)   |
  |            |   |           |         |           |
  +------+-----+   +----+------+         +-----+-----+
         |              |                      |
         +--------------+----------------------+
                        |
                  +-----v-----+
                  |           |
                  |  Output   |
                  | (Display/ |
                  |   LEDs)   |
                  |           |
                  +-----------+
```

## PCB Layout Considerations

For a single-layer PCB implementation:
- Keep analog oscillator circuits separated from digital sections
- Position phase comparator circuits close to both oscillators and ESP32
- Place decoupling capacitors (0.1μF) close to all ICs
- Provide separate power distribution for analog and digital sections
- Use star grounding topology to minimize ground loops
- Position entropy circuit away from oscillators to prevent unwanted coupling
- Include silkscreen labels for all major components and test points

## Next Steps

After building the basic node schematic shown here:

1. Verify oscillator operation with an oscilloscope
2. Test phase detection circuit with known reference signals
3. Confirm entropy injection causes observable phase changes
4. Implement firmware to integrate all components
5. Network multiple nodes together for enhanced computation

For more details on component selection, refer to the [parts_list.md](../parts_list.md) document.

## Firmware Considerations

The ESP32 needs to run firmware that:
- Monitors the phase relationships between oscillators
- Injects entropy at appropriate times
- Detects stable states after collapse
- Communicates with other nodes
- Provides visual feedback via the display system

See the [03-software/firmware](../../03-software/firmware/) directory for firmware implementation details.