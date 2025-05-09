# Prime Computer Assembly Guide

## Overview

This guide walks through the complete assembly process for building a Prime Resonance Computer. The system consists of three main nodes connected in a network configuration, with each node containing prime-frequency oscillators, phase detection circuitry, and processing capabilities. By following this guide, you will be able to construct a fully functional prime computer system capable of demonstrating entropic resonance principles.

## Safety Precautions

Before beginning assembly, please observe these safety measures:

- Always work in a well-ventilated area, especially when soldering
- Use eye protection when cutting, drilling, or soldering
- Disconnect power before modifying any circuits
- Use an ESD (Electrostatic Discharge) wrist strap when handling sensitive components
- Keep a fire extinguisher nearby when soldering
- Allow hot components to cool before touching them

## Required Tools

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

## Assembly Workflow

The complete assembly process is divided into stages:

1. Component preparation and testing
2. Oscillator circuit assembly
3. Phase detector assembly
4. ESP32 controller integration
5. Power supply setup
6. Enclosure assembly
7. Network configuration
8. Testing and calibration

## Stage 1: Component Preparation and Testing

### Step 1.1: Component Inventory

Verify you have all components listed in the [Parts List](parts_list.md). Sort components by category:

- ICs (integrated circuits)
- Passive components (resistors, capacitors)
- Connectors and headers
- Display components
- Power components
- Mechanical parts

### Step 1.2: Test Critical Components

Before assembly, test these key components:

**ESP32 Testing:**
1. Connect ESP32 to computer via USB
2. Verify power LED illuminates
3. Upload a simple test program (e.g., LED blink)
4. Confirm the test program runs correctly

**555 Timer Testing:**
1. Set up a basic astable circuit on breadboard:
   - Pin 1 to GND
   - Pin 8 to VCC (5V)
   - Pin 2 to Pin 6
   - 10kΩ resistor from Pin 6 to Pin 7
   - 10kΩ resistor from Pin 7 to VCC
   - 0.1μF capacitor from Pin 2 to GND
   - LED with 470Ω resistor from Pin 3 to GND
2. Apply power and verify LED blinks

**Display Testing:**
1. Connect OLED display to breadboard:
   - VCC to 3.3V
   - GND to GND
   - SCL to a GPIO pin (e.g., D22)
   - SDA to a GPIO pin (e.g., D21)
2. Upload OLED test program to ESP32
3. Verify display shows test pattern

## Stage 2: Oscillator Circuit Assembly

### Step 2.1: Prime Oscillator Circuit

Each node requires three oscillators tuned to prime number frequencies. Build the circuit below on a breadboard first, then transfer to PCB.

![Prime Oscillator Circuit](../04-diagrams/oscillator_circuit.png)

**Parts for each oscillator:**
- 1 × 555 timer IC
- 1 × 10kΩ resistor (R1)
- 1 × 4.7kΩ resistor (R2)
- 1 × 10kΩ trim potentiometer (R3)
- 1 × 0.01μF ceramic capacitor (C1)
- 1 × 0.1μF ceramic capacitor (C2)
- 1 × 10μF electrolytic capacitor (C3)

**Assembly steps:**
1. Insert 555 timer IC into breadboard or 8-pin DIP socket on PCB
2. Connect pin 1 (GND) to ground
3. Connect pin 8 (VCC) to 5V
4. Connect pins 2 and 6 together
5. Connect R1 from pin 7 to pin 8
6. Connect R2 from pin 7 to pin 6
7. Connect R3 (potentiometer) in parallel with R2
8. Connect C1 from pin 2 to ground
9. Connect C2 from pin 5 to ground
10. Connect C3 from pin 8 to ground
11. Output is taken from pin 3

### Step 2.2: Configure Oscillator Frequencies

Each oscillator must be tuned to a specific prime frequency:

| Oscillator | Frequency (Hz) | Prime Number |
|------------|----------------|-------------|
| OSC1 | 2 | 2 |
| OSC2 | 3 | 3 |
| OSC3 | 5 | 5 |

Calculate the required R and C values using the 555 timer formula:
f = 1.44 / ((R1 + 2R2) × C1)

For fine-tuning:
1. Connect oscilloscope or frequency counter to output (pin 3)
2. Adjust potentiometer R3 until desired frequency is achieved
3. Measure and record the exact resistance value for each setting

### Step 2.3: Entropy Source Circuit

The entropy source provides randomness for state transitions:

![Entropy Source Circuit](../04-diagrams/entropy_source.png)

**Parts:**
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
6. Connect output to ESP32 ADC input

## Stage 3: Phase Detector Assembly

### Step 3.1: Phase Detector Circuit

The phase detector measures relationships between oscillators:

![Phase Detector Circuit](../04-diagrams/phase_detector.png)

**Parts for each phase detector (3 required per node):**
- 1 × CD4070 XOR gate IC
- 2 × 10kΩ resistors
- 1 × 0.1μF capacitor
- 1 × 1kΩ resistor
- 1 × 10kΩ trim potentiometer

**Assembly steps:**
1. Insert CD4070 IC into breadboard or DIP socket
2. Connect VCC and GND pins
3. Connect oscillator 1 output to input A of XOR gate
4. Connect oscillator 2 output to input B of XOR gate
5. From XOR output, connect 10kΩ resistor to ground
6. Connect 0.1μF capacitor in parallel with 10kΩ resistor
7. Connect this junction to ESP32 ADC input

### Step 3.2: Phase Detection Matrix

Create a matrix of phase detectors for all oscillator pairs:

| Phase Detector | Oscillator Pair | ESP32 ADC Input |
|----------------|-----------------|----------------|
| PD1 | OSC1 - OSC2 | GPIO34 (ADC6) |
| PD2 | OSC1 - OSC3 | GPIO35 (ADC7) |
| PD3 | OSC2 - OSC3 | GPIO32 (ADC4) |

## Stage 4: ESP32 Controller Integration

### Step 4.1: ESP32 Base Circuit

Connect core components to the ESP32:

![ESP32 Base Circuit](../04-diagrams/esp32_base.png)

**Assembly steps:**
1. Mount ESP32 DevKit on PCB using headers or directly solder
2. Connect 3.3V and GND from external supply
3. Add 10μF and 0.1μF decoupling capacitors between 3.3V and GND
4. Connect EN pin to 3.3V via 10kΩ resistor
5. Add reset button between EN pin and GND with 0.1μF capacitor

### Step 4.2: Connect Oscillators to ESP32

Connect each oscillator output to ESP32 GPIO pins:

| Oscillator | ESP32 GPIO Pin |
|------------|----------------|
| OSC1 | GPIO25 |
| OSC2 | GPIO26 |
| OSC3 | GPIO27 |

For each connection:
1. Add a 1kΩ series resistor for protection
2. Add a pull-down 10kΩ resistor to ground

### Step 4.3: Connect Phase Detectors

Connect phase detector outputs to ESP32 ADC pins as specified in Step 3.2.

### Step 4.4: Connect Display

For the OLED display:
1. Connect VCC to 3.3V
2. Connect GND to ground
3. Connect SCL to GPIO22
4. Connect SDA to GPIO21

### Step 4.5: Connect Network Module

For ESP-NOW wireless communication:
1. ESP32 has built-in WiFi - no additional connections needed
2. For wired communication (optional), connect:
   - ESP32 TX0 (GPIO1) to RX of next node
   - ESP32 RX0 (GPIO3) to TX of next node
   - Common ground between nodes

## Stage 5: Power Supply Setup

### Step 5.1: Power Supply Circuit

![Power Supply Circuit](../04-diagrams/power_supply.png)

**Parts:**
- 1 × 5V 2A power adapter
- 1 × AMS1117-3.3 voltage regulator
- 2 × 10μF electrolytic capacitors
- 2 × 0.1μF ceramic capacitors
- 1 × 10Ω resistor
- 1 × Power switch
- 1 × DC barrel jack

**Assembly steps:**
1. Connect DC barrel jack to PCB
2. Wire power switch in series with positive input
3. Connect 10Ω resistor in series after switch
4. Add 10μF electrolytic capacitor from positive to ground
5. Connect input to AMS1117-3.3 input pin
6. Connect AMS1117-3.3 output pin to 3.3V power rail
7. Connect AMS1117-3.3 ground pin to ground
8. Add 0.1μF ceramic capacitor from input to ground
9. Add both 10μF electrolytic and 0.1μF ceramic from output to ground

### Step 5.2: Power Distribution

Create separate power rails for digital and analog sections:
1. Digital section (ESP32, display): Connect directly to 3.3V output
2. Analog section (oscillators, phase detectors):
   - Add ferrite bead in series with 3.3V supply
   - Add 100μF capacitor from analog supply to ground

## Stage 6: Enclosure Assembly

### Step 6.1: Prepare Enclosure Panels

![Enclosure Design](../04-diagrams/enclosure.png)

**Materials:**
- 3mm acrylic sheets (top, bottom, sides)
- M3 screws and standoffs
- Rubber feet

**Steps:**
1. Cut acrylic according to the dimensions in provided templates
2. Drill mounting holes for PCB, power jack, and switches
3. If using laser cutter, cut directly from provided files

### Step 6.2: PCB Mounting

1. Install M3 standoffs on bottom panel using screws
2. Mount PCB onto standoffs
3. Secure with additional screws

### Step 6.3: Front Panel Assembly

1. Mount OLED display using M2 screws or hot glue
2. Install power switch
3. Add LED indicators if included in design
4. Label controls with permanent marker or printed stickers

### Step 6.4: Assemble Enclosure

1. Attach side panels to bottom panel using acrylic adhesive or slot-together design
2. Route cables through appropriate holes
3. Attach top panel using screws or slot design
4. Add rubber feet to bottom of enclosure

## Stage 7: Network Configuration

### Step 7.1: Node Identification

Configure each node with a unique ID:

1. Node 1 (Coordinator): Set `NODE_ID` to 1 in firmware
2. Node 2: Set `NODE_ID` to 2 in firmware
3. Node 3: Set `NODE_ID` to 3 in firmware

### Step 7.2: Physical Network Layout

Arrange nodes in the recommended formation:

```
    Node 2
     /   \
Node 1 --- Node 3
```

Ensure maximum distance between nodes is less than:
- 10 meters for indoor WiFi
- 30 cm for direct wired connections

### Step 7.3: Network Topology Setup

In the firmware network configuration:

1. For Coordinator node (Node 1):
   - Set `isCoordinator` to `true`
   - Add MAC addresses of all other nodes as peers

2. For all other nodes:
   - Set `isCoordinator` to `false`
   - Add coordinator's MAC address as primary peer

## Stage 8: Testing and Calibration

### Step 8.1: Initial Power-Up

1. Perform visual inspection for shorts or incorrect connections
2. Connect power supply to DC jack
3. Verify power LED illuminates
4. Measure voltage at test points:
   - 5V rail should read 4.8-5.2V
   - 3.3V rail should read 3.2-3.4V
5. Check current consumption (should be less than 200mA per node)

### Step 8.2: Oscillator Calibration

For each oscillator:
1. Connect oscilloscope or frequency counter to oscillator output
2. Adjust trimmer potentiometer until frequency matches target prime number
3. Verify stability by monitoring for several minutes
4. Record final potentiometer position

### Step 8.3: Phase Detector Calibration

For each phase detector:
1. Force both input oscillators to same frequency temporarily
2. Adjust phase offset potentiometer until ADC reads mid-range (approximately 2048)
3. Return oscillators to original frequencies
4. Verify phase detector output varies over time

### Step 8.4: Network Testing

1. Power up all nodes
2. On coordinator's display, verify all nodes appear in network list
3. Check round-trip communication time (should be less than 100ms)
4. Verify data exchange by monitoring status LEDs

## Detailed Node Assembly

### Node PCB Layout

![Node PCB Layout](../04-diagrams/node_pcb_layout.png)

The PCB layout above shows component placement for a single node. Critical areas include:

1. **Power Section** (top-left):
   - Voltage regulator and power filtering components
   - Decoupling capacitors for ICs
   - Power input protection

2. **Oscillator Section** (top-right):
   - Three 555 timer oscillator circuits
   - Frequency adjustment trimmers
   - Output buffers

3. **Phase Detection Section** (middle):
   - XOR gates for phase comparison
   - Low-pass filters for average phase difference
   - Buffer amplifiers

4. **ESP32 Section** (bottom):
   - ESP32 module and supporting components
   - Programming header
   - Reset circuit

5. **Display Section** (bottom-right):
   - OLED connector
   - I2C pull-up resistors

### Node Wiring Diagram

![Node Wiring Diagram](../04-diagrams/node_wiring.png)

This diagram shows all connections between components. Follow color coding:
- Red: Power connections
- Black: Ground connections
- Blue: Digital signals
- Green: Analog signals
- Yellow: Communication bus lines

## Troubleshooting Guide

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

### Communication Issues

| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| Nodes not connecting | Incorrect WiFi settings | Verify SSID and password match |
| | Too much distance | Move nodes closer together |
| | Wrong MAC addresses | Double-check MAC addresses in config |
| Data corruption | Interference | Change WiFi channel |
| | Buffer overflow | Reduce data transmission rate |

## Final Assembly Checklist

Before considering assembly complete, verify:

- [ ] All screws and mechanical fasteners are tight
- [ ] No loose wires or components
- [ ] All ICs are firmly seated in sockets
- [ ] No solder bridges or cold solder joints
- [ ] All oscillators are calibrated to correct frequencies
- [ ] Phase detectors show changing values when monitored
- [ ] Network communication is established between all nodes
- [ ] Displays show correct information
- [ ] Power consumption is within expected range
- [ ] No components are running unusually hot

## Performance Verification

To verify your prime computer is working correctly:

1. Run the included self-test program
2. Verify all three nodes appear in the network display
3. Watch for synchronized blinking patterns across nodes
4. Check that entropy injection causes visible pattern changes
5. Run a simple prime number computation test
6. Verify results match expected outputs

## Node Expansion (Optional)

To add additional nodes to your network:

1. Build additional node hardware following this guide
2. Assign unique NODE_ID values (4, 5, etc.)
3. Update coordinator firmware to recognize additional nodes
4. Position new nodes within communication range
5. Power up system in sequence: coordinator first, then other nodes

## Next Steps

After completing hardware assembly:

1. Proceed to [Firmware Installation Guide](../03-software/firmware_installation.md)
2. Configure network settings as described in [Network Setup](../03-software/network_setup.md)
3. Explore computational examples in [Example Applications](../05-applications/example_applications.md)