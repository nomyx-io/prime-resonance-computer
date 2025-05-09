# Prime Computer PCB Designs

## Overview

This document provides detailed PCB (Printed Circuit Board) designs for fabricating the Prime Resonance Computer hardware. These designs include component placement diagrams, routing guidelines, and manufacturing specifications. The PCB files are available in KiCad format, Gerber format, and as PDF schematics, making them compatible with most PCB manufacturing services and software tools.

## Table of Contents

1. [Main Node PCB](#main-node-pcb)
2. [Coordinator Node PCB](#coordinator-node-pcb)
3. [Manufacturing Specifications](#manufacturing-specifications)
4. [Assembly Guidelines](#assembly-guidelines)
5. [Testing Procedures](#testing-procedures)
6. [Design Files](#design-files)

## Main Node PCB

### Board Specifications

- Dimensions: 100mm × 80mm
- Layers: 2 (Top and Bottom)
- Thickness: 1.6mm
- Copper weight: 1oz (35μm)
- Surface finish: HASL (lead-free)
- Soldermask color: Blue (recommended for best contrast)
- Silkscreen color: White

### Component Placement - Top Layer

```
+----------------------------------------------------+
|                                                    |
|   +----------+                      +----------+   |
|   | Power    |                      | ESP32    |   |
|   | Supply   |                      | Module   |   |
|   +----------+                      +----------+   |
|                                                    |
|   +----------+    +----------+    +----------+     |
|   |          |    |          |    |          |     |
|   | OSC1     |    | OSC2     |    | OSC3     |     |
|   | Circuit  |    | Circuit  |    | Circuit  |     |
|   |          |    |          |    |          |     |
|   +----------+    +----------+    +----------+     |
|                                                    |
|                                                    |
|   +----------+    +----------+    +----------+     |
|   |          |    |          |    |          |     |
|   | Phase    |    | Phase    |    | Phase    |     |
|   | Det. 1   |    | Det. 2   |    | Det. 3   |     |
|   |          |    |          |    |          |     |
|   +----------+    +----------+    +----------+     |
|                                                    |
|                   +----------+                     |
|                   | OLED     |                     |
|                   | Display  |                     |
|                   | Connector|                     |
|                   +----------+                     |
|                                                    |
|   +--------+                          +--------+   |
|   | BTN1   |                          | BTN2   |   |
|   +--------+                          +--------+   |
|                                                    |
+----------------------------------------------------+
```

### PCB Layers

#### Top Layer (Component Side)

The top layer contains all components and the majority of signal traces:

![Node PCB Top Layer](../04-diagrams/node_pcb_top.png)

**Key Routing Areas:**
- ESP32 module connections
- Oscillator signal paths
- Phase detector outputs
- Power distribution network

#### Bottom Layer (Solder Side)

The bottom layer primarily serves as a ground plane with some signal routing:

![Node PCB Bottom Layer](../04-diagrams/node_pcb_bottom.png)

**Key Feature Areas:**
- Ground plane coverage (80%+)
- Supplementary signal routing
- Power supply return paths
- Test point pads

### Key PCB Sections

#### Power Supply Section

This dedicated area handles power input and regulation:

```
      +--------------------------------+
      |       5V Input Jack            |
      |     +----------------+         |
      |     |                |         |
      |     |                |         |
      |     +----------------+         |
      |                                |
      |  +-------+     +-----------+   |
      |  |       |     |           |   |
      |  | Power |     | AMS1117   |   |
      |  | Switch|     | Regulator |   |
      |  |       |     |           |   |
      |  +-------+     +-----------+   |
      |                                |
      | C1+  C2+    C3+    C4+        |
      | ---  ---    ---    ---        |
      | GND  GND    GND    GND        |
      +--------------------------------+
```

**Design Considerations:**
- Wide power traces (30 mil minimum)
- Large ground pads for thermal dissipation
- Multiple filtering capacitors
- Clear separation from sensitive analog circuits

#### Oscillator Section

Each oscillator has a dedicated area to minimize interference:

```
      +--------------------------------+
      |                                |
      |   +------------+               |
      |   |            |               |
      |   |   555 IC   |               |
      |   |            |               |
      |   +------------+               |
      |     |  |  |  |                 |
      |     |  |  |  |                 |
      |    R1  R2 C1  C2               |
      |    |   |   |   |               |
      |    +---+---+---+               |
      |        |                       |
      |    +--------+                  |
      |    |Trimmer |                  |
      |    |   R3   |                  |
      |    +--------+                  |
      |        |                       |
      |        +---- Output            |
      |                                |
      +--------------------------------+
```

**Design Considerations:**
- Short trace lengths for timing components
- Ground plane underneath oscillator circuit
- Isolation from other oscillator sections
- Easily accessible trimmer potentiometer

#### Phase Detector Section

Phase detectors compare oscillator outputs:

```
      +--------------------------------+
      |                                |
      |    +-------------+             |
      |    |             |             |
      |    |  CD4070     |             |
      |    |  XOR Gate   |             |
      |    |             |             |
      |    +-------------+             |
      |      |        |                |
      |     OSC1     OSC2              |
      |      |        |                |
      |      +--------+                |
      |          |                     |
      |      +------+                  |
      |      |Filter|                  |
      |      | R/C  |                  |
      |      +------+                  |
      |          |                     |
      |          +---- To ESP32        |
      |                                |
      +--------------------------------+
```

**Design Considerations:**
- Equal-length traces for oscillator inputs
- Analog filtering section protected from noise
- Direct path to ESP32 ADC input
- Separate ground return path

#### ESP32 Module Section

The ESP32 serves as the central controller:

```
      +--------------------------------+
      |                                |
      |    +------------------+        |
      |    |                  |        |
      |    |     ESP32        |        |
      |    |    DevKit        |        |
      |    |                  |        |
      |    +------------------+        |
      |      | | | | | | | | |         |
      |     (GPIO Connections)         |
      |    +------------------+        |
      |    | Reset & Program  |        |
      |    | Buttons          |        |
      |    +------------------+        |
      |                                |
      |    +------------------+        |
      |    | USB Serial       |        |
      |    | Connector        |        |
      |    +------------------+        |
      |                                |
      +--------------------------------+
```

**Design Considerations:**
- Proper clearance around antenna area
- Bypass capacitors near power pins
- Reset button with debounce circuit
- Programming button accessible from outside

#### Display Connector Section

I2C connection to OLED display:

```
      +--------------------------------+
      |                                |
      |    +------------------+        |
      |    |     OLED         |        |
      |    |    Connector     |        |
      |    |                  |        |
      |    |  VCC GND SCL SDA |        |
      |    +------------------+        |
      |      |   |   |   |             |
      |     3.3V GND  |   |            |
      |              |   |             |
      |             R1  R2             |
      |      |       |   |             |
      |     3.3V     |   |             |
      |              |   |             |
      |              +---+             |
      |                |               |
      |              ESP32             |
      |                                |
      +--------------------------------+
```

**Design Considerations:**
- I2C pull-up resistors (4.7kΩ)
- Connector positioned for easy access
- Short I2C lines to minimize noise
- ESD protection diodes (optional)

### Copper Zones and Ground Planes

Ground plane design is critical for proper operation:

```
      +--------------------------------+
      |   Analog Ground   |  Digital   |
      |        Zone       |  Ground    |
      |                   |  Zone      |
      |   +---+---+---+   |            |
      |   |OSC|OSC|OSC|   |            |
      |   | 1 | 2 | 3 |   |            |
      |   +---+---+---+   |            |
      |                   |            |
      |   +---+---+---+   |            |
      |   | PD| PD| PD|   |   +-----+  |
      |   | 1 | 2 | 3 |   |   |ESP32|  |
      |   +---+---+---+   |   |     |  |
      |                   |   +-----+  |
      |                   |            |
      |                   |            |
      |         Star Ground Point      |
      |                                |
      +--------------------------------+
```

**Design Considerations:**
- Separate analog and digital ground zones
- Single star ground connection point
- Solid ground under oscillators
- Isolated ground islands for sensitive components

## Coordinator Node PCB

The coordinator node has additional features beyond the standard node:

### Board Specifications

- Dimensions: 120mm × 90mm
- Layers: 2 (Top and Bottom)
- Thickness: 1.6mm
- Copper weight: 1oz (35μm)
- Surface finish: HASL (lead-free)
- Soldermask color: Blue
- Silkscreen color: White

### Component Placement - Top Layer

```
+----------------------------------------------------+
|                                                    |
|   +----------+                      +----------+   |
|   | Power    |                      | ESP32    |   |
|   | Supply   |                      | Module   |   |
|   +----------+                      +----------+   |
|                                                    |
|   +----------+    +----------+    +----------+     |
|   |          |    |          |    |          |     |
|   | OSC1     |    | OSC2     |    | OSC3     |     |
|   | Circuit  |    | Circuit  |    | Circuit  |     |
|   |          |    |          |    |          |     |
|   +----------+    +----------+    +----------+     |
|                                                    |
|                                                    |
|   +----------+    +----------+    +----------+     |
|   |          |    |          |    |          |     |
|   | Phase    |    | Phase    |    | Phase    |     |
|   | Det. 1   |    | Det. 2   |    | Det. 3   |     |
|   |          |    |          |    |          |     |
|   +----------+    +----------+    +----------+     |
|                                                    |
|                   +----------+                     |
|                   | OLED     |                     |
|                   | Display  |                     |
|                   | Connector|                     |
|                   +----------+                     |
|                                                    |
|   +--------+   +--------+   +--------+  +-------+  |
|   | BTN1   |   | BTN2   |   | BTN3   |  |Encoder|  |
|   +--------+   +--------+   +--------+  +-------+  |
|                                                    |
|   +-------------------+    +------------------+    |
|   | Network Interface |    | RGB LED Control  |    |
|   +-------------------+    +------------------+    |
|                                                    |
+----------------------------------------------------+
```

**Key Differences from Standard Node:**
- Additional buttons and rotary encoder
- Extended network interface options
- RGB LED control circuitry
- Larger ESP32 module area for future expansion

## Manufacturing Specifications

### Gerber File Export Settings

When generating Gerber files for manufacturing, use these settings:

1. **Format:**
   - RS-274X (Extended Gerber)
   - 2:5 precision (units in mm)
   - Absolute coordinates

2. **Required Layers:**
   - Top copper (GTL)
   - Bottom copper (GBL)
   - Top soldermask (GTS)
   - Bottom soldermask (GBS)
   - Top silkscreen (GTO)
   - Bottom silkscreen (GBO)
   - Board outline (GKO)
   - Drill file (TXT)

### Design Rules

These design rules are compatible with most PCB manufacturers:

| Parameter | Value | Notes |
|-----------|-------|-------|
| Minimum trace width | 8 mil (0.2mm) | 10 mil (0.25mm) recommended |
| Minimum spacing | 8 mil (0.2mm) | 10 mil (0.25mm) recommended |
| Minimum drill size | 0.3mm | 0.5mm for hand assembly |
| Minimum annular ring | 0.15mm | 0.2mm recommended |
| Minimum silkscreen width | 0.1mm | 0.15mm recommended |
| Via size | 0.6mm (drill) / 1.0mm (pad) | Tented vias recommended |

### BOM (Bill of Materials) Preparation

For efficient assembly, organize the BOM as follows:

1. Group components by type (resistors, capacitors, ICs, etc.)
2. Include component reference designators
3. Specify manufacturer part numbers when critical
4. Note polarity for all polarized components
5. Include component values and packages
6. Specify substitutions where allowed

## Assembly Guidelines

### PCB Assembly Process

Follow this sequence for optimal results:

1. **Visual Inspection:**
   - Check for manufacturing defects
   - Confirm hole alignment
   - Verify silkscreen markings

2. **Component Placement Order:**
   - SMD components (if any)
   - Low-profile components (resistors, diodes)
   - IC sockets
   - Capacitors
   - Connectors and headers
   - Mechanical components

3. **Soldering Guidelines:**
   - Use lead-free solder (e.g., Sn96.5/Ag3.0/Cu0.5)
   - Set iron temperature to 350°C (650°F)
   - Apply flux for difficult joints
   - Clean board with isopropyl alcohol after soldering

4. **Post-Assembly Inspection:**
   - Check for solder bridges
   - Verify component orientation
   - Confirm mechanical stability of larger components

### Component Placement Guide

![Component Placement Guide](../04-diagrams/component_placement_guide.png)

**Critical Component Orientations:**
- IC U1 (555 timer): Pin 1 marked with dot
- IC U2-U4 (CD4070): Pin 1 in top-left corner
- ESP32 module: USB connector facing board edge
- Electrolytic capacitors: Negative lead marked on PCB
- Diodes: Cathode band matches silkscreen marking

## Testing Procedures

### Visual Inspection Checklist

Before applying power, verify:

- [ ] No solder bridges between adjacent pins
- [ ] All components correctly oriented
- [ ] No missing components
- [ ] No cold solder joints (dull/grainy appearance)
- [ ] No damaged components from overheating
- [ ] No debris or flux residue that could cause shorts

### Electrical Testing Sequence

Perform tests in this order to prevent damage:

1. **Continuity Tests:**
   - Power to ground shorts (should show high resistance)
   - Ground continuity across board
   - Power rail continuity

2. **Power Supply Testing:**
   - Apply power gradually with current-limited supply
   - Verify 3.3V at test points
   - Check current consumption (should be <200mA)

3. **Oscillator Testing:**
   - Verify oscillator outputs with oscilloscope
   - Confirm adjustability across frequency range
   - Check duty cycle (approximately 50%)

4. **Phase Detector Testing:**
   - Inject test signals of known phase
   - Verify output voltage corresponds to phase difference

5. **ESP32 Testing:**
   - Check ESP32 boots correctly
   - Upload test firmware
   - Verify GPIO functionality

6. **Complete System Test:**
   - Run self-test firmware
   - Verify all interfaces function correctly
   - Test network communication between nodes

### Test Points

The PCB includes labeled test points for key signals:

| Test Point | Signal | Expected Value |
|------------|--------|----------------|
| TP1 | 5V Input | 5.0V DC |
| TP2 | 3.3V Rail | 3.3V DC |
| TP3 | OSC1 Out | Square wave, 2Hz |
| TP4 | OSC2 Out | Square wave, 3Hz |
| TP5 | OSC3 Out | Square wave, 5Hz |
| TP6 | Phase Det 1 | Varying DC, 0-3.3V |
| TP7 | Phase Det 2 | Varying DC, 0-3.3V |
| TP8 | Phase Det 3 | Varying DC, 0-3.3V |
| TP9 | GND | 0V |

## Design Files

### File Directory Structure

```
/pcb_designs/
  /node/
    node_main_schematic.pdf
    node_pcb_layout.pdf
    node_bom.csv
    /gerber/
      node_pcb.GTL  (Top Layer)
      node_pcb.GBL  (Bottom Layer)
      node_pcb.GTS  (Top Soldermask)
      node_pcb.GBS  (Bottom Soldermask)
      node_pcb.GTO  (Top Silkscreen)
      node_pcb.GBO  (Bottom Silkscreen)
      node_pcb.GKO  (Board Outline)
      node_pcb.TXT  (Drill File)
    /source/
      node_main.kicad_pcb
      node_main.kicad_sch
      node_main.pro
  
  /coordinator/
    coordinator_main_schematic.pdf
    coordinator_pcb_layout.pdf
    coordinator_bom.csv
    /gerber/
      coordinator_pcb.GTL
      coordinator_pcb.GBL
      coordinator_pcb.GTS
      coordinator_pcb.GBS
      coordinator_pcb.GTO
      coordinator_pcb.GBO
      coordinator_pcb.GKO
      coordinator_pcb.TXT
    /source/
      coordinator_main.kicad_pcb
      coordinator_main.kicad_sch
      coordinator_main.pro
```

### File Formats

The PCB designs are available in multiple formats:

1. **KiCad Format:**
   - Native KiCad 6.0 project files
   - Includes schematic, PCB layout, and libraries
   - Best for modifications or customizations

2. **Gerber Format:**
   - Industry-standard fabrication format
   - Compatible with all PCB manufacturers
   - RS-274X extended format

3. **PDF Format:**
   - Schematic and PCB layout as PDFs
   - Useful for reference and documentation
   - Includes dimensions and layer information

4. **CSV Format:**
   - Bill of Materials in spreadsheet format
   - Compatible with component ordering systems
   - Includes part numbers and quantities

### PCB Design Software

These designs were created with KiCad 6.0, a free and open-source PCB design software. To modify the designs:

1. Download and install KiCad 6.0 or newer from [kicad.org](https://www.kicad.org/)
2. Open the .pro project file
3. Use the Schematic Editor to modify the circuit
4. Use the PCB Editor to modify the board layout
5. Generate Gerber files using the built-in CAM processor

Alternative software that can import these designs:

- Autodesk EAGLE (using KiCad-to-EAGLE converter)
- Altium Designer (limited compatibility)
- EasyEDA (via Gerber import)

## Customization Options

### Layout Variants

These alternative layouts are available:

1. **Compact Version:**
   - Reduced dimensions: 80mm × 60mm
   - Components on both sides
   - Higher component density
   - Best for space-constrained applications

2. **Breadboard-Friendly Version:**
   - Single-sided layout
   - Through-hole components only
   - 0.1" grid spacing
   - Easier hand assembly

3. **High-Performance Version:**
   - Four-layer PCB construction
   - Dedicated power and ground planes
   - Improved signal integrity
   - Enhanced thermal performance

### Component Alternatives

The PCB supports these component variations:

1. **Oscillator Options:**
   - Through-hole 555 timer DIP-8
   - Surface-mount 555 timer SOIC-8
   - Crystal oscillator modules

2. **ESP32 Module Options:**
   - ESP32-WROOM module (standard)
   - ESP32-WROVER module (with PSRAM)
   - ESP32-PICO-D4 chip (space-constrained designs)

3. **Display Options:**
   - 0.96" OLED via I2C connector
   - 1.3" OLED via I2C connector
   - TFT LCD via SPI connector (expanded layout only)

## Ordering PCBs

### Recommended Manufacturers

These PCB manufacturers are recommended for the Prime Computer boards:

1. **JLCPCB:**
   - Economical pricing
   - Fast turnaround
   - Good quality for the price
   - [jlcpcb.com](https://jlcpcb.com/)

2. **PCBWay:**
   - Good balance of quality and price
   - Many finish options
   - Assembly service available
   - [pcbway.com](https://www.pcbway.com/)

3. **OSH Park:**
   - High-quality purple PCBs
   - Quick delivery in US
   - Perfect for prototypes
   - [oshpark.com](https://oshpark.com/)

### Ordering Specifications

When ordering, specify these parameters:

- Board thickness: 1.6mm
- Copper weight: 1oz (35μm)
- Surface finish: HASL lead-free
- Soldermask color: Blue (recommended)
- Silkscreen color: White
- Panelization: None (single boards)
- Electrical testing: Yes
- Edge connector beveling: No

### Cost Estimates

Approximate costs for manufacturing 3 nodes:

| Manufacturer | 3 PCBs | 5 PCBs | 10 PCBs | Lead Time |
|-------------|---------|---------|----------|----------|
| JLCPCB | $15-20 | $20-25 | $25-30 | 7-12 days |
| PCBWay | $20-25 | $25-30 | $30-40 | 7-14 days |
| OSH Park | $30-40 | $45-60 | $90-110 | 10-14 days |

*Prices include shipping to US/EU and are approximate as of 2025.

## Design Validation

The PCB designs have been validated through:

1. **Design Rule Checking (DRC):**
   - Clearance constraints verified
   - Trace width requirements met
   - Via specifications checked
   - No unconnected nets

2. **Electrical Rule Checking (ERC):**
   - No unconnected inputs
   - Power connections verified
   - No conflicting outputs
   - Proper pin assignments

3. **Thermal Analysis:**
   - Heat-generating components identified
   - Sufficient copper pour for thermal dissipation
   - Critical component spacing verified
   - Operating temperature ranges considered

4. **Signal Integrity:**
   - Trace impedances appropriate for signals
   - Minimal crosstalk between sensitive signals
   - Proper termination where needed
   - Ground return paths verified

## Further Resources

For additional PCB design guidance:

1. **KiCad Documentation:**
   - [KiCad Getting Started Guide](https://docs.kicad.org/6.0/en/getting_started_in_kicad/getting_started_in_kicad.html)

2. **PCB Design Best Practices:**
   - [Sparkfun PCB Basics](https://learn.sparkfun.com/tutorials/pcb-basics)
   - [Adafruit PCB Design Guidelines](https://learn.adafruit.com/ktowns-ultimate-creating-parts-in-eagle-tutorial)

3. **PCB Manufacturing Process:**
   - [How PCBs are Made](https://www.pcbway.com/blog/Engineering_Technical/How_PCBs_are_Made_A_Step_by_Step_Guide.html)

4. **Component Sourcing:**
   - [Mouser Electronics](https://www.mouser.com/)
   - [Digi-Key](https://www.digikey.com/)
   - [LCSC Electronics](https://www.lcsc.com/)