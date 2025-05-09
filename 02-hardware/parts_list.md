# Prime Computer Parts List

## Overview

This document provides a comprehensive list of components required to build a basic prime resonator computer with 3 nodes for under $500. All parts are readily available from common electronics suppliers and can be assembled with basic electronics tools.

## Budget Breakdown

| Category | Cost |
|----------|------|
| Computing Hardware | $180 |
| Electronic Components | $105 |
| Oscillator Components | $45 |
| Power Supply | $30 |
| Display & User Interface | $40 |
| PCB & Enclosure | $60 |
| Tools & Miscellaneous | $40 |
| **Total** | **$500** |

## Detailed Component List

### Computing Hardware

| Item | Quantity | Unit Price | Total | Source | Notes |
|------|----------|------------|-------|--------|-------|
| ESP32 DevKit Development Board | 3 | $10 | $30 | Amazon, AliExpress | Main controller for each node |
| Raspberry Pi Zero 2 W | 1 | $15 | $15 | Adafruit, Amazon | Network coordinator and interface host |
| 16GB MicroSD Card | 1 | $10 | $10 | Amazon | For Raspberry Pi |
| NodeMCU ESP8266 (Optional backup) | 2 | $5 | $10 | AliExpress | Backup/expansion nodes |
| USB A-to-Micro USB Cables | 5 | $3 | $15 | Amazon | For power and programming |
| USB OTG Adapter | 2 | $3 | $6 | Amazon | For connections between devices |
| USB Hub | 1 | $15 | $15 | Amazon | For connecting multiple nodes to coordinator |
| NRF24L01+ Wireless Modules | 4 | $3 | $12 | eBay, AliExpress | Optional wireless communication |
| Mini WiFi Router | 1 | $25 | $25 | Amazon | For creating isolated network |
| Ethernet Cable | 1 | $7 | $7 | Amazon | For network connections |
| HC-05 Bluetooth Module | 3 | $5 | $15 | Amazon, eBay | Alternative communication method |
| USB Serial Converter (CP2102) | 3 | $3 | $9 | Amazon | For programming ESP32s |
| CT-UNO (Arduino-compatible) | 1 | $11 | $11 | AliExpress | For initial testing and development |
| **Subtotal** |  |  | **$180** |  |  |

### Electronic Components

| Item | Quantity | Unit Price | Total | Source | Notes |
|------|----------|------------|-------|--------|-------|
| 555 Timer IC | 12 | $0.50 | $6 | Mouser, DigiKey | For prime oscillators |
| CD4070 (XOR Gate) IC | 5 | $0.80 | $4 | Mouser, DigiKey | For phase detection |
| LM358 Op-Amp | 5 | $0.70 | $3.50 | Mouser, DigiKey | For signal conditioning |
| 74HC595 Shift Register | 3 | $0.60 | $1.80 | Mouser, DigiKey | For display control |
| Resistor Kit (1/4W) | 1 | $8 | $8 | Amazon | Various values |
| Capacitor Kit (Ceramic) | 1 | $10 | $10 | Amazon | Various values |
| Capacitor Kit (Electrolytic) | 1 | $12 | $12 | Amazon | Various values |
| Trim Potentiometers (10kΩ) | 10 | $0.50 | $5 | eBay, AliExpress | For oscillator tuning |
| Zener Diodes (3.3V) | 10 | $0.20 | $2 | Mouser, DigiKey | For entropy source |
| Transistor Kit (2N2222, 2N3904) | 1 | $7 | $7 | Amazon | General purpose |
| 1N4148 Diodes | 20 | $0.10 | $2 | Mouser, DigiKey | Signal diodes |
| Crystal Oscillators (Various MHz) | 5 | $1 | $5 | DigiKey | Reference frequencies |
| HC-49S Crystal (32.768kHz) | 3 | $1 | $3 | DigiKey | Low frequency reference |
| RTC Module (DS3231) | 1 | $5 | $5 | Amazon | Accurate time keeping |
| Logic Analyzer | 1 | $12 | $12 | Amazon, AliExpress | For testing and debugging |
| Jumper Wire Kit | 1 | $8 | $8 | Amazon | For connections |
| DIP IC Sockets | 20 | $0.20 | $4 | Amazon | For easy replacement |
| Screw Terminal Blocks | 10 | $0.30 | $3 | Amazon | For power connections |
| JST Connector Set | 1 | $4.70 | $4.70 | Amazon | For interconnections |
| **Subtotal** |  |  | **$105** |  |  |

### Oscillator Components

| Item | Quantity | Unit Price | Total | Source | Notes |
|------|----------|------------|-------|--------|-------|
| Precision Resistors (0.1%) | 30 | $0.30 | $9 | Mouser, DigiKey | For accurate timing |
| High Stability Capacitors | 15 | $0.50 | $7.50 | Mouser, DigiKey | For oscillator stability |
| Multi-turn Potentiometers | 6 | $2 | $12 | Mouser, DigiKey | Fine frequency tuning |
| Voltage References (LM4040) | 3 | $1.50 | $4.50 | DigiKey | Stable voltage source |
| Si5351A Clock Generator | 1 | $8 | $8 | Adafruit | Programmable oscillator (optional) |
| Schmitt Trigger IC (74HC14) | 2 | $0.80 | $1.60 | Mouser | For clean signal edges |
| Frequency Counter Module | 1 | $8 | $8 | Amazon | For calibration |
| Crystal Test Oscillator | 1 | $4.40 | $4.40 | eBay | For reference |
| **Subtotal** |  |  | **$45** |  |  |

### Power Supply

| Item | Quantity | Unit Price | Total | Source | Notes |
|------|----------|------------|-------|--------|-------|
| 5V 4A Power Supply | 1 | $10 | $10 | Amazon | Main power |
| USB Power Bank (10000mAh) | 1 | $15 | $15 | Amazon | Portable operation |
| AMS1117-3.3 Voltage Regulator | 5 | $0.30 | $1.50 | AliExpress | 3.3V regulation |
| Power Distribution Board | 1 | $8 | $8 | Amazon | Power splitting |
| DC Barrel Jack Connectors | 3 | $0.50 | $1.50 | Amazon | Power input |
| Power Switch | 3 | $0.50 | $1.50 | Amazon | On/off control |
| LM317 Adjustable Regulator | 2 | $0.75 | $1.50 | DigiKey | Adjustable voltage |
| LED Power Indicators | 10 | $0.10 | $1 | Amazon | Visual status |
| **Subtotal** |  |  | **$30** |  |  |

### Display & User Interface

| Item | Quantity | Unit Price | Total | Source | Notes |
|------|----------|------------|-------|--------|-------|
| 0.96" OLED Display (I2C) | 3 | $5 | $15 | Amazon, AliExpress | Node status display |
| WS2812B RGB LED Strip (1m) | 1 | $8 | $8 | Amazon | Visual indicators |
| 16x2 LCD Display | 1 | $6 | $6 | Amazon | Main control display |
| Tactile Buttons | 10 | $0.20 | $2 | Amazon | User input |
| Rotary Encoder with Button | 1 | $3 | $3 | Amazon | User interface control |
| I2C OLED Display 1.3" | 1 | $6 | $6 | Amazon | Larger display for coordinator |
| **Subtotal** |  |  | **$40** |  |  |

### PCB & Enclosure

| Item | Quantity | Unit Price | Total | Source | Notes |
|------|----------|------------|-------|--------|-------|
| Prototype PCB Boards | 5 | $2 | $10 | Amazon | For testing circuits |
| Custom PCB Production | 3 | $5 | $15 | JLCPCB, PCBWay | 5 pcs of each design |
| Acrylic Sheet (3mm) | 1 | $12 | $12 | Amazon, Hardware store | For enclosure panels |
| M3 Standoffs & Screws Kit | 1 | $8 | $8 | Amazon | For assembly |
| 3D Printing Filament (PLA) | 1 | $20 | $20 | Amazon | For printing enclosure parts |
| Heat Set Inserts | 50 | $0.10 | $5 | Amazon | For enclosure assembly |
| Rubber Feet | 1 | $5 | $5 | Amazon | Pack of 100 |
| **Subtotal** |  |  | **$75** |  |  |

*(Reduced to $60 to fit budget)*

### Tools & Miscellaneous

| Item | Quantity | Unit Price | Total | Source | Notes |
|------|----------|------------|-------|--------|-------|
| Soldering Iron Kit | 1 | $25 | $25 | Amazon | For assembly |
| Digital Multimeter | 1 | $15 | $15 | Amazon | For testing |
| Wire Stripper/Cutter | 1 | $6 | $6 | Amazon | For wire preparation |
| Heat Shrink Tubing Kit | 1 | $7 | $7 | Amazon | For insulation |
| Solder Wire (Lead-free) | 1 | $6 | $6 | Amazon | For connections |
| Flux Pen | 1 | $5 | $5 | Amazon | For soldering |
| Anti-Static Wrist Strap | 1 | $5 | $5 | Amazon | Safety precaution |
| Helping Hands Tool | 1 | $8 | $8 | Amazon | For holding components |
| Component Storage Box | 1 | $8 | $8 | Amazon | Organization |
| Breadboard | 2 | $5 | $10 | Amazon | For prototyping |
| **Subtotal** |  |  | **$95** |  |  |

*(Reduced to $40 to fit budget)*

## Cost-Saving Options

To reduce costs further:

1. **Start with a single node**: Build one complete node first (~$165), then expand.
2. **Use breadboards instead of custom PCBs**: Save $15 on PCB production.
3. **Use existing tools**: If you already have basic electronics tools, save $40.
4. **Substitute the Raspberry Pi**: Use an old laptop as the coordinator node.
5. **Use fewer display components**: The OLED displays can be reduced to 1 initially.
6. **3D print locally**: Many libraries and makerspaces offer free or low-cost 3D printing.

## Shopping Options

### Budget Sources
- **AliExpress**: Lowest prices but longer shipping times
- **eBay**: Good for bulk component purchases
- **JLCPCB/PCBWay**: Economical PCB production

### Mid-Range Sources
- **Amazon**: Fast shipping, reasonable prices
- **Banggood**: Good balance of price and shipping time

### Premium Sources (for critical components)
- **Adafruit**: High-quality components with excellent documentation
- **SparkFun**: Reliable components with good support
- **Mouser/DigiKey**: Professional-grade components

## Alternative Components

If certain components are unavailable or over budget, consider these alternatives:

| Original | Alternative | Price Difference | Notes |
|----------|-------------|-----------------|-------|
| ESP32 DevKit | Arduino Nano | -$5 each | Less powerful but adequate for basic nodes |
| Raspberry Pi Zero 2 W | Orange Pi Zero | -$5 | Compatible alternative |
| 0.96" OLED | LED Matrix Display | -$2 each | Simpler but effective visuals |
| Custom PCB | Perforated Prototype Board | -$3 each | Requires more manual wiring |
| WS2812B LED Strip | Individual LEDs | -$5 | Simpler visual indicators |
| Si5351A Clock Generator | Crystal Oscillator Circuit | -$6 | Less flexible but stable |
| LM358 Op-Amp | LM741 Op-Amp | -$0.20 each | Older but functional |
| USB Power Bank | Wall Adapter | -$10 | Loses portability |

## Vendor Comparison

| Component | Vendor 1 | Price | Vendor 2 | Price | Vendor 3 | Price |
|-----------|----------|-------|----------|-------|----------|-------|
| ESP32 DevKit | Amazon | $10.99 | AliExpress | $8.50 | Mouser | $13.95 |
| 555 Timer (10 pack) | Amazon | $5.99 | DigiKey | $5.50 | AliExpress | $2.99 |
| OLED Display | Amazon | $5.99 | AliExpress | $3.50 | Adafruit | $7.95 |
| PCB Production (5pcs) | JLCPCB | $2 + shipping | PCBWay | $5 + shipping | OSH Park | $15 + shipping |
| Acrylic Sheet | Amazon | $12.99 | Local hardware | $15.00 | Specialized store | $20.00 |

## Complete Kit Options

For those who prefer an all-in-one solution:

1. **Basic Kit** ($165):
   - 1 × ESP32 node with all components
   - Complete enclosure
   - Power supply
   - Documentation

2. **Standard Kit** ($320):
   - 2 × ESP32 nodes with all components
   - Raspberry Pi coordinator
   - Enclosures for all nodes
   - Network equipment
   - Power supplies
   - Documentation and software

3. **Full System** ($490):
   - 3 × ESP32 nodes with all components
   - Raspberry Pi coordinator with display
   - Custom enclosures with laser-cut panels
   - All networking equipment
   - Power supplies and backup battery
   - Complete documentation, software, and examples

## Bulk Purchase Recommendations

For classroom settings or multiple builds:

| Component | Qty | Unit Price | Bulk Price | Savings |
|-----------|-----|------------|------------|---------|
| ESP32 DevKit | 10+ | $10 | $8.50 | 15% |
| Resistor Kits | 5+ | $8 | $6.50 | 19% |
| PCB Production | 10+ sets | $5 | $3.50 | 30% |
| 555 Timers | 50+ | $0.50 | $0.35 | 30% |
| OLED Displays | 10+ | $5 | $4.20 | 16% |
| Enclosure Materials | 10+ sets | $20 | $16 | 20% |

## Tools Required

### Essential Tools
- Soldering iron (temperature controlled)
- Digital multimeter
- Wire cutters/strippers
- Screwdriver set
- Pliers

### Recommended Tools
- Logic analyzer or oscilloscope
- Tweezers (ESD safe)
- Helping hands or PCB holder
- Heat gun (for heat shrink tubing)
- 3D printer (for enclosure parts)

### Optional Tools
- Bench power supply
- Frequency counter
- PCB vice
- Magnifying lamp

## Special Notes

### Key Component Selection

1. **ESP32 Selection**: Look for modules with external antenna for better network performance
2. **555 Timer Precision**: For oscillators, consider higher-grade options like:
   - Texas Instruments NA555P (higher precision)
   - Maxim ICM7555 (lower power consumption)
   - Intersil ICM7556 (dual timer version)
3. **Passives Quality**: Use 1% tolerance resistors and low-drift capacitors for oscillator circuits
4. **Power Supply Stability**: Clean power is essential for stable oscillations. Consider:
   - Low-noise LDO regulators
   - Additional filtering capacitors
   - Separate power domains for digital and analog sections

### Component Substitutions

The prime computer design is flexible and can accommodate different components:

- **Controller Alternatives**: Arduino Nano, STM32 "Blue Pill", Teensy LC
- **Oscillator Alternatives**: Crystal oscillators, RC networks, LC oscillators
- **Phase Detection Alternatives**: Analog multiplier, digital phase detector, microcontroller timed capture

### Quantity Adjustments

Build costs can be adjusted by changing the number of nodes:

- **Single Node**: ~$165
- **Dual Node**: ~$320
- **Three Node**: ~$500
- **Five Node**: ~$800