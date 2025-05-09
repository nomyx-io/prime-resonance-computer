# Prime Resonator Node - Enclosure Design

## Overview

This document provides designs for housing a prime resonator node in an attractive, functional enclosure. The design balances aesthetics, accessibility for education/debugging, and proper ventilation while maintaining visual appeal. The enclosure can be 3D printed or laser-cut from acrylic for cost-effective production.

## Design Considerations

### Functional Requirements
- House a complete prime resonator node PCB (80mm × 80mm)
- Provide visibility of key components for educational purposes
- Allow access to tuning potentiometers and test points
- Ensure proper cooling for components
- Protect sensitive circuitry from dust and accidental contact
- Enable easy access for modifications and repairs
- Provide stable mounting for displays and indicators
- Support interconnection between multiple nodes

### Aesthetic Requirements
- Thematic design that reflects prime number concepts
- Transparent sections for viewing internal components
- Integrated light diffusion for LED indicators
- Professional appearance suitable for academic/research environments
- Compact and stackable form factor

## Enclosure Specifications

### Dimensions
- External: 100mm × 100mm × 40mm
- Internal cavity: 85mm × 85mm × 35mm
- Wall thickness: 3mm
- Base thickness: 3mm
- Top thickness: 3mm

### Material Options
- 3D Printed: PLA or PETG (preferred for thermal stability)
- Laser-cut: 3mm clear or colored acrylic
- Mixed: 3D printed frame with acrylic panels

### Assembly Method
- Snap-fit design for tool-free assembly
- Alternative: M3 screws and heat-set inserts
- Modular components for easy customization

## Design Visualization

### Top View
```
    +------------------------------------------+
    |                                          |
    |  +----------------------------------+    |
    |  |                                  |    |
    |  |        OLED Display Window       |    |
    |  |                                  |    |
    |  +----------------------------------+    |
    |                                          |
    |  +--------+  +--------+  +--------+      |
    |  |        |  |        |  |        |      |
    |  | Prime  |  | Prime  |  | Prime  |      |
    |  |   2    |  |   3    |  |   5    |      |
    |  |        |  |        |  |        |      |
    |  | O LED  |  | O LED  |  | O LED  |      |
    |  +--------+  +--------+  +--------+      |
    |                                          |
    |                    +---------+           |
    |                    | Reset   |           |
    |                    +---------+           |
    |                                          |
    +------------------------------------------+
```

### Front View
```
    +------------------------------------------+
    |                                          |
    |  +--------------------+                  |
    |  |  OLED Display      |   Prime Computer |
    |  +--------------------+                  |
    |                                          |
    |  O      O      O      [Status LEDs]      |
    |                                          |
    |                                          |
    +------------------------------------------+
```

### Side View
```
    +------------------------------------------+
    |                                          |
    |                                          |
    |  [Clear Acrylic or Printed Panel]        |
    |                                          |
    |                                          |
    +------------------------------------------+
```

### Bottom View
```
    +------------------------------------------+
    |                                          |
    |  +-USB-+                                 |
    |  |     |                                 |
    |  +-----+                                 |
    |                                          |
    |  +------------------------------+        |
    |  |                              |        |
    |  |  Ventilation Grid            |        |
    |  |                              |        |
    |  +------------------------------+        |
    |                                          |
    |  O        O        O        O            |
    |  [Mounting Holes/Rubber Feet]            |
    |                                          |
    +------------------------------------------+
```

## Component Details

### Top Panel
- Clear acrylic or printed with cutouts for:
  - OLED display (25mm × 15mm window)
  - Three individual LED windows with labels
  - Access to potentiometers for oscillator tuning
  - Reset and program buttons

### Side Panels
- Ventilated design with prime number pattern
- Openings follow Fibonacci or prime number sequence
- USB port access on one side
- Connection ports for networking on opposite side

### Bottom Panel
- Mounting holes for PCB standoffs
  - 4× M3 holes positioned at 75mm × 75mm square
- Ventilation grid in prime-inspired pattern
- Rubber feet positions (4×)
- Cable management slots

### Light Diffusion
- Frosted acrylic light pipes for LED indicators
- Main RGB LED strip diffuser panel
- Light isolation between channels to prevent bleed

## Assembly Instructions

1. **Prepare the PCB**
   - Mount standoffs to PCB (4× M3 × 10mm)
   - Attach any external connectors (OLED, LED strip)

2. **Prepare the Enclosure**
   - Clean all parts of printing residue or protective film
   - Install heat-set inserts if using threaded assembly
   - Insert light pipes and diffusers

3. **Mount the PCB**
   - Place PCB on bottom panel standoffs
   - Secure with M3 screws

4. **Connect External Components**
   - Route OLED display to mounting position
   - Position RGB LED strip along designated channel
   - Route any external sensors or connections

5. **Assemble Enclosure**
   - Attach side panels to bottom panel
   - Route cables through designated openings
   - Attach top panel
   - Secure all panels using snap features or screws

6. **Final Touches**
   - Apply any labels or decals
   - Attach rubber feet to bottom panel
   - Perform power-on test

## Networking Multiple Nodes

The enclosure is designed for modular expansion in two ways:

### Horizontal Expansion
```
+----------+   +----------+   +----------+
|          |   |          |   |          |
|  Node 1  |<->|  Node 2  |<->|  Node 3  |
|          |   |          |   |          |
+----------+   +----------+   +----------+
```

- Side panels include interlocking features
- Pass-through ports for direct electrical connection
- Shared power distribution option

### Vertical Stacking
```
+----------+
|  Node 3  |
+----------+
     ↕
+----------+
|  Node 2  |
+----------+
     ↕
+----------+
|  Node 1  |
+----------+
```

- Corner posts with guide rails
- Power and signal bus through stacking connectors
- Secured with twist-lock mechanism

## Customization Options

### Functional Variants
- **Basic Node**: As described above
- **Display Node**: Extended front panel for larger display
- **Control Node**: Additional switches and input controls
- **Power Distribution Node**: Includes power supply for multiple nodes

### Aesthetic Variants
- Color-coded by prime number (e.g., 2=blue, 3=green, 5=red)
- Transparent vs. opaque panels
- Minimalist vs. detailed external patterning
- LED illumination intensity and pattern options

## 3D Printing Guidelines

### Print Settings
- **Layer Height**: 0.2mm
- **Infill**: 20% for structural parts, 100% for small features
- **Perimeters**: 3 minimum
- **Material**: PETG recommended (better heat resistance)
- **Supports**: Required for top panel USB port overhang
- **Orientation**: Print panels flat on build plate

### Post-Processing
- Sand any rough edges (especially around display cutout)
- Acetone vapor smoothing for ABS (optional)
- Heat-set insert installation using soldering iron
- Light pipe polishing for better light transmission

## Laser Cutting Guidelines

For acrylic version:

- **Material**: 3mm clear or colored acrylic
- **Kerf Compensation**: 0.1mm
- **Tab-and-Slot Design**: For assembly without adhesives
- **Protective Film**: Leave in place until final assembly
- **Flame Polishing**: For edges (optional)

## Source Files

The following design files are provided:

1. **3D Printable STL Files**:
   - `top_panel.stl`
   - `bottom_panel.stl`
   - `side_panel_usb.stl`
   - `side_panel_blank.stl`
   - `side_panel_network.stl`
   - `light_pipes.stl`

2. **Laser Cutting Files**:
   - `acrylic_panels.dxf`
   - `acrylic_panels.svg`

3. **Editable Design Files**:
   - Fusion 360: `node_enclosure.f3d`
   - FreeCAD: `node_enclosure.FCStd`

## Bill of Materials

| Item | Quantity | Notes |
|------|----------|-------|
| 3D Printed Panels | 1 set | Or laser-cut acrylic |
| M3 × 6mm Screws | 8 | For securing panels |
| M3 × 10mm Standoffs | 4 | For mounting PCB |
| M3 Heat-Set Inserts | 8 | If using threaded assembly |
| Rubber Feet | 4 | Self-adhesive |
| Light Pipes | 3 | For individual LEDs |
| Light Diffuser | 1 | For RGB LED strip |
| Acrylic Display Window | 1 | Pre-cut to size |

## Advanced Features

### Optional Enhancements
- **Active Cooling**: Space for 30mm fan if needed
- **External Probe Points**: Gold-plated contacts for oscilloscope probes
- **Magnetic Quick-Connect**: For rapid node reconfiguration
- **Cable Management System**: Internal routing channels and clips

### Production Scaling
For producing multiple enclosures:
- Consider injection molding for quantities >100
- Design tweaks for mold flow and ejection
- Simplified assembly with snap-fit only (no screws)