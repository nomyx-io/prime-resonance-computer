# Hardware Build Guide

## Introduction

This section provides detailed, step-by-step instructions for assembling the hardware components of your Prime Resonance Computer. We'll build the system one module at a time, starting with component preparation and ending with a complete networked system.

## Safety Precautions

Before beginning assembly, please observe these safety measures:

- Always work in a well-ventilated area, especially when soldering
- Use eye protection when cutting, drilling, or soldering
- Disconnect power before modifying any circuits
- Use an ESD (Electrostatic Discharge) wrist strap when handling sensitive components
- Keep a fire extinguisher nearby when soldering
- Allow hot components to cool before touching them

## Materials and Tools Preparation

### Required Tools

Ensure you have the following tools before starting:

- Soldering iron (temperature-controlled, 350°C/650°F)
- Solder (lead-free, 0.8mm diameter)
- Soldering flux
- Digital multimeter
- Wire cutters and strippers
- Precision screwdriver set
- Needle-nose pliers
- Tweezers (ESD safe)
- Helping hands or PCB holder
- Heat gun or lighter (for heat shrink tubing)
- Magnifying glass or loupe
- Breadboard for initial testing
- Computer with USB port for programming

### Workspace Setup

1. Choose a well-lit, clean workspace with adequate ventilation
2. Organize components into labeled containers or compartments
3. Set up a dedicated area for soldering with a heat-resistant mat
4. Keep a damp sponge nearby for cleaning your soldering iron
5. Have a printed copy of schematics and assembly guides for reference

## Parts Acquisition

### Complete Parts List

The following parts are required for a single node. Multiply by three for a complete three-node system:

#### Core Electronics:
- 1 × ESP32 Development Board
- 3 × NE555 Timer ICs
- 1 × CD4070 Quad XOR Gate IC
- 1 × LM358 Operational Amplifier
- 1 × AMS1117-3.3 Voltage Regulator
- 1 × 3.3V Zener Diode
- 1 × 0.96" OLED Display (I2C, 128×64 resolution)

#### Resistors:
- 5 × 10kΩ resistors
- 5 × 1kΩ resistors
- 3 × 4.7kΩ resistors
- 3 × 10kΩ trim potentiometers
- 1 × 10Ω resistor

#### Capacitors:
- 5 × 0.1μF ceramic capacitors
- 3 × 0.01μF ceramic capacitors
- 4 × 10μF electrolytic capacitors
- 1 × 100μF electrolytic capacitor

#### Connectors and Power:
- 1 × DC barrel jack
- 1 × Power switch
- 1 × 5V 2A power adapter
- Header pins (male and female)
- Breadboard jumper wires
- Solid core wire for PCB connections

#### Mechanical Parts:
- 1 × PCB (either custom-ordered or prototype board)
- 1 × Enclosure (3mm acrylic sheets or 3D printed)
- 4 × M3 standoffs and screws
- 4 × Rubber feet
- Mounting hardware for display

### Sourcing Components

Components can be sourced from:
- Online retailers (DigiKey, Mouser, LCSC, Arrow)
- Local electronics stores
- Amazon, eBay, or AliExpress for budget options
- Electronic component surplus stores

**Pro Tip**: Order extra passive components (resistors, capacitors) as they're inexpensive and easy to lose.

## Component Testing

Before assembly, test critical components to ensure they're working properly.

### ESP32 Testing

1. Connect ESP32 to computer via USB
2. Verify power LED illuminates
3. Upload a simple test program (Blink LED sketch):

```cpp
void setup() {
  pinMode(2, OUTPUT);  // Built-in LED on most ESP32 modules
}

void loop() {
  digitalWrite(2, HIGH);
  delay(500);
  digitalWrite(2, LOW);
  delay(500);
}
```

4. Confirm the onboard LED blinks indicating proper functioning

### 555 Timer Testing

Test each 555 timer using a basic astable circuit on a breadboard:

1. Assemble the following circuit:
   - Pin 1 to GND
   - Pin 8 to VCC (5V)
   - Pin 2 to Pin 6
   - 10kΩ resistor from Pin 6 to Pin 7
   - 10kΩ resistor from Pin 7 to VCC
   - 0.1μF capacitor from Pin 2 to GND
   - LED with 470Ω resistor from Pin 3 to GND
2. Apply power and verify LED blinks at approximately 1Hz
3. If you have an oscilloscope, check the output waveform at pin 3

### Display Testing

1. Connect OLED display to a breadboard:
   - VCC to 3.3V
   - GND to GND
   - SCL to GPIO22 on ESP32
   - SDA to GPIO21 on ESP32
2. Upload an OLED test program to ESP32 (using Adafruit SSD1306 library)
3. Verify display shows test pattern

## Circuit Assembly

For each node, we'll build the following modules:
1. **Power Supply Circuit**
2. **Oscillator Circuits** (three per node)
3. **Phase Detector Circuit**
4. **Entropy Source Circuit**
5. **ESP32 Integration Circuit**
6. **Display Connection**

### PCB Options

You have three options for circuit assembly:

1. **Custom PCB**: Order the custom PCB design included in the appendix
2. **Prototype Board**: Assemble on stripboard/perfboard following layout diagrams
3. **Breadboard Prototype**: For initial testing, assemble on breadboards

We'll provide instructions assuming prototype board assembly, but the component placements apply to all methods.

### Module 1: Power Supply Circuit

The power supply provides 5V and 3.3V for digital and analog components.

![Power Supply Circuit](diagrams/power_supply.png)

**Components:**
- 1 × 5V 2A power adapter
- 1 × AMS1117-3.3 voltage regulator
- 2 × 10μF electrolytic capacitors
- 2 × 0.1μF ceramic capacitors
- 1 × 10Ω resistor
- 1 × Power switch
- 1 × DC barrel jack

**Assembly steps:**
1. Mount DC barrel jack on PCB
2. Wire power switch in series with positive input
3. Connect 10Ω resistor in series after switch
4. Add 10μF electrolytic capacitor from positive to ground
5. Connect input to AMS1117-3.3 input pin
6. Connect AMS1117-3.3 output pin to 3.3V power rail
7. Connect AMS1117-3.3 ground pin to ground
8. Add 0.1μF ceramic capacitor from input to ground
9. Add 10μF electrolytic and 0.1μF ceramic from output to ground

**Power Distribution:**
Create separate power rails for digital and analog sections:
1. Digital section (ESP32, display): Connect directly to 3.3V output
2. Analog section (oscillators, phase detectors):
   - Add ferrite bead in series with 3.3V supply
   - Add 100μF capacitor from analog supply to ground

### Module 2: Oscillator Circuits

Each node requires three oscillators tuned to prime number frequencies (2Hz, 3Hz, 5Hz).

![Prime Oscillator Circuit](diagrams/oscillator_circuit.png)

**Components for each oscillator (×3):**
- 1 × 555 timer IC
- 1 × 10kΩ resistor (R1)
- 1 × 4.7kΩ resistor (R2)
- 1 × 10kΩ trim potentiometer (R3)
- 1 × 0.01μF ceramic capacitor (C1)
- 1 × 0.1μF ceramic capacitor (C2)
- 1 × 10μF electrolytic capacitor (C3)

**Assembly steps (repeat for each oscillator):**
1. Insert 555 timer IC into PCB or 8-pin DIP socket
2. Connect pin 1 (GND) to ground
3. Connect pin 8 (VCC) to 5V
4. Connect pins 2 and 6 together
5. Connect R1 from pin 7 to pin 8
6. Connect R2 from pin 7 to pin 6
7. Connect R3 (potentiometer) in parallel with R2
8. Connect C1 from pin 2 to ground
9. Connect C2 from pin 5 to ground
10. Connect C3 from pin 8 to ground
11. Connect pin 3 (output) to a test point or header pin

**Oscillator Frequencies:**

| Oscillator | Frequency (Hz) | Prime Number |
|------------|----------------|-------------|
| OSC1 | 2 | 2 |
| OSC2 | 3 | 3 |
| OSC3 | 5 | 5 |

Calculate resistor/capacitor values using the 555 timer formula:
f = 1.44 / ((R1 + 2R2) × C1)

We'll perform final tuning during the calibration stage.

### Module 3: Phase Detector Circuit

The phase detector measures relationships between oscillator pairs.

![Phase Detector Circuit](diagrams/phase_detector.png)

**Components:**
- 1 × CD4070 XOR gate IC
- 6 × 10kΩ resistors
- 3 × 0.1μF capacitors
- 3 × 1kΩ resistors

**Assembly steps:**
1. Insert CD4070 IC into PCB or DIP socket
2. Connect VCC and GND pins
3. Create three phase detector circuits, one for each oscillator pair:

   **Phase Detector 1 (OSC1-OSC2):**
   - Connect OSC1 output to input A of first XOR gate
   - Connect OSC2 output to input B of first XOR gate
   - From XOR output, connect 10kΩ resistor to ground
   - Connect 0.1μF capacitor in parallel with 10kΩ resistor
   - Connect this junction through 1kΩ resistor to ESP32 ADC pin (GPIO34)

   **Phase Detector 2 (OSC1-OSC3):**
   - Connect OSC1 output to input A of second XOR gate
   - Connect OSC3 output to input B of second XOR gate
   - From XOR output, connect 10kΩ resistor to ground
   - Connect 0.1μF capacitor in parallel with 10kΩ resistor
   - Connect this junction through 1kΩ resistor to ESP32 ADC pin (GPIO35)

   **Phase Detector 3 (OSC2-OSC3):**
   - Connect OSC2 output to input A of third XOR gate
   - Connect OSC3 output to input B of third XOR gate
   - From XOR output, connect 10kΩ resistor to ground
   - Connect 0.1μF capacitor in parallel with 10kΩ resistor
   - Connect this junction through 1kΩ resistor to ESP32 ADC pin (GPIO32)

### Module 4: Entropy Source Circuit

The entropy source provides randomness for state transitions.

![Entropy Source Circuit](diagrams/entropy_source.png)

**Components:**
- 1 × Zener diode (3.3V)
- 1 × 10kΩ resistor
- 1 × 1kΩ resistor
- 1 × 0.1μF capacitor
- 1 × LM358 op-amp

**Assembly steps:**
1. Connect 10kΩ resistor from 5V to Zener diode anode
2. Connect Zener diode cathode to ground
3. Connect the junction of resistor and Zener to op-amp non-inverting input
4. Configure op-amp as buffer (output connected to inverting input)
5. Connect 0.1μF capacitor from op-amp output to ground
6. Connect output through 1kΩ resistor to ESP32 ADC input (GPIO33)

### Module 5: ESP32 Integration Circuit

Connect the ESP32 to all the other modules.

![ESP32 Base Circuit](diagrams/esp32_base.png)

**Assembly steps:**
1. Mount ESP32 DevKit on PCB using headers or directly solder
2. Connect 3.3V and GND from power supply
3. Add 10μF and 0.1μF decoupling capacitors between 3.3V and GND
4. Connect reset button between EN pin and GND with 0.1μF capacitor

**Connect Oscillators to ESP32:**
Connect each oscillator output to ESP32 GPIO pins:

| Oscillator | ESP32 GPIO Pin |
|------------|----------------|
| OSC1 | GPIO25 |
| OSC2 | GPIO26 |
| OSC3 | GPIO27 |

For each connection:
1. Add a 1kΩ series resistor for protection
2. Add a pull-down 10kΩ resistor to ground

**Connect Phase Detectors:**
Phase detector outputs connect to ESP32 ADC pins as specified earlier:

| Phase Detector | Oscillator Pair | ESP32 ADC Pin |
|----------------|-----------------|----------------|
| PD1 | OSC1 - OSC2 | GPIO34 (ADC6) |
| PD2 | OSC1 - OSC3 | GPIO35 (ADC7) |
| PD3 | OSC2 - OSC3 | GPIO32 (ADC4) |

### Module 6: Display Connection

Connect the OLED display to the ESP32.

**Components:**
- 1 × 0.96" OLED Display (I2C)
- 2 × 4.7kΩ pull-up resistors

**Assembly steps:**
1. Connect OLED VCC to 3.3V
2. Connect OLED GND to ground
3. Connect OLED SCL to GPIO22 with 4.7kΩ pull-up resistor to 3.3V
4. Connect OLED SDA to GPIO21 with 4.7kΩ pull-up resistor to 3.3V
5. Secure display to PCB or enclosure front panel

## Enclosure Assembly

### Prepare Enclosure Panels

![Enclosure Design](diagrams/enclosure.png)

**Materials:**
- 3mm acrylic sheets or 3D printed parts
- M3 screws and standoffs
- Rubber feet

**Steps:**
1. Cut acrylic according to the dimensions in provided templates or 3D print the enclosure parts
2. Drill mounting holes for PCB, power jack, and display
3. If using laser cutter, cut directly from provided files

### PCB Mounting

1. Install M3 standoffs on bottom panel using screws
2. Mount PCB onto standoffs
3. Secure with additional screws

### Front Panel Assembly

1. Mount OLED display using M2 screws or hot glue
2. Install power switch
3. Add LED indicators if included in design
4. Label controls with permanent marker or printed stickers

### Final Enclosure Assembly

1. Attach side panels to bottom panel using acrylic adhesive or slot-together design
2. Route cables through appropriate holes
3. Attach top panel using screws or slot design
4. Add rubber feet to bottom of enclosure

## Network Configuration

### Node Identification

Configure each node with a unique ID:

1. Node 1 (Coordinator): Set `NODE_ID` to 1 in firmware
2. Node 2: Set `NODE_ID` to 2 in firmware
3. Node 3: Set `NODE_ID` to 3 in firmware

### Physical Network Layout

Arrange nodes in the recommended formation:

```
    Node 2
     /   \
Node 1 --- Node 3
```

Ensure maximum distance between nodes is less than:
- 10 meters for indoor WiFi
- 30 cm for direct wired connections

## Initial Testing

### Power-Up Sequence

1. Perform visual inspection for shorts or incorrect connections
2. Connect power supply to DC jack
3. Verify power LED illuminates
4. Measure voltage at test points:
   - 5V rail should read 4.8-5.2V
   - 3.3V rail should read 3.2-3.4V
5. Check current consumption (should be less than 200mA per node)

### Signal Verification

If you have access to an oscilloscope:
1. Check oscillator outputs at test points (pins 3)
2. Verify frequencies are approximately 2Hz, 3Hz, and 5Hz
3. Check phase detector outputs for varying signals

## Troubleshooting Common Hardware Issues

### Power Issues

| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| No power LED | Incorrect polarity | Check power connector orientation |
| | Blown fuse/regulator | Check voltage regulator temperature, replace if hot |
| | Bad connection | Check solder joints at power input |
| Low voltage | Overloaded power supply | Measure current draw, ensure below power supply rating |
| | Poor regulation | Check voltage regulator output with multimeter |

### Oscillator Issues

| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| No oscillation | Missing connection | Check all connections to 555 timer |
| | Incorrect resistor values | Verify resistors match schematic |
| | Bad IC | Replace 555 timer |
| Unstable frequency | Power supply noise | Add more filtering capacitors |
| | Temperature effects | Allow system to reach thermal equilibrium |

## Next Steps

After completing the hardware assembly for all three nodes, you're ready to move on to the software installation and configuration section. Before proceeding, ensure:

1. All nodes power up correctly
2. Basic visual checks pass (LEDs, display)
3. Initial voltage measurements are correct
4. No components are running unusually hot

In the next section, we'll install and configure the firmware that will bring your Prime Resonance Computer to life.