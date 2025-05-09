# Appendix B: Parts List and Sourcing Guide

## Introduction

This appendix provides a comprehensive list of all components required to build the Prime Resonance Computer, along with sourcing information, estimated costs, and alternatives. Our goal is to keep the total cost under $500 while ensuring reliable operation.

The components are organized by subsystem, matching the assembly sequence in Section 2. For each component, we provide:
- Exact specifications and parameters
- Quantity required
- Estimated cost range
- Alternative options
- Sourcing suggestions

## Complete Bill of Materials

### Core Processing Components

| Component | Specification | Quantity | Est. Cost | Notes |
|-----------|--------------|----------|-----------|-------|
| ESP32 Development Board | ESP32-DEVKIT-C or equivalent | 1 | $8-15 | NodeMCU-32S or similar alternatives are acceptable |
| Breadboard | Full-size (830+ tie points) | 1 | $5-10 | For prototyping; optional if using custom PCB |
| Jumper Wires | Male-to-male, various lengths, pack | 1 | $3-8 | Assorted colors recommended for easier circuit tracing |
| USB Cable | Micro-USB or USB-C (depending on ESP32 board) | 1 | $2-5 | Data-capable cable, not charge-only |
| **Subtotal** | | | **$18-38** | |

### Oscillator Components

| Component | Specification | Quantity | Est. Cost | Notes |
|-----------|--------------|----------|-----------|-------|
| 555 Timer IC | NE555 or equivalent | 3 | $2-6 | Can substitute TLC555 (CMOS version) |
| DIP Socket (8-pin) | Standard 8-pin DIP socket | 3 | $1-3 | Optional but recommended for easy replacement |
| Resistor 4.7kΩ | 1/4W, ±5% or better | 3 | $0.30-1 | For oscillator timing circuits |
| Resistor 15kΩ | 1/4W, ±5% or better | 1 | $0.10-0.50 | For 5Hz oscillator |
| Resistor 30kΩ | 1/4W, ±5% or better | 1 | $0.10-0.50 | For 3Hz oscillator |
| Resistor 47kΩ | 1/4W, ±5% or better | 1 | $0.10-0.50 | For 2Hz oscillator |
| Capacitor 1µF | Ceramic or film | 3 | $0.60-2 | For oscillator timing |
| Trimmer Potentiometer 10kΩ | 3-pin, multi-turn | 3 | $3-9 | For fine frequency adjustment; optional but recommended |
| **Subtotal** | | | **$7.20-22.50** | |

### Phase Detector Components

| Component | Specification | Quantity | Est. Cost | Notes |
|-----------|--------------|----------|-----------|-------|
| Quad XOR Gate | CD4070B or 74HC86 | 1 | $0.75-2 | One chip contains all needed XOR gates |
| DIP Socket (14-pin) | Standard 14-pin DIP socket | 1 | $0.50-1 | Optional but recommended for easy replacement |
| Resistor 10kΩ | 1/4W, ±5% or better | 3 | $0.30-1 | For phase detector low-pass filters |
| Capacitor 0.1µF | Ceramic | 3 | $0.60-1.50 | For phase detector low-pass filters |
| **Subtotal** | | | **$2.15-5.50** | |

### Entropy Source Components

| Component | Specification | Quantity | Est. Cost | Notes |
|-----------|--------------|----------|-----------|-------|
| Zener Diode | BZX79C3V3 or similar 3.3V | 1 | $0.30-1 | For noise generation |
| Resistor 1kΩ | 1/4W, ±5% or better | 1 | $0.10-0.30 | For biasing the zener diode |
| Resistor 10kΩ | 1/4W, ±5% or better | 1 | $0.10-0.30 | Already counted in phase detector section |
| Capacitor 0.01µF | Ceramic | 1 | $0.20-0.50 | For noise filtering |
| **Subtotal** | | | **$0.70-2.10** | |

### Display and User Interface Components

| Component | Specification | Quantity | Est. Cost | Notes |
|-----------|--------------|----------|-----------|-------|
| OLED Display | 128x64, I2C interface, SSD1306 | 1 | $5-15 | 0.96" or 1.3" size recommended |
| Tactile Pushbuttons | Standard momentary buttons | 2 | $0.50-2 | For user input |
| LED (various colors) | 5mm or 3mm standard LED | 4 | $0.40-2 | Status indicators |
| Resistor 330Ω | 1/4W, ±5% or better | 4 | $0.40-1 | Current limiting for LEDs |
| Resistor 10kΩ | 1/4W, ±5% or better | 2 | $0.20-0.60 | Pull-up for buttons (optional if using ESP32 internal pull-ups) |
| **Subtotal** | | | **$6.50-20.60** | |

### Power Supply Components

| Component | Specification | Quantity | Est. Cost | Notes |
|-----------|--------------|----------|-----------|-------|
| Voltage Regulator 5V | LM7805 or AMS1117-5.0 | 1 | $0.50-2 | Optional if powering directly from USB |
| Capacitor 0.33µF | Ceramic | 1 | $0.20-0.50 | For voltage regulator input filtering |
| Capacitor 0.1µF | Ceramic | 1 | $0.20-0.50 | For voltage regulator output filtering |
| Power Jack | DC barrel jack, 5.5x2.1mm | 1 | $0.50-2 | Optional if powering directly from USB |
| **Subtotal** | | | **$1.40-5.00** | |

### Housing and Mechanical Components

| Component | Specification | Quantity | Est. Cost | Notes |
|-----------|--------------|----------|-----------|-------|
| Enclosure | ABS plastic, approx. 150x100x50mm | 1 | $8-20 | Project box with space for all components |
| PCB | Custom PCB or prototyping board | 1 | $5-30 | Perfboard works well for prototype |
| Standoffs & Screws | M3, various lengths | 8 | $3-8 | For mounting components |
| Heat Shrink Tubing | Assorted sizes, pack | 1 | $2-5 | For insulating connections |
| Wire | 22AWG solid core, various colors | 1 roll | $3-10 | For connections not using jumper wires |
| **Subtotal** | | | **$21-73** | |

### Tools Required (if not already owned)

| Tool | Specification | Quantity | Est. Cost | Notes |
|-----------|--------------|----------|-----------|-------|
| Soldering Iron | 30-60W, temperature controlled | 1 | $20-50 | Basic models start at $20, recommended models $30-40 |
| Solder | Rosin core, 0.6-0.8mm, lead-free | 1 | $5-10 | 50g-100g spool sufficient |
| Digital Multimeter | Basic voltage/current/resistance measurement | 1 | $15-40 | Essential for testing and troubleshooting |
| Wire Cutters | Small electronics type | 1 | $5-15 | For trimming component leads |
| Wire Strippers | 20-30 AWG capacity | 1 | $5-15 | For preparing wires |
| Small Screwdrivers | Phillips #0/#1 and flat head set | 1 set | $5-15 | For assembly and adjustments |
| Needle-nose Pliers | Small electronics type | 1 | $5-10 | For bending leads and general assembly |
| **Subtotal** | | | **$60-155** | |

### Optional Equipment

| Item | Specification | Quantity | Est. Cost | Notes |
|-----------|--------------|----------|-----------|-------|
| Oscilloscope | Digital, 2+ channels, 20MHz+ | 1 | $100-400 | Tremendously helpful but not required; used models or USB scopes are more affordable |
| Logic Analyzer | 8+ channels | 1 | $30-100 | Useful for digital signal debugging |
| Bench Power Supply | Adjustable, 0-12V, 1A+ | 1 | $40-100 | For testing and development |
| 3D Printer | FDM type | 1 | $200-500 | For creating custom enclosures and brackets |
| **Subtotal** | | | **$370-1100** | |

### Development Software

| Software | License | Est. Cost | Notes |
|-----------|--------------|----------|-------|
| Arduino IDE | Open Source | $0 | For programming the ESP32 |
| KiCad | Open Source | $0 | For designing custom PCBs |
| FreeCAD | Open Source | $0 | For designing 3D-printable parts |
| ESP32 Development Framework | Open Source | $0 | Libraries and tools for ESP32 |
| **Subtotal** | | **$0** | |

### Total Cost Summary

| Category | Cost Range |
|----------|------------|
| Core Processing Components | $18-38 |
| Oscillator Components | $7.20-22.50 |
| Phase Detector Components | $2.15-5.50 |
| Entropy Source Components | $0.70-2.10 |
| Display and User Interface | $6.50-20.60 |
| Power Supply Components | $1.40-5.00 |
| Housing and Mechanical | $21-73 |
| **Total Base System** | **$56.95-166.70** |
| Tools (if needed) | $60-155 |
| Optional Equipment | $370-1100 |

As shown, the base system can be built for approximately $57-167, well within the $500 budget even with the purchase of necessary tools. The optional equipment, while helpful, is not required and would exceed the budget constraint if all purchased new.

## Alternative Components Options

### ESP32 Alternatives

Several ESP32 development board options are suitable for this project:

| Alternative | Pros | Cons | Est. Cost |
|-------------|------|------|-----------|
| NodeMCU-32S | Breadboard friendly, widely available | Larger footprint | $8-12 |
| ESP32-WROOM-32 | Compact size, lower power consumption | Fewer exposed pins, requires adapter for breadboard | $6-10 |
| ESP32-WROVER | Includes PSRAM for larger projects | Higher cost | $10-15 |
| ESP32-S2 | Newer USB capabilities | Fewer GPIO pins | $8-12 |
| ESP32-C3 | RISC-V core, very affordable | Fewer features | $5-8 |

### Display Alternatives

Several display options can be used:

| Alternative | Pros | Cons | Est. Cost |
|-------------|------|------|-----------|
| SH1106 OLED (1.3") | Larger display area | Slightly different driver required | $8-15 |
| Nokia 5110 LCD | Very low power, high contrast | Monochrome, lower resolution | $3-8 |
| 0.91" OLED | Very compact | Limited display area | $4-10 |
| Character LCD 16x2 | Simple interface, robust | Limited graphics capabilities | $5-10 |
| TFT Color Display | Full color, higher resolution | More complex interface, higher power | $10-20 |

### Oscillator Alternatives

Alternative oscillator implementations:

| Alternative | Pros | Cons | Est. Cost |
|-------------|------|------|-----------|
| Crystal Oscillator + 4060 Counter | More stable frequency | More complex circuit, less adjustable | $3-8 per oscillator |
| Si5351 Clock Generator | Digital control, multiple outputs | I2C interface adds complexity | $8-15 for all oscillators |
| Arduino Nano (as oscillator source) | Software adjustable, highly flexible | Overkill for the task, higher cost | $5-12 per oscillator |
| LTC6900 Oscillator | Precision, low power | Higher cost, SOT23 package harder to work with | $3-5 per oscillator |

### Enclosure Alternatives

Housing options for the project:

| Alternative | Pros | Cons | Est. Cost |
|-------------|------|------|-----------|
| 3D Printed Custom Case | Perfect fit, customizable | Requires 3D printer or service | $5-25 |
| Transparent Acrylic Box | Showcases internal components | Requires precise cutting | $10-20 |
| Wooden Box | Aesthetic appeal | Manual fabrication required | $10-25 |
| Repurposed Electronic Case | Free/cheap, ready-made | May require modification | $0-10 |
| Cardboard Prototype | Very low cost for testing | Not durable, unprofessional appearance | $0-1 |

## Recommended Suppliers

Below are suggested suppliers for procuring components:

### Global Online Retailers

1. **Mouser Electronics** (mouser.com)
   - Wide selection of professional-grade components
   - Excellent documentation and specifications
   - Higher prices but guaranteed quality

2. **Digikey** (digikey.com)
   - Comprehensive inventory of electronic components
   - Fast shipping, reliable quality
   - Detailed search filters and datasheets

3. **LCSC Electronics** (lcsc.com)
   - Lower costs, especially for bulk orders
   - Many components used in this project available
   - Longer shipping times for some regions

4. **Amazon** (amazon.com)
   - Fast shipping with Prime
   - ESP32 development boards and basic components readily available
   - Variable quality; check reviews carefully

5. **eBay** (ebay.com)
   - Often has lowest prices, especially for bulk components
   - Wide variety of new and used test equipment
   - Quality can be inconsistent; check seller ratings

### Regional Electronics Retailers

Depending on your location, these retailers may offer in-store pickup or faster shipping:

- **United States**: Micro Center, Fry's Electronics, Jameco
- **United Kingdom**: RS Components, CPC Farnell
- **Europe**: Conrad Electronic, Reichelt Elektronik
- **Australia**: Jaycar, Altronics
- **Canada**: Sayal Electronics, Active Tech

### Maker-Focused Suppliers

These retailers cater specifically to hobbyists and makers:

1. **Adafruit** (adafruit.com)
   - High-quality components
   - Excellent tutorials and support
   - ESP32 boards, displays, and accessories

2. **SparkFun** (sparkfun.com)
   - Well-documented components
   - Educational resources
   - Breakout boards that simplify complex components

3. **Seeed Studio** (seeedstudio.com)
   - Good prices on development boards
   - PCB fabrication services
   - Many maker-focused components

### PCB Manufacturing

If creating a custom PCB:

1. **JLCPCB** (jlcpcb.com)
   - Very affordable PCB production
   - Component assembly services available
   - Quick turnaround for simple boards

2. **PCBWay** (pcbway.com)
   - Competitive pricing
   - Good quality control
   - Various board specifications available

3. **OSH Park** (oshpark.com)
   - High-quality purple PCBs
   - Small batch-friendly pricing
   - US-based manufacturing

## Budget Options and Alternatives

### Minimal Viable System (~$30-50)

For the most cost-conscious approach:

1. **Use breadboard only** (no custom PCB)
2. **ESP32 Development Board**: Inexpensive ESP32-C3 or NodeMCU-32S
3. **Display**: Nokia 5110 LCD instead of OLED
4. **Enclosure**: Repurposed container or simple acrylic sheet construction
5. **Power**: USB only, no separate power supply components
6. **Common Components**: Source resistors and capacitors from assorted packs

### Mid-Range System (~$70-120)

A balanced approach with improved reliability:

1. **Construction**: Perfboard soldered assembly
2. **ESP32**: Standard ESP32-DEVKIT-C or similar
3. **Display**: Standard 0.96" OLED display
4. **Oscillators**: Add trimmer potentiometers for fine adjustment
5. **Enclosure**: Basic project box with custom front panel
6. **Power**: USB power with basic filtering components

### Enhanced System (~$150-250)

For better performance and features:

1. **Construction**: Custom PCB (single or double-sided)
2. **ESP32**: ESP32-WROVER with PSRAM
3. **Display**: Larger 1.3" OLED or small TFT color display
4. **Oscillators**: Crystal-based for better stability
5. **Enclosure**: Custom 3D-printed enclosure
6. **Power**: Proper regulated power supply with filtering
7. **Extras**: Additional visualization LEDs, higher precision components

### Full Research System (~$300-500)

For serious exploration and development:

1. **Construction**: Professional PCB with solder mask and silkscreen
2. **Processing**: Multiple ESP32 boards for distributed computing
3. **Display**: High-resolution TFT display
4. **Oscillators**: Precision oscillator modules with temperature compensation
5. **Measurement**: Additional sensors for environmental monitoring
6. **Enclosure**: Professional enclosure with proper labeling
7. **Interfaces**: Additional connectivity options (Ethernet, BLE, etc.)

## Tools Required

### Essential Tools

These tools are necessary for successful assembly:

1. **Soldering Iron**: 30-60W adjustable temperature
   - Recommended: Hakko FX-888D or similar quality ($100+)
   - Budget option: Any adjustable temperature iron ($20-30)

2. **Digital Multimeter**: For measuring voltage, resistance, continuity
   - Recommended: Fluke 101 or similar ($50+)
   - Budget option: Any basic multimeter with continuity test ($10-15)

3. **Small Hand Tools**:
   - Wire cutters/strippers
   - Needle-nose pliers
   - Phillips and flat-head screwdrivers
   - Tweezers for small components

4. **Computer** with USB port for programming the ESP32

### Optional But Recommended Tools

These tools make the project easier but aren't strictly necessary:

1. **Oscilloscope**: For visualizing and debugging signals
   - Budget option: Hantek USB scope ($80-150)
   - Entry-level standalone: Rigol DS1054Z or similar ($350+)
   - Absolute minimum: Smartphone oscilloscope apps with simple probe circuit ($5-20)

2. **Logic Analyzer**: For digital signal debugging
   - Budget option: 8-channel USB logic analyzer ($10-30)
   - Better option: Saleae Logic clone ($30-50)

3. **Bench Power Supply**: For controlled power during testing
   - Budget option: Fixed output wall adapters with USB testing module ($15-25)
   - Better option: Adjustable bench supply with current limiting ($40-100)

4. **Third Hand Tool**: For holding PCBs and components during soldering
   - Budget option: Simple holder with clips ($5-15)
   - Better option: Adjustable model with magnifying glass ($15-30)

5. **ESP32 Programmer**: For firmware flashing
   - Budget option: USB-to-Serial adapter if not included with ESP32 board ($3-10)

### Software Tools

Required software tools (all free):

1. **Arduino IDE**: For ESP32 programming
   - Ensure you install ESP32 board support
   - Add libraries: Adafruit SSD1306, Adafruit GFX

2. **Serial Terminal**:
   - Arduino IDE built-in Serial Monitor
   - Alternatives: PuTTY, Serial, or CoolTerm

3. **Circuit Design** (optional):
   - KiCad for PCB design
   - Fritzing for breadboard layout visualization

## Conclusion

Building the Prime Resonance Computer requires a modest investment in electronic components and tools. The complete system can be built for well under the $500 budget, even if purchasing tools specifically for this project. By selecting components based on availability and budget constraints, you can optimize the system for your specific needs.

The parts list provided here represents a baseline configuration that has been tested and verified to work. However, the system is designed to be flexible, allowing for substitutions and enhancements based on available components and desired features.

Remember that bargain-basement components (especially from unverified sources) may lead to inconsistent behavior in the Prime Resonance Computer, where precision and stability are important. When possible, choose quality components for critical elements like oscillators and phase detection circuits.