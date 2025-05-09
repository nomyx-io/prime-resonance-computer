# Technical Specifications and Schematics

## Introduction

This section provides comprehensive technical specifications, detailed schematics, PCB layouts, and component listings for the Prime Resonance Computer. These details are intended for advanced users who wish to create production-quality systems, understand the inner workings of the hardware, or modify the design for specialized applications.

## Complete System Specifications

### System Architecture

**Core System**

| Parameter | Specification | Details |
|-----------|--------------|---------|
| Processor | ESP32 | Dual-core Tensilica Xtensa LX6, 240 MHz |
| Memory | 520 KB SRAM | Internal ESP32 memory |
| Storage | 4 MB Flash | For firmware and configuration |
| Oscillators | 3× NE555 | Tuned to prime frequencies |
| Phase Detection | CD4070 | XOR-based phase comparison |
| Entropy Source | Zener-based | 3.3V zener noise amplifier |
| Display | 0.96" OLED | 128×64 resolution, I2C interface |

**Electrical Specifications**

| Parameter | Specification | Details |
|-----------|--------------|---------|
| Input Voltage | 5V DC | Center-positive 5.5×2.1mm barrel jack |
| Current Consumption | 150-250 mA | Per node, depending on activity |
| Operating Voltage | 3.3V | Main logic and analog circuits |
| Signal Levels | 0-3.3V | Digital I/O, analog within ADC range |
| ADC Resolution | 12-bit | 0-4095 levels |

**Environmental Specifications**

| Parameter | Specification | Details |
|-----------|--------------|---------|
| Operating Temperature | 10-40°C | 20-25°C recommended for precision |
| Storage Temperature | -20-60°C | Non-operating |
| Operating Humidity | 20-80% RH | Non-condensing |
| Altitude | Up to 2000m | Standard atmospheric pressure |
| EMC Compatibility | Class B | Normal residential environment |

### Performance Specifications

**Oscillator Performance**

| Parameter | Target Value | Tolerance | Typical Performance |
|-----------|-------------|-----------|---------------------|
| OSC1 Frequency | 2.00 Hz | ±0.05 Hz | ±0.02 Hz after calibration |
| OSC2 Frequency | 3.00 Hz | ±0.05 Hz | ±0.03 Hz after calibration |
| OSC3 Frequency | 5.00 Hz | ±0.05 Hz | ±0.05 Hz after calibration |
| Frequency Stability | N/A | <0.5% drift/hour | <0.2% typical after warmup |
| Duty Cycle | 50% | ±5% | ±3% typical |
| Rise/Fall Time | <1 ms | N/A | 0.5 ms typical |

**Phase Detection Performance**

| Parameter | Value | Details |
|-----------|-------|---------|
| Resolution | 0.35° | Minimum detectable phase difference |
| Range | 0-360° | Full phase detection range |
| Accuracy | ±2° | After calibration |
| Readout Rate | 100 Hz | Default sampling frequency |

**Computational Performance**

| Operation | Time | Notes |
|-----------|------|-------|
| Prime Verification (6-digit) | 3-15 seconds | Depends on number properties |
| Factorization (6-digit) | 5-60 seconds | Depends on number complexity |
| Pattern Recognition | 1-10 seconds | Depends on pattern length |
| Random Number Generation | 100 numbers/sec | True random, not pseudo-random |

**Network Performance**

| Parameter | Value | Notes |
|-----------|-------|-------|
| Communication Range | 10-30 meters | Line of sight, indoor environment |
| Throughput | 150 Kbps | Effective ESP-NOW data rate |
| Latency | 15-40 ms | Typical round trip time |
| Maximum Nodes | 20 | Practical limit per network |

### Physical Specifications

**Dimensions**

| Component | Dimensions (mm) | Notes |
|-----------|----------------|-------|
| PCB | 100 × 80 × 1.6 | FR4, double-sided, 1oz copper |
| Enclosure | 120 × 100 × 40 | Including standoffs and connectors |
| Display Area | 25 × 14 | Active display area |
| Full System (3 nodes) | 370 × 150 × 50 | Approximate footprint |

**Weight**

| Component | Weight (g) | Notes |
|-----------|-----------|-------|
| PCB with Components | 75 | Fully assembled |
| Enclosure | 120 | Including fasteners |
| Complete Node | 200 | Including cabling |
| Full System (3 nodes) | 650 | Including power adapters |

## Detailed Schematics

### Power Supply Circuit

![Power Supply Schematic](diagrams/schematic_power.png)

```
                            AMS1117-3.3
                             ┌──────┐
                 C1    R1    │      │
   5V Input ○──┬──▒▒▒──┬────┤IN OUT├───┬──○ 3.3V Output
                │       │    │      │   │
                ▽       │    │ ADJ  │   ▽
              10µF      │    └──┬───┘ 10µF
                        │       │       │
   GND       ○──────────┴───────┴───────┴──○ GND

   Components:
   - C1, C3: 10µF electrolytic capacitor
   - C2: 0.1µF ceramic capacitor
   - R1: 10Ω resistor
   - U1: AMS1117-3.3 voltage regulator
```

### Oscillator Circuit (Single Unit)

![Oscillator Schematic](diagrams/schematic_oscillator.png)

```
   3.3V ○───────┬────────────┬────────────┐
                │            │            │
                │            │            │
               R1           R2           C3
              10kΩ         4.7kΩ        10µF
                │            │            │
                │    ┌───────┴─┐          │
                ├────┤7       8├──────────┘
                │    │         │
                │    │         │
                │    │ 555     │
                │    │         │
                ├────┤6       3├───┬──○ Output to ESP32
                │    │         │   │
                │    │         │   │
                │    │         │   └──○ To Phase Detectors
                │    │         │
   GND ○────────┼────┤1   5   │
                │    └─────┬───┘
                │          │
                │          ▽
                │         0.1µF
                │          │
                │          │
                ▽          │
              0.01µF       │
                │          │
                └──────────┴───────○ GND

   Components:
   - R1: 10kΩ resistor
   - R2: 4.7kΩ resistor
   - R3: 10kΩ trim potentiometer (not shown, in parallel with R2)
   - C1: 0.01µF ceramic capacitor
   - C2: 0.1µF ceramic capacitor
   - C3: 10µF electrolytic capacitor
   - U1: NE555 timer IC
```

### Phase Detector Circuit (Single Unit)

![Phase Detector Schematic](diagrams/schematic_phase_detector.png)

```
   Osc1 ○─────┬──────┐
              │      │
              │   ┌──┴───┐
              │   │A    Y│
              │   │      ├───┬───────┬──○ To ESP32 ADC
              │   │ XOR  │   │       │
              │   │      │   │       │
              │   │B     │   R1     C1
   Osc2 ○─────┴───┴──────┘  10kΩ   0.1µF
                              │       │
                              │       │
   GND  ○────────────────────┴───────┴──○ GND

   Components:
   - U1: CD4070 XOR gate (1/4 of the IC)
   - R1: 10kΩ resistor
   - C1: 0.1µF ceramic capacitor
```

### Entropy Source Circuit

![Entropy Source Schematic](diagrams/schematic_entropy.png)

```
   5V  ○──────────┬──────────────────┐
                 R1                   │
                10kΩ                  │
                  │                   │
                  │                   │
                  │                   │
                  ▼                  R4
                 D1                  1kΩ
              3.3V Zener              │
                  │                   │
                  │    ┌───────┐      │
                  └────┤+      │      │
                       │       ├──────┴──○ To ESP32 ADC
                       │ LM358 │
   GND ○───────────────┤-      │
                       └───┬───┘
                           │
                           │
                           └─────────────○ GND

   Components:
   - R1: 10kΩ resistor
   - R4: 1kΩ resistor
   - D1: 3.3V Zener diode
   - U1: LM358 operational amplifier (1/2 of the IC)
```

### ESP32 Connection Diagram

![ESP32 Connection Schematic](diagrams/schematic_esp32.png)

```
                         ┌───────────────────────┐
                         │                       │
                         │       ESP32           │
                         │                       │
   3.3V ○────────────────┤3V3             GPIO5 ├────○ OSC1 Input
                         │                       │
   GND  ○────────────────┤GND             GPIO18├────○ OSC2 Input
                         │                       │
                         │                 GPIO19├────○ OSC3 Input
                         │                       │
                         │                 GPIO34├────○ Phase Det 1
                         │                       │
                         │                 GPIO35├────○ Phase Det 2
                         │                       │
                         │                 GPIO32├────○ Phase Det 3
                         │                       │
                         │                 GPIO33├────○ Entropy
                         │                       │
                         │                 GPIO21├────○ SDA (Display)
                         │                       │
                         │                 GPIO22├────○ SCL (Display)
                         │                       │
   USB ○─────────────────┤USB                    │
                         │                       │
                         └───────────────────────┘
```

## Complete Bill of Materials (BOM)

### Core Electronics

| ID | Component | Specifications | Quantity (per node) | Typical Part Number | Estimated Cost |
|----|-----------|---------------|---------------------|---------------------|---------------|
| U1 | ESP32 Development Board | 30-pin DevKitC | 1 | ESP32-WROOM-32D | $5-10 |
| U2-U4 | 555 Timer IC | DIP-8 | 3 | NE555P | $0.50 each |
| U5 | XOR Gate IC | DIP-14, Quad XOR | 1 | CD4070BE | $0.75 |
| U6 | Operational Amplifier | DIP-8, Dual Op-Amp | 1 | LM358P | $0.50 |
| U7 | Voltage Regulator | 3.3V, 1A, TO-220 | 1 | AMS1117-3.3 | $0.40 |
| D1 | Zener Diode | 3.3V, DO-35 | 1 | 1N4728A | $0.20 |
| DISP1 | OLED Display | 0.96", 128×64, I2C | 1 | SSD1306 | $3-5 |

### Resistors (all 1/4W, ±5% unless specified)

| ID | Value | Quantity (per node) | Notes |
|----|-------|---------------------|-------|
| R1-R3 | 10kΩ | 5 | Pull-up and biasing |
| R4-R6 | 1kΩ | 5 | Signal isolation |
| R7-R9 | 4.7kΩ | 3 | 555 timing |
| R10-R12 | 10kΩ | 3 | Trim potentiometers |
| R13 | 10Ω | 1 | Power supply filter |

### Capacitors

| ID | Value | Type | Quantity (per node) | Notes |
|----|-------|------|---------------------|-------|
| C1-C3 | 0.1μF | Ceramic | 5 | Decoupling |
| C4-C6 | 0.01μF | Ceramic | 3 | 555 timing |
| C7-C10 | 10μF | Electrolytic | 4 | Power filtering |
| C11 | 100μF | Electrolytic | 1 | Main filter |

### Connectors and Power

| ID | Component | Specifications | Quantity (per node) | Notes |
|----|-----------|---------------|---------------------|-------|
| J1 | DC Power Jack | 5.5×2.1mm | 1 | Barrel connector |
| J2-J5 | Pin Headers | 2.54mm pitch | 40 pins | For connections and jumpers |
| SW1 | Power Switch | SPST | 1 | Toggle switch |
| SW2-SW4 | Pushbuttons | Momentary, NO | 3 | User interface |
| PWR1 | Power Adapter | 5V, 2A | 1 | Center positive |

### Mechanical Components

| ID | Component | Specifications | Quantity (per node) | Notes |
|----|-----------|---------------|---------------------|-------|
| PCB1 | Printed Circuit Board | 100×80mm, 2-layer | 1 | Main board |
| BOX1 | Enclosure | 120×100×40mm | 1 | Acrylic or 3D printed |
| M1-M4 | Standoffs | M3, 10mm | 4 | PCB mounting |
| M5-M8 | Screws | M3, 6mm | 8 | Case and PCB mounting |
| F1-F4 | Rubber Feet | Adhesive | 4 | Case stability |

### Total Cost Estimate (per node)

| Category | Cost Range |
|----------|------------|
| Core Electronics | $11-18 |
| Passive Components | $5-10 |
| Connectors & Power | $8-15 |
| PCB | $5-20 |
| Enclosure | $10-30 |
| **Total per Node** | **$39-93** |
| **Complete 3-Node System** | **$117-279** |

Additional costs may include tools, shipping, and taxes. The wide price range reflects variations in component quality and sourcing methods.

## PCB Design

### PCB Layout Guidelines

The Prime Resonance Computer PCB follows these design principles:

1. **Separation of Analog and Digital**
   - Analog section (oscillators, phase detectors) isolated from digital
   - Separate ground planes with single-point connection
   - Power filtering between sections

2. **Signal Integrity**
   - Short traces for critical signals
   - Ground plane under all high-speed signals
   - Proper termination for sensitive lines

3. **Component Placement**
   - Heat-generating components (voltage regulator) away from sensitive circuits
   - User interface elements (buttons, display) accessible at enclosure edges
   - Test points for key signals

4. **EMI/EMC Considerations**
   - Ground plane on bottom layer
   - Input/output filtering
   - Proper bypass capacitor placement

### PCB Layout

![PCB Layout](diagrams/pcb_layout.png)

- Top layer: Red
- Bottom layer: Blue
- Silkscreen: White
- Board outline: Yellow

PCB dimensions: 100mm × 80mm

### Layer Stack

| Layer | Material | Thickness |
|-------|----------|-----------|
| Top Copper | Copper | 1oz (35μm) |
| Substrate | FR4 | 1.6mm |
| Bottom Copper | Copper | 1oz (35μm) |

### PCB Manufacturing Specifications

| Parameter | Value | Notes |
|-----------|-------|-------|
| Layers | 2 | Double-sided |
| Material | FR4 | Standard PCB material |
| Thickness | 1.6mm | Standard thickness |
| Minimum Track Width | 10mil | For standard manufacturing |
| Minimum Spacing | 10mil | For standard manufacturing |
| Copper Weight | 1oz | Standard thickness |
| Surface Finish | HASL or ENIG | ENIG preferred for fine-pitch components |
| Soldermask | Green | Standard |
| Silkscreen | White | Standard |

The Gerber files for PCB manufacturing are available in the `hardware/pcb` directory.

## Enclosure Design

### Enclosure Specifications

The enclosure consists of laser-cut or 3D printed panels:

1. **Bottom Panel**
   - Contains mounting holes for PCB standoffs
   - Ventilation holes for thermal management
   - Four rubber feet for stability

2. **Top Panel**
   - Cutout for OLED display
   - Holes for buttons and LEDs
   - System logo and labeling

3. **Side Panels**
   - Slots for assembling with top and bottom
   - Cutout for power connector
   - Ventilation slots

### 3D Printable Design

![Enclosure Design](diagrams/enclosure_3d.png)

- Material: PLA or ABS
- Wall thickness: 2mm
- Infill: 20% minimum
- Resolution: 0.2mm layer height recommended
- Print time: Approximately 6 hours for all parts

STL files for 3D printing are available in the `hardware/enclosure` directory.

### Laser Cut Design

The enclosure can also be fabricated from 3mm acrylic using these design files:

![Laser Cut Design](diagrams/enclosure_laser.png)

- Material: 3mm cast acrylic
- Assembly method: Tab and slot with acrylic cement
- Cutting specifications: CO2 laser cutter, 60W+
- Color: Clear or smoked acrylic recommended

DXF files for laser cutting are available in the `hardware/enclosure` directory.

## Component Selection Guidelines

### Critical Component Selection

1. **ESP32 Module**
   - Recommendation: Official Espressif ESP32-DevKitC or ESP32-WROOM-32D
   - Alternatives: NodeMCU-32S, DOIT ESP32 DevKit
   - Avoid: Clones with unmarked chips, as they may have unstable performance

2. **555 Timer ICs**
   - Recommendation: Texas Instruments NE555P or NA555P
   - Alternatives: LM555CN, ST NE555N
   - Avoid: Ultra-low power variants which have different timing characteristics

3. **OLED Display**
   - Recommendation: 0.96" SSD1306 I2C display (128×64)
   - Alternatives: SH1106 with appropriate driver changes
   - Key parameter: I2C address (0x3C or 0x3D)

4. **Voltage Regulator**
   - Recommendation: AMS1117-3.3
   - Alternatives: LM1117-3.3, LM3940
   - Required specs: 3.3V fixed output, 1A minimum current, low dropout

### Component Tolerances

For optimal performance, use components with these tolerances:

| Component | Recommended Tolerance | Impact |
|-----------|----------------------|--------|
| Resistors | ±1% | Critical for oscillator timing |
| Capacitors (timing) | ±5% | Critical for frequency stability |
| Capacitors (filtering) | ±20% | Non-critical |
| Potentiometers | Linear taper | For precise adjustment |
| Voltage Regulator | ±2% output accuracy | Power stability |

## Manufacturing Notes

### PCB Assembly

For manual assembly:

1. Start with SMD components (if any)
2. Install low-profile components (resistors, diodes)
3. Install IC sockets (recommended for all ICs)
4. Install capacitors and larger components
5. Install connectors and mechanical parts last
6. Clean flux residue with isopropyl alcohol
7. Visually inspect all solder joints

For automated assembly:

1. Most components are through-hole for easy manual assembly
2. SMD versions of the design are available for professional manufacturing
3. BOM in CSV format compatible with JLC PCB and similar services is provided

### Testing Procedures

Each assembled board should undergo these tests:

1. **Visual Inspection**
   - Check for solder bridges
   - Verify component orientation
   - Check for missing components

2. **Power Test**
   - Apply power with current limiter set to 300mA
   - Check voltage at test points:
     - TP1: 5V (±0.25V)
     - TP2: 3.3V (±0.1V)
   - Check current draw (<200mA normal)

3. **Functional Tests**
   - Install test firmware
   - Check oscillator outputs with oscilloscope
   - Verify OLED display operation
   - Perform button and LED tests

## Next Steps

With these detailed specifications, you have all the technical information needed to build a production-quality Prime Resonance Computer. The next section provides a complete firmware reference for those interested in understanding or modifying the system's software functionality.