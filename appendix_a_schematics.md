# Appendix A: Schematics and Technical Diagrams

This appendix provides detailed schematics, circuit diagrams, PCB layouts, and technical drawings for the Prime Resonance Computer. These resources will help you build the hardware components accurately and understand their interconnections.

## A.1 System Block Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                    Prime Resonance Computer                     │
│                                                                 │
│  ┌───────────────┐    ┌─────────────────┐    ┌───────────────┐  │
│  │  Oscillator   │    │  Phase Detection │    │    ESP32      │  │
│  │   Circuit     │◄──►│     Network     │◄──►│ Microcontroller│  │
│  │  (3 Prime     │    │  (XOR Gates &   │    │               │  │
│  │  Oscillators) │    │  Filter Circuits)│    │               │  │
│  └───────┬───────┘    └────────┬────────┘    └───────┬───────┘  │
│          │                     │                     │          │
│          │                     │                     │          │
│          │                     │                     │          │
│          ▼                     ▼                     ▼          │
│  ┌───────────────┐    ┌─────────────────┐    ┌───────────────┐  │
│  │  Power Supply │    │ User Interface  │    │  Expansion    │  │
│  │  Regulation   │    │ (OLED Display   │    │  Connector    │  │
│  │               │    │  & Buttons)     │    │               │  │
│  └───────────────┘    └─────────────────┘    └───────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## A.2 Oscillator Circuit Schematics

### A.2.1 Complete Oscillator Array Schematic

```
                            VCC (5V)
                              │
                              ├──── 0.1µF
                              │      │
                              │      │
                              │      ▼
                              ▼      GND
┌─────────────────────────────────────────────────────────────┐
│                     Oscillator 1 (2Hz)                      │
│                                                             │
│                     ┌────┬──┬─────┐                         │
│                     │    │  │     │                         │
│      4.7kΩ          │  8 │7 │ 6   │                        │
│        ┌────────────┤Vcc D  T     │                        │
│        │            │    │  │     │                        │
│        │            │    │  │     │                        │
│        │            │ NE │  │     │   Output 1             │
│        │            │ 555│  │     ├────────────────────────┼─→ To Phase Detector
│        │            │    │  │     │                        │   & ESP32 GPIO0
│        │            │    │3 │     │                        │
│        │            │    ├──┤     │                        │
│ VCC ───┤            │  R │  │ CV  │                        │
│        │            │    │  │     │                        │
│        │            │    │  │     │                        │
│        │            │    │4 │ 5   │  0.1µF                │
│        │            │    ├──┼─────┼───────┐                │
│        │            │    │  │     │       │                │
│        │            └────┴──┴─────┘       │                │
│        │               │    │             │                │
│        │               │    │             ▼                │
│        │               │    └───────→ GND                  │
│        │               │                                   │
│        │               │                                   │
│        │               │                                   │
│        ▼               ▼                                   │
│       47kΩ           1µF                                  │
│        │               │                                   │
│        └───────────────┘                                   │
│        │                                                   │
│        ▼                                                   │
│       GND                                                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                     Oscillator 2 (3Hz)                      │
│                                                             │
│                     ┌────┬──┬─────┐                         │
│                     │    │  │     │                         │
│      4.7kΩ          │  8 │7 │ 6   │                        │
│        ┌────────────┤Vcc D  T     │                        │
│        │            │    │  │     │                        │
│        │            │    │  │     │                        │
│        │            │ NE │  │     │   Output 2             │
│        │            │ 555│  │     ├────────────────────────┼─→ To Phase Detector
│        │            │    │  │     │                        │   & ESP32 GPIO2
│        │            │    │3 │     │                        │
│        │            │    ├──┤     │                        │
│ VCC ───┤            │  R │  │ CV  │                        │
│        │            │    │  │     │                        │
│        │            │    │  │     │                        │
│        │            │    │4 │ 5   │  0.1µF                │
│        │            │    ├──┼─────┼───────┐                │
│        │            │    │  │     │       │                │
│        │            └────┴──┴─────┘       │                │
│        │               │    │             │                │
│        │               │    │             ▼                │
│        │               │    └───────→ GND                  │
│        │               │                                   │
│        │               │                                   │
│        │               │                                   │
│        ▼               ▼                                   │
│       30kΩ           1µF                                  │
│        │               │                                   │
│        └───────────────┘                                   │
│        │                                                   │
│        ▼                                                   │
│       GND                                                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                     Oscillator 3 (5Hz)                      │
│                                                             │
│                     ┌────┬──┬─────┐                         │
│                     │    │  │     │                         │
│      4.7kΩ          │  8 │7 │ 6   │                        │
│        ┌────────────┤Vcc D  T     │                        │
│        │            │    │  │     │                        │
│        │            │    │  │     │                        │
│        │            │ NE │  │     │   Output 3             │
│        │            │ 555│  │     ├────────────────────────┼─→ To Phase Detector
│        │            │    │  │     │                        │   & ESP32 GPIO15
│        │            │    │3 │     │                        │
│        │            │    ├──┤     │                        │
│ VCC ───┤            │  R │  │ CV  │                        │
│        │            │    │  │     │                        │
│        │            │    │  │     │                        │
│        │            │    │4 │ 5   │  0.1µF                │
│        │            │    ├──┼─────┼───────┐                │
│        │            │    │  │     │       │                │
│        │            └────┴──┴─────┘       │                │
│        │               │    │             │                │
│        │               │    │             ▼                │
│        │               │    └───────→ GND                  │
│        │               │                                   │
│        │               │                                   │
│        │               │                                   │
│        ▼               ▼                                   │
│       15kΩ           1µF                                  │
│        │               │                                   │
│        └───────────────┘                                   │
│        │                                                   │
│        ▼                                                   │
│       GND                                                  │
└─────────────────────────────────────────────────────────────┘
```

### A.2.2 Fine-Tunable Oscillator Circuit

For greater precision, replace fixed resistors with tunable combinations:

```
VCC (5V)
  │
  │
  ▼
┌────┐ 4.7kΩ
│    ├────────┐
│    │        │
│ 555│        │
│    │        │
│    │        │        Output
│    ├────────┼───────────→
│    │        │
│    │        │
└────┘        │
  │           │
  │           │
  │           │
  │           │
  │           ▼
  │         ┌───┐
  │         │   │ Fixed resistor
  │         │   │ (e.g., 39kΩ)
  │         │   │
  │         └───┘
  │           │
  │           │
  │           ▼
  │         ┌───┐
  │         │ ↑↓│ 10kΩ trimmer
  │         │   │ potentiometer
  │         └───┘
  │           │
  │           │
  ▼           ▼
┌───┐         │
│   │ 1µF     │
│   │         │
└───┘         │
  │           │
  ▼           ▼
 GND         GND
```

## A.3 Phase Detector Network Schematics

### A.3.1 Complete Phase Detector Circuit

```
                           VCC (3.3V)
                              │
                              │
                         0.1µF ┌───┐
                              │    │
                              ▼    ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Phase Detector Network                     │
│                                                                 │
│  Oscillator 1 ──────┐                                           │
│  Output             │                                           │
│                     ▼                                           │
│                ┌────────┐                                       │
│                │1      14│                                      │
│                │        │◀── VCC (3.3V)                        │
│                │        │                                       │
│  Oscillator 2 ─┤2     13│                                       │
│  Output        │        │                                       │
│                │        │                                       │
│                │ CD4070B│                10kΩ         0.1µF     │
│                │  Quad  │    Phase 1-2   ┌───┐        ┌───┐     │
│                │  XOR   │3  Output      │    │       │    │     │
│                │  Gate  ├─────────────────────────────▶    │     │
│                │        │                └───┘        └───┘     │
│                │        │                  │            │      │
│  Oscillator 3 ─┤8      │                  │            │      │
│  Output        │        │                  ▼            ▼      │
│                │        │                 GND          GND     │
│                │        │                                      │
│                │7       │                                      │
│                └────────┘                                      │
│                   │                                            │
│                   ▼                                            │
│                  GND                                           │
│                                                               │
│                                                               │
│                ┌────────┐                                     │
│                │5      4 │                                     │
│  Oscillator 1 ─┤        ├──┐                                  │
│  Output        │        │  │                                  │
│                │        │  │                                  │
│                │        │  │     10kΩ         0.1µF           │
│  Oscillator 3 ─┤6       │  │    ┌───┐        ┌───┐           │
│  Output        │        │  └────┤    ├───────┤    ├─────────► To ESP32 ADC1
│                │        │       └───┘        └───┘            │ (GPIO39)
│                │        │         │            │              │
│                │        │         ▼            ▼              │
│                │        │        GND          GND             │
│                └────────┘                                     │
│                                                               │
│                                                               │
│                ┌────────┐                                     │
│                │8      10│                                     │
│  Oscillator 2 ─┤        ├──┐                                  │
│  Output        │        │  │                                  │
│                │        │  │                                  │
│                │        │  │     10kΩ         0.1µF           │
│  Oscillator 3 ─┤9       │  │    ┌───┐        ┌───┐           │
│  Output        │        │  └────┤    ├───────┤    ├─────────► To ESP32 ADC2
│                │        │       └───┘        └───┘            │ (GPIO34)
│                │        │         │            │              │
│                │        │         ▼            ▼              │
│                │        │        GND          GND             │
│                └────────┘                                     │
│                                                               │
│                                                               │
└─────────────────────────────────────────────────────────────────┘
```

### A.3.2 Entropy Source Circuit

```
      VCC (3.3V)
          │
          │
          ▼
        ┌───┐
        │   │ 1kΩ
        │   │
        └───┘
          │
          │
          ▼
          │
          │ BZX79C3V3
          │ Zener Diode
         ─┼─
          │
          │
          │
          ▼
        ┌───┐
        │   │ 10kΩ
        │   │
        └───┘
          │
          │         ┌───┐
          ├─────────┤   │ 0.01µF
          │         │   │
          │         └───┘
          │           │
          │           │
          ▼           ▼
         GND         GND
          │           │
          │           │
          └───────────┘
                │
                │
                ▼
            To ESP32 ADC3
            (GPIO35)
```

## A.4 ESP32 Connection Diagram

### A.4.1 ESP32 Pinout with Connected Components

```
┌───────────────────────────────────────────────────────────────────────┐
│                                                                       │
│                        ┌──────────────────┐                           │
│                        │                  │                           │
│                        │                  │                           │
│                        │                  │                           │
│       GND ─────────────┤GND            5V ├───────── 5V Power         │
│                        │                  │                           │
│  Oscillator 1 ─────────┤GPIO0    GPIO22  ├───────── OLED SCL         │
│                        │                  │                           │
│                        │GPIO1    GPIO21  ├───────── OLED SDA         │
│                        │                  │                           │
│  Oscillator 2 ─────────┤GPIO2    GPIO19  ├───────── LED 2            │
│                        │                  │                           │
│                        │GPIO3    GPIO18  ├───────── LED 1            │
│                        │                  │                           │
│                        │GPIO4    GPIO17  ├───────── Button 2         │
│                        │                  │                           │
│                        │      ESP32      │                           │
│                        │                  │                           │
│                        │                  │                           │
│                        │                  │                           │
│  Oscillator 3 ─────────┤GPIO15   GPIO16  ├───────── Button 1         │
│                        │                  │                           │
│                        │GPIO13           │                           │
│                        │                  │                           │
│                        │GPIO12           │                           │
│                        │                  │                           │
│                        │GPIO14           │                           │
│                        │                  │                           │
│                        │GPIO27   GPIO36  ├───────── Phase Detector 1-2│
│                        │         (ADC0)  │                           │
│                        │                  │                           │
│                        │GPIO26   GPIO39  ├───────── Phase Detector 1-3│
│                        │         (ADC1)  │                           │
│                        │                  │                           │
│                        │GPIO25   GPIO34  ├───────── Phase Detector 2-3│
│                        │         (ADC2)  │                           │
│                        │                  │                           │
│                        │GPIO33   GPIO35  ├───────── Entropy Source    │
│                        │         (ADC3)  │                           │
│                        │                  │                           │
│                        │GPIO32   GND     ├───────── GND              │
│                        │                  │                           │
│       3.3V ────────────┤3.3V             │                           │
│                        │                  │                           │
│                        └──────────────────┘                           │
│                                                                       │
└───────────────────────────────────────────────────────────────────────┘
```

### A.4.2 I2C Display Connection

```
┌──────────────────────────────┐
│                              │
│    OLED Display Module       │
│    128x64 SSD1306            │
│                              │
│  ┌────┬────┬────┬────┐       │
│  │GND │VCC │SCL │SDA │       │
│  └─┬──┴─┬──┴─┬──┴─┬──┘       │
│    │    │    │    │          │
│    │    │    │    │          │
│    ▼    ▼    ▼    ▼          │
│   GND  3.3V  │    │          │
│              │    │          │
│              │    │          │
│              │    └──────────┐
│              │               │
│              └───────────────┐
│                              │
└──────────────────────────────┘
                │    │
                │    │
                │    │
                ▼    ▼
             GPIO22 GPIO21
                │    │
                │    │
                │    │
                ▼    ▼
              ESP32 Board
```

### A.4.3 Button and LED Connection

```
                VCC (3.3V)
                    │
                    │  10kΩ Pull-up Resistors
                    ├─────┬────┐
                    │     │    │
                    ▼     ▼    │
                  ┌───┐ ┌───┐  │
                  │   │ │   │  │
                  └───┘ └───┘  │
                    │     │    │
                    │     │    │
                    │     │    │
        ┌───────────┘     └────┼───────────┐
        │                      │           │
        │                      │           │
        │                      │           │
        ▼                      ▼           │
      Button 1               Button 2      │
        │                      │           │
        │                      │           │
        ▼                      ▼           │
      GPIO16                 GPIO17        │
        │                      │           │
        │                      │           │
       ESP32                  ESP32        │
                                          │
                                          │
         ESP32                ESP32       │
          │                     │         │
          │                     │         │
          ▼                     ▼         │
        GPIO18                GPIO19      │
          │                     │         │
          │                     │         │
          ▼                     ▼         │
        ┌───┐                 ┌───┐       │
        │   │ 330Ω            │   │ 330Ω  │
        └───┘                 └───┘       │
          │                     │         │
          │                     │         │
          ▼                     ▼         │
        ┌───┐                 ┌───┐       │
        │ + │ LED 1           │ + │ LED 2 │
        └───┘                 └───┘       │
          │                     │         │
          │                     │         │
          ▼                     ▼         │
         GND                   GND        │
          │                     │         │
          │                     │         │
          └─────────────────────┘
```

## A.5 PCB Layout

### A.5.1 Complete PCB Layout

The Prime Resonance Computer PCB is designed as a single-sided board for ease of manufacturing. Below are the top and bottom layer views:

**Top Layer:**

```
┌────────────────────────────────────────────────────────────┐
│                      ┌─────────┐                           │
│  ┌─────┐  ┌─────┐    │         │                          │
│  │555#1│  │555#2│    │  ESP32  │   ┌──────┐               │
│  └─────┘  └─────┘    │ MODULE  │   │OLED  │               │
│                      │         │   │DISPLAY│               │
│  ┌─────┐  ┌─────┐    │         │   │      │               │
│  │555#3│  │4070B│    │         │   │      │               │
│  └─────┘  └─────┘    └─────────┘   └──────┘               │
│                                                           │
│  C1   C2   C3      R1   R2   R3                          │
│  ○    ○    ○       ○    ○    ○                          │
│                                                           │
│  C4   C5   C6      R4   R5   R6       ┌────┐    ┌────┐    │
│  ○    ○    ○       ○    ○    ○       │BTN1│    │BTN2│    │
│                                       └────┘    └────┘    │
│  C7   C8   C9      R7   R8   R9                          │
│  ○    ○    ○       ○    ○    ○       LED1      LED2     │
│                                       ○         ○         │
│  ┌──────────────┐                                         │
│  │ POWER SUPPLY │                                         │
│  │   MODULE     │                                         │
│  └──────────────┘                                         │
│                                                           │
│  PWR                            EXPANSION HEADER          │
│  ○ ○                           ○ ○ ○ ○ ○ ○ ○ ○ ○ ○      │
│                                                           │
└────────────────────────────────────────────────────────────┘
```

**Bottom Layer (trace side):**

Simplified representation of PCB traces:

```
┌────────────────────────────────────────────────────────────┐
│      ╔══════╗            ╔══════╗                          │
│    ╔═╝      ╚═╗       ╔══╝      ╚══╗                      │
│    ║          ╠═══════╣           ║                       │
│    ║          ║       ║           ║                       │
│    ╚═╗      ╔═╝       ╚══╗      ╔══╝                      │
│      ╚══════╝            ╚══╦═══╝                         │
│                             ║                             │
│      ╔════╗             ╔═══╩══╗                          │
│    ╔═╝    ╚═╗         ╔═╝      ╚═╗                        │
│    ║        ╠═════════╣          ║                        │
│    ║        ║         ║          ║                        │
│    ╚═╗    ╔═╝         ╚═╗      ╔═╝                        │
│      ╚════╝             ╚══════╝                          │
│                                                           │
│      ╔═══════════════════════════╗                        │
│      ║                           ║                        │
│      ║   Power and Ground Plane  ║                        │
│      ║                           ║                        │
│      ╚═══════════════════════════╝                        │
│                                                           │
└────────────────────────────────────────────────────────────┘
```

### A.5.2 Component Placement Guide

```
┌────────────────────────────────────────────────────────────────────┐
│                           TOP VIEW                                 │
│                                                                    │
│  ┌────────────────┐                                                │
│  │                │                                                │
│  │     ESP32      │                                                │
│  │    MODULE      │                                                │
│  │                │          ┌──────────────┐                      │
│  │                │          │              │                      │
│  └────────────────┘          │  OLED DISPLAY│                      │
│         J1                   │              │                      │
│  ○ ○ ○ ○ ○ ○ ○ ○            │              │                      │
│                              └──────────────┘                      │
│  ┌────┐  ┌────┐  ┌────┐                                           │
│  │U1  │  │U2  │  │U3  │                          B1      B2       │
│  │555 │  │555 │  │555 │                          ┌┐      ┌┐       │
│  └────┘  └────┘  └────┘                          └┘      └┘       │
│                                                                    │
│  ┌────────┐      ┌────┐                          D1      D2       │
│  │  U4    │      │U5  │                          ┌┐      ┌┐       │
│  │CD4070B │      │REG │                          └┘      └┘       │
│  └────────┘      └────┘                                           │
│                                                                    │
│  C1  C2  C3  C4  C5  C6  C7        EXPANSION HEADER               │
│  ○   ○   ○   ○   ○   ○   ○   J2    ○ ○ ○ ○ ○ ○ ○ ○ ○ ○          │
│                                                                    │
│  R1  R2  R3  R4  R5  R6  R7  R8                                   │
│  ○   ○   ○   ○   ○   ○   ○   ○                                   │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘

Component Identifiers:
U1, U2, U3: NE555 timer ICs for oscillators
U4: CD4070B quad XOR gate
U5: 5V voltage regulator
C1-C7: Capacitors
R1-R8: Resistors
B1, B2: Pushbuttons
D1, D2: LEDs
J1: Power connector
J2: Expansion header
```

## A.6 Wiring Diagrams

### A.6.1 Breadboard Implementation

For those using breadboard construction, here's a simplified layout for reference:

```
┌───────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────────┐  │
│  │ + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + +│  │
│  │ - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -│  │
│  │                                                                         │  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │                               BREADBOARD 1                             │  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │                                                                         │  │
│  │ + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + +│  │
│  │ - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -│  │
│  └─────────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────────┐  │
│  │ + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + +│  │
│  │ - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -│  │
│  │                                                                         │  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │                               BREADBOARD 2                             │  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │ o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o o│  │
│  │                                                                         │  │
│  │ + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + + +│  │
│  │ - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -│  │
│  └─────────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
└───────────────────────────────────────────────────────────────────────────────┘

BREADBOARD 1: Oscillator circuits and phase detectors
BREADBOARD 2: ESP32, display, and user interface components
```

**Component Placement Notes:**

1. Keep oscillator circuits separated to minimize crosstalk
2. Place decoupling capacitors as close as possible to IC power pins
3. Keep analog signal paths short, especially between phase detectors and ESP32 ADC inputs
4. Use the second breadboard for the ESP32 and user interface components

### A.6.2 Power Distribution

```
Power Supply Input
      │
      ▼
┌─────────────┐
│             │
│  USB or     │
│  External   │
│  Power      │
│             │
└──────┬──────┘
       │
       ▼
┌──────────────┐
│              │
│   Voltage    │
│   Regulator  │
│              │
└──────┬───────┘
       │
     5V Rail
       │
  ┌────┴──────────┐
  │               │
  ▼               ▼
┌─────┐        ┌─────┐
│ESP32│        │     │
│3.3V │        │ 555 │
│Reg. │        │ ICs │
└──┬──┘        └─────┘
   │
3.3V Rail
   │
┌──┴────────────────┐
│                   │
▼                   ▼
┌─────┐          ┌─────┐
│Logic│          │OLED │
│ICs  │          │Disp.│
└─────┘          └─────┘
```

## A.7 Enclosure Design

### A.7.1 Enclosure Dimensions and Layout

```
┌───────────────────────────────────────────────────────┐
│                                                      │
│                   TOP PANEL                          │
│                                                      │
│  ┌───────────────┐           ┌────┐      ┌────┐      │
│  │               │           │    │      │    │      │
│  │               │           │BTN1│      │BTN2│      │
│  │    DISPLAY    │           │    │      │    │      │
│  │    CUTOUT     │           └────┘      └────┘      │
│  │               │                                   │
│  │               │           ┌────┐      ┌────┐      │
│  └───────────────┘           │LED1│      │LED2│      │
│                              └────┘      └────┘      │
│                                                      │
└───────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────┐
│                                                      │
│                  RIGHT PANEL                         │
│                                                      │
│                                                      │
│    ┌──────────────────┐                              │
│    │  USB PORT        │                              │
│    └──────────────────┘                              │
│                                                      │
│                                                      │
└───────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────┐
│                                                      │
│                  REAR PANEL                          │
│                                                      │
│                                                      │
│    ┌────┐               ┌─────────────────────┐      │
│    │PWR │               │  EXPANSION PORT     │      │
│    │JACK│               │                     │      │
│    └────┘               └─────────────────────┘      │
│                                                      │
│                                                      │
└───────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────┐
│                                                      │
│                 DIMENSIONS                           │
│                                                      │
│    Width:  150mm                                     │
│    Depth:  100mm                                     │
│    Height:  50mm                                     │
│                                                      │
│    Display cutout: 35mm × 20mm                       │
│    Button holes:   10mm diameter                     │
│    LED holes:      5mm diameter                      │
│    USB cutout:     12mm × 7mm                        │
│    Power jack:     8mm diameter                      │
│                                                      │
└───────────────────────────────────────────────────────┘
```

### A.7.2 Internal Mounting

```
┌───────────────────────────────────────────────────────┐
│                                                      │
│              INTERNAL MOUNTING LAYOUT                │
│                                                      │
│    ┌────────────┐        ┌──────────┐                │
│    │  4 × M3    │        │  PCB     │                │
│    │  STANDOFFS │◀───────┤  MOUNT   │                │
│    └────────────┘        │  POINTS  │                │
│                          └──────────┘                │
│                                                      │
│                                                      │
│    ┌────────────┐                                   │
│    │  DISPLAY   │                                   │
│    │  MOUNTING  │                                   │
│    │  BRACKETS  │                                   │
│    └────────────┘                                   │
│                                                      │
│                                                      │
└───────────────────────────────────────────────────────┘
```

## A.8 Detailed Component Specifications

### A.8.1 Component Specifications for Reference

| Component | Recommended Part | Specifications | Alternative Options |
|-----------|------------------|---------------|---------------------|
| 555 Timer IC | NE555P | Operating Voltage: 4.5-16V<br>Timing Range: 0-15Hz<br>Package: DIP-8 | TLC555 (lower power)<br>LM555 (higher current) |
| XOR Gate | CD4070B | Operating Voltage: 3-15V<br>Package: DIP-14<br>Logic Family: CMOS | 74HC86 (higher speed)<br>74LS86 (TTL compatible) |
| ESP32 | ESP32 DevKit | CPU: Dual-core 240MHz<br>Flash: 4MB<br>GPIO: 36 pins | ESP32-WROVER (more memory)<br>ESP32-S2 (simpler, cheaper) |
| OLED Display | SSD1306 | Resolution: 128×64<br>Interface: I2C<br>Size: 0.96" | SH1106 (better contrast)<br>SSD1309 (larger size option) |
| Resistors | Multiple Values | Power Rating: 1/4W<br>Tolerance: 1% or better | Use metal film for stability |
| Capacitors | Multiple Values | Ceramic/Film type<br>Voltage Rating: 16V min | Use low-temperature coefficient |
| Zener Diode | BZX79C3V3 | Voltage: 3.3V<br>Power: 0.5W | 1N4728A (alternative) |

### A.8.2 Oscillator Frequency Formula Reference

For reference during construction and tuning:

Frequency = 1.44 / ((R₁ + 2×R₂) × C)

Where:
- R₁ is the resistor connected between pins 7 and 8 (4.7kΩ in our design)
- R₂ is the resistor connected between pins 7 and 2 (varies to set frequency)
- C is the capacitor connected between pin 2 and ground (1μF in our design)

For the target frequencies:
- 2Hz oscillator: R₂ = 47kΩ (with 1μF capacitor)
- 3Hz oscillator: R₂ = 30kΩ (with 1μF capacitor)
- 5Hz oscillator: R₂ = 15kΩ (with 1μF capacitor)

## A.9 Advanced Options and Modifications

### A.9.1 Expanded Oscillator Configuration

```
┌─────────────────────────────────────────────────────────────┐
│             Advanced 5-Oscillator Configuration             │
│                                                             │
│  ┌───┐     ┌───┐     ┌───┐     ┌───┐     ┌───┐             │
│  │555│     │555│     │555│     │555│     │555│             │
│  │#1 │     │#2 │     │#3 │     │#4 │     │#5 │             │
│  │2Hz│     │3Hz│     │5Hz│     │7Hz│     │11Hz│            │
│  └─┬─┘     └─┬─┘     └─┬─┘     └─┬─┘     └─┬─┘             │
│    │         │         │         │         │               │
│    │         │         │         │         │               │
│    ▼         ▼         ▼         ▼         ▼               │
│  ┌──────────────────────────────────────────────┐          │
│  │                                              │          │
│  │              Phase Detector Array            │          │
│  │                                              │          │
│  └──────────────────────────────────────────────┘          │
│    │         │         │         │         │               │
│    │         │         │         │         │               │
│    ▼         ▼         ▼         ▼         ▼               │
│  ADC0       ADC1      ADC2      ADC3      ADC4             │
│    │         │         │         │         │               │
│    │         │         │         │         │               │
│    └─────────┴─────────┴─────────┴─────────┘               │
│                        │                                   │
│                        ▼                                   │
│                      ESP32                                 │
│                                                            │
└─────────────────────────────────────────────────────────────┘
```

### A.9.2 WiFi Connectivity Hardware

```
┌────────────────────────────────────────────┐
│                                            │
│              WiFi Configuration            │
│                                            │
│                                            │
│       ESP32                                │
│     ┌──────┐                              │
│     │      │                              │
│     │      │                              │
│     │      │      2.4GHz                  │
│     │      │      Antenna                 │
│     │      │         │                    │
│     │      │     ┌───┴────┐               │
│     │      │     │   RF   │               │
│     │      │     │ FILTER │               │
│     │      │     └────────┘               │
│     │      │                              │
│     │      │                              │
│     │      │                              │
│     └──────┘                              │
│                                            │
└────────────────────────────────────────────┘
```

### A.9.3 Optional External Clock Configuration

```
┌────────────────────────────────────────────────────┐
│           External Clock Configuration             │
│                                                   │
│        ┌──────────────┐                           │
│        │              │                           │
│        │   Crystal    │                           │
│        │  Oscillator  │                           │
│        │   10MHz      │                           │
│        │              │                           │
│        └──────┬───────┘                           │
│               │                                   │
│               │                                   │
│               ▼                                   │
│        ┌──────────────┐                           │
│        │              │                           │
│        │  Frequency   │                           │
│        │   Divider    │                          │
│        │   Circuit    │                          │
│        │              │                           │
│        └──┬─────┬─────┘                           │
│           │     │     │                           │
│           │     │     │                           │
│           ▼     ▼     ▼                           │
│        TO OSC1 OSC2  OSC3                        │
│                                                   │
└────────────────────────────────────────────────────┘
```

### A.9.4 Multi-Unit Network Setup

```
┌───────────────────────────────────────────────────────────┐
│             Multi-Unit Network Setup                     │
│                                                          │
│  ┌────────────┐      ┌────────────┐     ┌────────────┐    │
│  │            │      │            │     │            │    │
│  │  Primary   │      │ Secondary  │     │ Secondary  │    │
│  │   Unit     │◄────►│   Unit     │◄───►│   Unit     │    │
│  │            │      │            │     │            │    │
│  └────────────┘      └────────────┘     └────────────┘    │
│        │                                                 │
│        │                                                 │
│        ▼                                                 │
│  ┌────────────┐                                          │
│  │            │                                          │
│  │  Control   │                                          │
│  │  Computer  │                                          │
│  │            │                                          │
│  └────────────┘                                          │
│                                                          │
└───────────────────────────────────────────────────────────┘
```

## A.10 Digital Input Reference

For direct digital connections from oscillators to ESP32 for frequency measurement:

```
┌──────────────────────────────────────────────────────────────┐
│                Digital Input Connections                    │
│                                                             │
│                                                             │
│   Oscillator 1     Oscillator 2     Oscillator 3           │
│   Output           Output           Output                 │
│      │                │                │                    │
│      │                │                │                    │
│      ▼                ▼                ▼                    │
│   ┌────┐           ┌────┐           ┌────┐                 │
│   │    │           │    │           │    │                 │
│   │10kΩ│           │10kΩ│           │10kΩ│                 │
│   │    │           │    │           │    │                 │
│   └────┘           └────┘           └────┘                 │
│      │                │                │                    │
│      │                │                │                    │
│      ▼                ▼                ▼                    │
│   GPIO0            GPIO2            GPIO15                 │
│      │                │                │                    │
│      │                │                │                    │
│      └────────────────┴────────────────┘                    │
│                       │                                     │
│                       ▼                                     │
│                     ESP32                                   │
│                                                             │
└──────────────────────────────────────────────────────────────┘
```

## A.11 PCB Manufacturing Guidelines

### A.11.1 PCB Specifications

For those wishing to manufacture the PCB:

| Parameter | Specification |
|-----------|---------------|
| PCB Type | Single-sided (preferred) or double-sided |
| Board Material | FR-4 |
| Board Thickness | 1.6mm |
| Copper Thickness | 1oz (35μm) |
| Min Track Width | 0.25mm |
| Min Space | 0.25mm |
| Surface Finish | HASL or ENIG |
| Solder Mask | Green (preferred) |
| Silkscreen | White |
| Board Dimensions | 100mm × 80mm |

### A.11.2 PCB Assembly Notes

Key considerations for manual assembly:

1. **Component Placement**
   - Start with smaller components (resistors, capacitors)
   - Then add ICs and larger components
   - Mount the ESP32 module last

2. **Soldering Tips**
   - Use 0.8mm or thinner solder wire
   - Temperature-controlled iron set to 320-350°C
   - Clean tip frequently
   - Allow adequate cooling between components

3. **Testing During Assembly**
   - Test power rails before adding active components
   - Check for shorts between adjacent pins
   - Verify oscillator operation before connecting to ESP32
   - Test each subsystem individually before integration

## A.12 Reference Designators

Complete list of reference designators for the PCB:

| Designator | Component | Value |
|------------|-----------|-------|
| U1 | 555 Timer | NE555P (2Hz) |
| U2 | 555 Timer | NE555P (3Hz) |
| U3 | 555 Timer | NE555P (5Hz) |
| U4 | Quad XOR Gate | CD4070B |
| U5 | Voltage Regulator | LM7805 |
| MOD1 | ESP32 Module | ESP32-WROOM-32 |
| DISP1 | OLED Display | SSD1306 128×64 |
| R1, R2, R3 | Resistor | 4.7kΩ |
| R4 | Resistor | 47kΩ |
| R5 | Resistor | 30kΩ |
| R6 | Resistor | 15kΩ |
| R7, R8, R9 | Resistor | 10kΩ |
| R10, R11 | Resistor | 330Ω |
| R12 | Resistor | 1kΩ |
| C1, C2, C3 | Capacitor | 1μF |
| C4, C5, C6, C7 | Capacitor | 0.1μF |
| C8 | Capacitor | 0.01μF |
| C9 | Capacitor | 0.33μF |
| C10 | Capacitor | 100μF |
| D1 | Zener Diode | BZX79C3V3 |
| D2, D3 | LED | 5mm |
| S1, S2 | Pushbutton | Tactile |
| J1 | Power Connector | 2.1mm Barrel Jack |
| J2 | Expansion Header | 2×5 Pin Header |

---

These schematics and technical diagrams provide the complete reference for building the Prime Resonance Computer hardware. Use them in conjunction with the Hardware Assembly Guide in Section 2 for step-by-step construction instructions.