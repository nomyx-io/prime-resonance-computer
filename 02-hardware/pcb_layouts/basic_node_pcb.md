# Basic Prime Resonator Node - PCB Layout

## Overview

This document provides PCB layout designs for the basic prime resonator node. The PCB is designed to be:

- Economical to produce (2-layer)
- Easy to assemble (mostly through-hole components)
- Modular for educational purposes
- Under 100mm × 100mm for low-cost fabrication

## PCB Layout Visualization

```
+-------------------------------------------------------+
|                                                       |
|   +-----+          +-------+          +----------+    |
|   |     |          |       |          |          |    |
|   | USB |          | Power |          | ESP32    |    |
|   | PWR |          | Reg   |          | Module   |    |
|   |     |          |       |          |          |    |
|   +-----+          +-------+          +----------+    |
|                                              |        |
|   +--------+      +--------+      +--------+ |        |
|   |        |      |        |      |        | |        |
|   | OSC 1  |      | OSC 2  |      | OSC 3  | |        |
|   | (2Hz)  |      | (3Hz)  |      | (5Hz)  | |        |
|   |        |      |        |      |        | |        |
|   +---+----+      +---+----+      +---+----+ |        |
|       |               |               |      |        |
|       v               v               v      v        |
|   +-----------------------------------------------+   |
|   |                                               |   |
|   |           Phase Comparator Section            |   |
|   |                                               |   |
|   +-----------------------------------------------+   |
|                         |                             |
|   +--------+            |            +-----------+    |
|   |        |            |            |           |    |
|   | Entropy |<----------+----------->| I2C OLED  |    |
|   | Source  |                        | Display   |    |
|   |        |                         |           |    |
|   +--------+                         +-----------+    |
|                                                       |
|   +---------------+                  +-----------+    |
|   |               |                  |           |    |
|   | RGB LED Strip |                  | Expansion |    |
|   | Connector     |                  | Header    |    |
|   |               |                  |           |    |
|   +---------------+                  +-----------+    |
|                                                       |
+-------------------------------------------------------+
```

## Layer Structure

The PCB uses a standard 2-layer design:

- **Top Layer**: Component placement and signal routing
- **Bottom Layer**: Ground plane with signal routing where necessary
- **Silkscreen Top**: Component outlines, labels, and reference designators
- **Silkscreen Bottom**: Board information and additional labels

## Component Placement Strategy

### Power Section
- Located near the USB connector at the top-left of the board
- Short traces to minimize voltage drop
- Decoupling capacitors placed close to power pins of ICs
- Separate analog and digital power planes where possible

### Oscillator Section
- Each oscillator circuit is isolated in its own section
- 555 timer ICs arranged in parallel for easy comparison
- Tuning potentiometers accessible from the edge of the board
- Ground connections use star topology to minimize interference

### Phase Comparator Section
- Centrally located between oscillators and ESP32
- Short, direct traces to minimize signal degradation
- Digital and analog ground separation maintained
- Test points provided for oscilloscope monitoring

### ESP32 Module
- Placed on the right side of the board
- Pin headers for easy removal/replacement
- USB programming port accessible from the edge
- Reset and programming buttons clearly labeled

### Output Section
- I2C OLED display connector located on the right side
- RGB LED strip connector on the bottom edge
- Separate power filtering for LED strip to prevent noise
- Expansion headers for additional output devices

### Entropy Source
- Isolated from oscillators to prevent coupling
- Shielded ground plane underneath
- Power filtering components for clean supply
- Analog output buffering before digital conversion

## PCB Routing Guidelines

### Signal Integrity
- Keep analog signals away from digital signals
- Use ground plane islands for sensitive analog circuits
- Route clock signals with minimal length and turns
- Add series termination resistors for fast digital signals

### Power Distribution
- Use power planes or wide traces for power distribution
- Include multiple decoupling capacitors (0.1μF, 10μF)
- Place bulk capacitors (100μF) near power entry points
- Implement star grounding for analog sections

### Critical Traces
- Phase detector outputs to ESP32 ADC inputs
- Oscillator outputs to phase comparators
- Entropy source to ESP32 analog input
- I2C signals to display (keep pairs matched length)

### Design for Manufacturing
- Minimum trace width: 8 mil (0.2032 mm)
- Minimum spacing: 8 mil (0.2032 mm)
- Via size: 0.6 mm drill, 1.2 mm pad
- Edge clearance: 2 mm from all components

## Component Footprints

| Component | Package | Qty |
|-----------|---------|-----|
| ESP32 DevKit | Module with pins | 1 |
| 555 Timer ICs | DIP-8 | 3 |
| CD4070 (XOR Gates) | DIP-14 | 1 |
| LM358 Op-Amp | DIP-8 | 1 |
| Resistors | Through-hole 1/4W | 20+ |
| Capacitors (small) | Ceramic 2.54mm pitch | 10+ |
| Capacitors (large) | Electrolytic 2.5mm or 5mm pitch | 4 |
| Potentiometers | Trim pot 3-pin | 3 |
| Zener Diode | DO-35 | 1 |
| LEDs | 3mm or 5mm | 5 |
| Pin Headers | 2.54mm pitch | 5 sets |
| OLED Display Connector | 4-pin JST | 1 |
| USB Connector | Micro USB | 1 |
| 3.3V Regulator | SOT-223 | 1 |
| Buttons | 6mm × 6mm tactile | 2 |

## PCB Production Specifications

- **Dimensions**: 80mm × 80mm
- **Layers**: 2 layers
- **Thickness**: 1.6mm
- **Copper Weight**: 1oz (35μm)
- **Solder Mask**: Blue
- **Silkscreen**: White
- **Surface Finish**: HASL (lead-free)
- **Min Trace/Space**: 8/8 mil

## Prototype Testing Points

Labeled test points are provided for essential signals:

1. TP1: 5V Power
2. TP2: 3.3V Power
3. TP3-TP5: Oscillator outputs
4. TP6-TP8: Phase comparator outputs
5. TP9: Entropy source output
6. TP10: Ground reference

## Assembly Guidelines

1. Begin with power regulation components
2. Install passive components (resistors, capacitors)
3. Add IC sockets for all integrated circuits
4. Install connectors and headers
5. Test power distribution before inserting ICs
6. Insert ICs and test basic functionality
7. Calibrate oscillators to prime frequencies
8. Connect display and configure with firmware

## PCB Layout Files

To generate industry-standard Gerber files from this design:

1. Download and install KiCAD (free and open-source)
2. Open the source files provided in the `pcb_source` directory
3. Use the Plot function to generate Gerber files
4. Use the Drill file generator for drill information
5. Package all files for manufacturing

The PCB design files are available in:
- KiCAD format (editable)
- Gerber format (for manufacturing)
- PDF format (for reference)

## Alternative Layout Options

### Breadboard Prototype Version

For initial prototyping without PCB fabrication:

```
+---------------------------------------------+
| +----------+    +----------+    +----------+|
| |          |    |          |    |          ||
| |  ESP32   |    |  OLED    |    |  Power   ||
| |  DevKit  |    | Display  |    | Supply   ||
| |          |    |          |    |          ||
| +----------+    +----------+    +----------+|
|                                             |
| +-----------------Breadboard----------------+|
| |                                           ||
| | +-------+  +-------+  +-------+  +-----+ ||
| | | OSC 1 |  | OSC 2 |  | OSC 3 |  | XOR | ||
| | |  555  |  |  555  |  |  555  |  | Gate| ||
| | +-------+  +-------+  +-------+  +-----+ ||
| |                                           ||
| | +----------+  +--------+  +-----------+  ||
| | |  Entropy |  |  LED   |  | Jumper    |  ||
| | |  Source  |  | Array  |  | Wire Area |  ||
| | +----------+  +--------+  +-----------+  ||
| |                                           ||
| +-------------------------------------------+|
+---------------------------------------------+
```

### 4-Layer Enhanced Version

For improved performance and reduced interference:

- Layer 1: Component placement and signal routing
- Layer 2: Ground plane
- Layer 3: Power distribution
- Layer 4: Signal routing and component placement

This design isolates sensitive analog signals and provides better power distribution.

## Scaling to Multiple Nodes

To create a multi-node prime computer, these PCBs can be interconnected:

```
  +-----------+    +-----------+    +-----------+
  |           |    |           |    |           |
  | Node PCB  |<-->| Node PCB  |<-->| Node PCB  |
  |    #1     |    |    #2     |    |    #3     |
  |           |    |           |    |           |
  +-----------+    +-----------+    +-----------+
        ^                ^                ^
        |                |                |
        v                v                v
  +-----------------------------------------+
  |                                         |
  |          Control/Display PCB            |
  |                                         |
  +-----------------------------------------+
```

Interconnection options:
1. Direct wiring between headers
2. Backplane board with sockets
3. Wireless communication (ESP32 WiFi/Bluetooth)

## Bill of Materials

See the [parts_list.md](../parts_list.md) document for a detailed bill of materials for this PCB.