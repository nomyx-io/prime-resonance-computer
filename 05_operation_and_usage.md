# Operation and Usage

## Introduction

Now that you've successfully assembled, configured, and calibrated your Prime Resonance Computer, you're ready to begin using it. This section covers the day-to-day operation of your system, including basic functions, computational tasks, monitoring, and advanced features.

## Basic Operations

### Power-On Sequence

The correct power-on sequence ensures proper system initialization:

1. First, power on the coordinator node (Node 1)
2. Wait for the coordinator to complete its boot sequence (display will show "Ready")
3. Power on the remaining nodes in any order
4. The coordinator will automatically discover and connect to other nodes
5. When all connections are established, the coordinator display will show "Network Complete"

### Understanding the User Interface

Each node features an OLED display that shows the system status. Navigate through information screens using the MODE button:

1. **Home Screen**
   - Node ID and name
   - System status
   - Network connectivity
   - Battery level (if applicable)

2. **Oscillator Screen**
   - Current frequencies of all three oscillators
   - Deviation from target frequencies
   - Stability indicators

3. **Phase Screen**
   - Current phase relationships between oscillator pairs
   - Phase stability metrics
   - Graphical phase representation

4. **Network Screen**
   - Connected peer nodes
   - Communication latency
   - Transmission statistics

5. **Computation Screen**
   - Current computation status
   - Results of recent computations
   - Performance metrics

### Control Interface

The physical controls on each node include:

- **POWER button** - Turns the node on/off
- **MODE button** - Cycles through display screens
- **SELECT button** - Selects options or triggers actions
- **UP/DOWN buttons** - Adjusts parameters or navigates menus

The coordinator node also features:

- **COMPUTE button** - Initiates a computational task
- **SYNC button** - Forces network synchronization
- **RESET button** - Resets the system to its default state

### Indicator LEDs

Status LEDs provide at-a-glance information:

- **POWER LED (Green)** - System is powered on
- **NETWORK LED (Blue)** - Network connection active
- **COMPUTE LED (Yellow)** - Computation in progress
- **ERROR LED (Red)** - Error condition detected

## Running Computational Tasks

The Prime Resonance Computer can perform several types of computational tasks that leverage its unique architecture.

### Available Computation Modes

1. **Prime Verification**
   - Determines if a number is prime using resonance patterns
   - Displays confidence level and verification time

2. **Prime Finding**
   - Searches for prime numbers in a specified range
   - Uses entropic resonance to identify candidate primes

3. **Factorization**
   - Attempts to factorize composite numbers into primes
   - Performance varies based on number complexity

4. **Pattern Recognition**
   - Identifies patterns in input data sequences
   - Maps patterns to prime resonance structures

5. **Random Number Generation**
   - Generates true random numbers using entropy source
   - Offers various distribution options

### Initiating a Computation

From the coordinator node:

1. Press the COMPUTE button to access the computation menu
2. Use UP/DOWN buttons to select computation type
3. Press SELECT to choose the computation
4. Enter parameters when prompted:
   - For Prime Verification: Enter the number to test
   - For Prime Finding: Enter the range (start, end)
   - For Factorization: Enter the number to factorize
   - For Pattern Recognition: Select data input method
   - For Random Numbers: Select quantity and properties

5. Press SELECT to begin computation
6. The COMPUTE LED will illuminate during processing
7. Results will be displayed when computation completes

### Example: Prime Verification

To verify if 997 is a prime number:

1. Press COMPUTE to access the menu
2. Select "Prime Verification"
3. Enter "997" using UP/DOWN buttons to change digits and SELECT to confirm
4. Press SELECT to begin verification
5. The system will display:
   - Phase synchronization patterns
   - Resonance stability measurements
   - Final result: "997 is PRIME (99.9% confidence)"
   - Computation time

### Example: Prime Factorization

To factorize the number 391:

1. Press COMPUTE to access the menu
2. Select "Factorization"
3. Enter "391" using the interface
4. Press SELECT to begin factorization
5. The system will display the progress and final result:
   - "391 = 17 × 23"
   - Computation time and confidence level

## Monitoring System Status

### Real-Time Oscillator Monitoring

Monitor the health and performance of your oscillators:

1. Navigate to the Oscillator Screen
2. Verify frequencies are within 0.5% of target values:
   - OSC1: 2.00 Hz ±0.01 Hz
   - OSC2: 3.00 Hz ±0.015 Hz
   - OSC3: 5.00 Hz ±0.025 Hz
3. Check stability indicators (should show "Stable")
4. If frequencies drift, recalibration may be necessary

### Phase Relationship Analysis

Understanding phase relationships is key to system operation:

1. Navigate to the Phase Screen
2. The display shows three phase relationships:
   - φ₁₂: Phase between OSC1 and OSC2
   - φ₁₃: Phase between OSC1 and OSC3
   - φ₂₃: Phase between OSC2 and OSC3
3. Phase values range from 0° to 360°
4. The graphical representation shows relative phase positions
5. During computation, watch for phase locking patterns

### Network Performance

For multi-node systems, network health is crucial:

1. Navigate to the Network Screen
2. Verify all nodes are connected
3. Check round-trip communication times (should be <100ms)
4. Monitor packet loss percentage (should be <1%)
5. If communication issues occur, check node placement and WiFi interference

### System Logs

Access detailed system logs for troubleshooting:

1. Connect to the node via USB
2. Open a serial terminal at 115200 baud
3. Type `log show` to display recent system logs
4. Type `log level 3` to increase logging detail (0-4)
5. Type `log export` to save logs to a file for analysis

## Network Synchronization

### Automatic Synchronization

Under normal operation, nodes synchronize automatically:

1. The coordinator broadcasts synchronization signals periodically
2. Each node adjusts its internal state based on these signals
3. Phase relationships between nodes stabilize
4. The Network Screen shows "Synchronized" status

### Manual Synchronization

If automatic synchronization fails:

1. On the coordinator node, press the SYNC button
2. Or type `network sync` in the serial console
3. The coordinator will initiate a full synchronization sequence
4. Verify all nodes show "Synchronized" status afterward

### Synchronization Troubleshooting

If nodes fail to synchronize:

1. Check physical distances between nodes (should be <10m)
2. Verify there's no interference from other WiFi devices
3. Ensure all nodes have correct firmware versions
4. Try changing the WiFi channel in configuration
5. As a last resort, perform a full system reset

## Advanced Features

### Data Logging and Export

Capture computational data for analysis:

1. Connect a USB drive to the coordinator node
2. Type `data export` in the serial console
3. Or select "Export Data" from the system menu
4. Choose data types to export:
   - Oscillator frequencies over time
   - Phase relationships
   - Computation results
   - System logs
5. Select export format (CSV, JSON, or binary)
6. Data will be saved to the USB drive

### Custom Computation Scripts

Advanced users can run custom computation tasks:

1. Create a script using the Prime Computation Scripting Language (PCSL)
2. Example script for custom prime testing:

```pcsl
// Custom prime test script
SET_ENTROPY 0.25
SAMPLE_OSCILLATORS 5000
ANALYZE_PHASE_LOCK OSC1 OSC2
IF RESONANCE > 0.8 THEN
  RESULT "Strong Prime Candidate"
ELSE
  RESULT "Not Prime or Weak Prime"
END
```

3. Save the script to a file with .pcsl extension
4. Copy to the coordinator node's storage
5. Run with: `script run filename.pcsl`

### Research Mode

For academic and research applications:

1. Enable research mode: `set mode research`
2. This enables additional features:
   - Raw data capture from all sensors
   - Higher precision measurements
   - Direct oscillator control
   - Advanced visualization options
3. Research data can be streamed to a computer for analysis
4. Use the provided Python or MATLAB interfaces for data processing

### Multi-System Clustering

Connect multiple Prime Resonance Computer systems together:

1. Configure one system as the master coordinator
2. Connect coordinators from each system via WiFi or wired Ethernet
3. On the master, run: `cluster discover`
4. Set the cluster topology: `cluster topology mesh`
5. Initialize the cluster: `cluster init`
6. Distributed computations can now span multiple systems
7. Use the cluster dashboard to monitor performance

## Performance Optimization

### Oscillator Fine-Tuning

For maximum computational performance:

1. Monitor oscillator precision over 24 hours
2. Identify any temperature-related drift
3. Apply compensation values:
   ```
   set oscillator1_temp_comp 0.002
   set oscillator2_temp_comp 0.003
   set oscillator3_temp_comp 0.004
   ```
4. Consider adding thermal insulation to reduce temperature variations

### Phase Detection Enhancement

Improve phase detection accuracy:

1. Enable advanced phase detection: `set phase_detection advanced`
2. Increase sampling rate: `set phase_sample_rate 1000`
3. Enable averaging: `set phase_averaging true`
4. These settings increase power consumption but improve computational accuracy

### Entropy Optimization

Fine-tune entropy injection for better results:

1. Analyze entropy quality: `entropy analyze`
2. Adjust entropy parameters based on analysis:
   ```
   set entropy_source hardware  // Uses hardware source only
   set entropy_weight 0.15      // Reduces entropy influence
   set entropy_filter true      // Enables filtering of outliers
   ```
3. Verify improvements with test computations

## Routine Maintenance

### System Updates

Keep your system updated:

1. Check for updates: `system check_update`
2. Apply available updates: `system update`
3. Updates may include:
   - Firmware improvements
   - Bug fixes
   - New computational algorithms
   - Performance optimizations

### Calibration Verification

Periodically verify calibration:

1. Run calibration check monthly: `calibration check`
2. System will verify all oscillators are at correct frequencies
3. If deviation exceeds 1%, perform full recalibration

### Hardware Inspection

Monthly hardware maintenance:

1. Check all connections for corrosion or loosening
2. Inspect heat-sensitive components (voltage regulators, ESP32)
3. Clean cooling vents and heat sinks if present
4. Verify power supply output voltages

## Next Steps

Now that you're familiar with the operation of your Prime Resonance Computer, proceed to the Troubleshooting and Maintenance section to learn how to address common issues and maintain your system for optimal performance.

Remember, this is a research platform designed to demonstrate the principles of prime resonance computing. As you gain experience, you may want to explore custom modifications and advanced applications covered in the final sections of this guide.