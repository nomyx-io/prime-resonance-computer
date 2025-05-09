# Operation Guide

## Introduction

This section guides you through the day-to-day operation of your Prime Resonance Computer. Now that your system is assembled, programmed, and calibrated, you're ready to perform various computational tasks and experiments. This guide covers basic operations, computational functions, result interpretation, and advanced usage scenarios.

The Prime Resonance Computer combines digital control with analog resonance phenomena to perform unique computational tasks. Understanding how to effectively operate the system will help you achieve optimal results in your explorations of prime resonance computing.

## Basic Operation

### Powering the System

1. **Power Connection**
   - Connect the power supply to the barrel jack or USB port.
   - Ensure stable power delivery (5V, at least 1A).

2. **Boot Sequence**
   - The system runs through an initialization sequence:
     - Power LED illuminates.
     - Display shows boot logo.
     - System performs self-test.
     - Oscillators activate.
     - Status LEDs indicate readiness.
   - The boot process takes approximately 5-10 seconds.

3. **Normal Operation Indicators**
   - Power LED: Solid green.
   - Status LEDs: Periodic flashing pattern.
   - OLED display: Home screen with system status.

4. **Power Management**
   - The system automatically enters low-power mode after inactivity.
   - Press any button to wake from low-power mode.
   - To fully power down, disconnect the power source.

### User Interface Navigation

Navigate the Prime Resonance Computer's interface using the buttons and display:

1. **Display Screens**
   - Main Menu: Entry point for all operations.
   - Operation Screens: Task-specific interfaces.
   - Results Screen: Displays computation outcomes.
   - System Status: Shows device health and parameters.
   - Oscillator Screen: Current frequencies and stability.
   - Phase Screen: Current phase relationships and metrics.
   - Network Screen: Connected nodes and communication status (for multi-node systems).
   - Computation Screen: Current computation status and recent results.

2. **Button Usage**
   - **MODE button**: Cycle through display screens or menu options. Long press (2 seconds) to return to the home screen. Double press to enter/exit settings menu.
   - **SELECT button**: Select highlighted option, confirm value entry, or back/cancel operation (long press).
   - **UP/DOWN buttons**: Navigate through menu options, increase/decrease values, or scroll through information.
   - **COMPUTE button (Coordinator node)**: Initiates a computational task.
   - **SYNC button (Coordinator node)**: Forces network synchronization.
   - **RESET button (Coordinator node)**: Resets the system to its default state.

3. **Indicator LEDs**
   - **POWER LED (Green)**: System is powered on (Solid). System idle, ready for operation (Solid).
   - **NETWORK LED (Blue)**: Network connection active (Solid). Oscillator 1 activity indicator (Blinks with oscillator cycles). Brightness indicates signal strength.
   - **COMPUTE LED (Yellow)**: Computation in progress (Solid). Flashes during data transmission. Off when no network activity.
   - **ERROR LED (Red)**: Error indication (Solid). Operation error or warning (Blinking). Off during normal operation.

## Running Computational Tasks

The Prime Resonance Computer can perform several types of computational tasks that leverage its unique architecture.

### Available Computation Modes

1. **Prime Verification**: Determines if a number is prime using resonance patterns. Displays confidence level and verification time.
2. **Prime Finding**: Searches for prime numbers in a specified range. Uses entropic resonance to identify candidate primes.
3. **Factorization**: Attempts to factorize composite numbers into primes. Performance varies based on number complexity.
4. **Pattern Recognition**: Identifies patterns in input data sequences. Maps patterns to prime resonance structures.
5. **Random Number Generation**: Generates true random numbers using entropy source. Offers various distribution options.

### Initiating a Computation

From the coordinator node:

1. Press the COMPUTE button to access the computation menu.
2. Use UP/DOWN buttons to select computation type.
3. Press SELECT to choose the computation.
4. Enter parameters when prompted:
   - For Prime Verification: Enter the number to test.
   - For Prime Finding: Enter the range (start, end).
   - For Factorization: Enter the number to factorize.
   - For Pattern Recognition: Select data input method.
   - For Random Numbers: Select quantity and properties.
5. Press SELECT to begin computation.
6. The COMPUTE LED will illuminate during processing.
7. Results will be displayed when computation completes.

### Example: Prime Verification

To verify if 997 is a prime number:

1. Press COMPUTE to access the menu.
2. Select "Prime Verification".
3. Enter "997" using UP/DOWN buttons to change digits and SELECT to confirm.
4. Press SELECT to begin verification.
5. The system will display:
   - Phase synchronization patterns.
   - Resonance stability measurements.
   - Final result: "997 is PRIME (99.9% confidence)".
   - Computation time.

**Using the Serial Console:**

```
compute prime 997
```

**Interpreting Results:**

The system will display:
- Whether the number is prime or composite.
- Confidence score (higher is better, typically >0.9 is reliable).
- Computation time.
- If composite, potential factors may be suggested.

**Example Output:**

```
Number: 997
Result: PRIME
Confidence: 0.987
Time: 3.24s
Resonance score: 8.7
```

For best results:
- Use higher computation intensity for large numbers.
- Perform multiple tests for critical applications.
- Compare confidence scores across different tests.

### Example: Prime Factorization

To factorize the number 391:

1. Press COMPUTE to access the menu.
2. Select "Factorization".
3. Enter "391" using the interface.
4. Press SELECT to begin factorization.
5. The system will display the progress and final result:
   - "391 = 17 × 23".
   - Computation time and confidence level.

**Using the Serial Console:**

```
compute factorize 391
```

**Interpreting Results:**

The system will display:
- The complete prime factorization expressed as multiplication.
- Confidence percentage for each factor.
- Computation time.
- For large numbers, factorization may be partial.

**Example Output:**

```
Number: 391
Factors: 17 × 23
Confidence: 0.998
Time: 2.7 seconds
```

For optimal factorization:
- Start with small numbers to understand the process.
- Large numbers may require higher computation intensity.
- Very large numbers (>10 digits) may exceed the system's capabilities.
- Multiple runs may yield different factorization paths with the same result.

### Prime Sequence Generation

This operation finds all prime numbers within a specified range, useful for mathematical exploration and pattern analysis.

**Using the Display Interface:**

1. From the main menu, select "Prime Sequence" using the Mode button.
2. Press Action button to enter sequence mode.
3. Set the start and end values for the range.
4. Start the process by selecting "Begin" and pressing Action.
5. The device will display the primes found in the range.
6. Use Up/Down to scroll through results.

**Using the Serial Console:**

```
compute prime_sequence 10 50
```

**Interpreting Results:**

The system will display:
- Total number of primes found.
- The complete list of primes in the range.
- Computation time.
- Optional: density and distribution metrics.

**Example Output:**

```
Range: 10 to 50
Primes found (11): 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47
Count: 11 primes found
Computation time: 5.3 seconds
```

Tips for sequence generation:
- Keep ranges reasonable (up to a few hundred numbers) for faster results.
- Use this feature to explore patterns in prime distribution.
- Export results for further mathematical analysis.

### Pattern Recognition

Identify patterns in numerical sequences:

**Using the Serial Command Interface:**

```
compute pattern 2,4,6,8,10
```

The system analyzes the sequence:

```
Analyzing pattern: 2,4,6,8,10
Pattern detected: Arithmetic progression
Formula: a(n) = 2n
Next 3 elements: 12, 14, 16
Confidence: 97.4%
Computation time: 1.2 seconds
```

**Supported Pattern Types:**

- Arithmetic progressions
- Geometric progressions
- Prime sequences
- Fibonacci-like sequences
- Power sequences
- Custom pattern recognition (with plugins)

**Advanced Pattern Recognition:**

Type: `compute pattern_deep <sequence>`

This performs more intensive analysis and can detect complex or combined patterns. Takes significantly longer to compute.

### Custom Computational Tasks

For researchers and advanced users, the system supports custom computational tasks:

1. **Loading Custom Algorithms:**
   - Place algorithm file in SPIFFS.
   - Type: `compute load <filename>`.
   - System loads and validates algorithm.
   - Example: `compute load factor_sieves.alg`.

2. **Running Custom Computations:**
   - Type: `compute custom <algorithm_name> [parameters...]`.
   - The system runs your custom algorithm.
   - Results format depends on algorithm design.

3. **Installing Computation Plugins:**
   - Create plugin following documentation format.
   - Upload to system using web interface or ESP32 file uploader.
   - Register plugin with: `plugin register <plugin_name>`.
   - Use like built-in functions.

## Monitoring System Status

### Real-Time Oscillator Monitoring

Monitor the health and performance of your oscillators:

1. Navigate to the Oscillator Screen.
2. Verify frequencies are within 0.5% of target values:
   - OSC1: 2.00 Hz ±0.01 Hz
   - OSC2: 3.00 Hz ±0.015 Hz
   - OSC3: 5.00 Hz ±0.025 Hz
3. Check stability indicators (should show "Stable").
4. If frequencies drift, recalibration may be necessary.

### Phase Relationship Analysis

Understanding phase relationships is key to system operation:

1. Navigate to the Phase Screen.
2. The display shows three phase relationships:
   - φ₁₂: Phase between OSC1 and OSC2.
   - φ₁₃: Phase between OSC1 and OSC3.
   - φ₂₃: Phase between OSC2 and OSC3.
3. Phase values range from 0° to 360°.
4. The graphical representation shows relative phase positions.
5. During computation, watch for phase locking patterns.

### Network Performance

For multi-node systems, network health is crucial:

1. Navigate to the Network Screen.
2. Verify all nodes are connected.
3. Check round-trip communication times (should be <100ms).
4. Monitor packet loss percentage (should be <1%).
5. If communication issues occur, check node placement and WiFi interference.

### System Logs

Access detailed system logs for troubleshooting:

1. Connect to the node via USB.
2. Open a serial terminal at 115200 baud.
3. Type `log show` to display recent system logs.
4. Type `log level 3` to increase logging detail (0-4).
5. Type `log export` to save logs to a file for analysis.

## Network Synchronization

### Automatic Synchronization

Under normal operation, nodes synchronize automatically:

1. The coordinator broadcasts synchronization signals periodically.
2. Each node adjusts its internal state based on these signals.
3. Phase relationships between nodes stabilize.
4. The Network Screen shows "Synchronized" status.

### Manual Synchronization

If automatic synchronization fails:

1. On the coordinator node, press the SYNC button.
2. Or type `network sync` in the serial console.
3. The coordinator will initiate a full synchronization sequence.
4. Verify all nodes show "Synchronized" status afterward.

### Synchronization Troubleshooting

If nodes fail to synchronize:

1. Check physical distances between nodes (should be <10m).
2. Verify there's no interference from other WiFi devices.
3. Ensure all nodes have correct firmware versions.
4. Try changing the WiFi channel in configuration.
5. As a last resort, perform a full system reset.

## Advanced Operational Modes

### Multi-Node Distributed Computation

For Prime Resonance Computer systems with multiple nodes:

1. **Preparing the Network:**
   - Ensure all nodes are powered on.
   - Verify network connectivity (`network status`).
   - Synchronize nodes (`network sync`).

2. **Distributed Computation Setup:**
   - From coordinator node, type: `compute distributed enable`.
   - Verify all nodes acknowledge (`network check`).

3. **Running Distributed Computations:**
   - Use standard computation commands with `distributed` flag.
   - Example: `compute distributed factorize 1273`.
   - The system distributes workload across nodes.

4. **Performance Gains:**
   - Speedup approximately 70-80% per additional node.
   - Most effective for complex computations.
   - Coordination overhead makes simple tasks slower.

5. **Monitoring Node Activity:**
   - Type: `network monitor`.
   - Shows real-time activity across all nodes.
   - Displays computation distribution and progress.

### Research Mode

For detailed analysis of resonance phenomena:

1. **Enabling Research Mode:**
   - Type: `mode research`.
   - This activates additional sensors and data collection.
   - OLED displays advanced metrics.

2. **Data Logging:**
   - Type: `log start <filename>`.
   - Records comprehensive resonance data.
   - Example: `log start experiment_12.dat`.

3. **Accessing Raw Data:**
   - Type: `data export <parameter> <duration>`.
   - Streams raw sensor data to serial connection.
   - Can be captured and analyzed externally.
   - Example: `data export phase_raw 10` (exports 10 seconds of data).

4. **Visualization Options:**
   - Type: `visualize <parameter>`.
   - Display shows real-time plots of selected parameter.
   - Options include:
     - `phase_space`: 3D phase relationship visualization.
     - `resonance_spectrum`: Frequency domain analysis.
     - `entropy_landscape`: Entropy surface visualization.

### Benchmark Mode

To evaluate and optimize system performance:

1. **Running Benchmarks:**
   - Type: `benchmark run`.
   - System performs standard test suite.
   - Results compare to reference performance.

2. **Specific Benchmark Tests:**
   - Type: `benchmark <test_name>`.
   - Options include:
     - `prime`: Prime verification performance.
     - `factor`: Factorization performance.
     - `resonance`: Resonance detection sensitivity.
     - `stability`: System stability metrics.

3. **Performance Optimization:**
   - Type: `benchmark optimize`.
   - System tests various parameters.
   - Suggests optimal configuration.
   - Apply recommendations with: `benchmark apply`.

## Result Interpretation and Analysis

Understanding and interpreting the results from your Prime Resonance Computer requires knowledge of both the output formats and the underlying theoretical principles.

### Standard Result Metrics

For most computational operations, results include these key metrics:

1. **Confidence Value:**
   - Range: 0.0-1.0 (0-100%).
   - Interpretation:
     - >95%: Very high certainty.
     - 80-95%: High certainty.
     - 60-80%: Moderate certainty.
     - <60%: Low certainty, consider rerunning with higher precision.
   - Factors affecting confidence:
     - Signal clarity.
     - Oscillator stability.
     - Resonance pattern distinctiveness.
     - Computational intensity setting.

2. **Resonance Score:**
   - Range: 0.0-10.0.
   - Higher values indicate stronger resonance patterns.
   - Prime numbers typically score >7.0.
   - Non-primes typically score <3.0.
   - "Gray area" between 3.0-7.0 requires higher precision.

3. **Entropy Metrics:**
   - Symbolic Entropy: Measure of prime factor distribution.
   - Phase Entropy: Variability in phase relationships.
   - Higher values generally correspond to prime characteristics.

4. **Processing Time:**
   - Reported in seconds or milliseconds.
   - Affected by:
     - Number size.
     - Computational intensity setting.
     - Hardware temperature.
     - Multi-node distribution.

### Visualizing Results

The Prime Resonance Computer offers visualization tools for deeper analysis:

1. **Phase Space Visualization:**
   - Type: `visualize phase_space <number>`.
   - Displays 3D representation of phase relationships.
   - Prime numbers show distinctive spiral patterns.
   - Composite numbers show more complex patterns with "factor echoes".

2. **Resonance Spectrum:**
   - Type: `visualize spectrum <number>`.
   - Displays frequency-domain representation of resonance.
   - Peaks correspond to prime factors or resonance harmonics.
   - Prime numbers show clean, single-peaked spectra.

3. **Entropy Surface:**
   - Type: `visualize entropy <number>`.
   - Shows how entropy changes across computational iterations.
   - Primes develop smooth, stable surfaces.
   - Composites show discontinuities at factor boundaries.

### Advanced Analysis Techniques

For researchers and advanced users:

1. **Comparative Analysis:**
   - Type: `analyze compare <number1> <number2>`.
   - Shows resonance pattern similarities and differences.
   - Useful for studying related numbers or sequences.

2. **Trend Analysis:**
   - Type: `analyze trend <start> <end> <step>`.
   - Analyzes patterns across a range of numbers.
   - Example: `analyze trend 100 200 10`.
   - Shows how resonance characteristics evolve across the range.

3. **Factor Relationship Mapping:**
   - Type: `analyze factors <number>`.
   - Maps resonance interactions between factors.
   - Visualizes hierarchical resonance structure.
   - Provides insights into factor relationships.

4. **Correlation Analysis:**
   - Type: `analyze correlate <parameter1> <parameter2>`.
   - Examines relationship between chosen parameters.
   - Generates correlation coefficient and visualization.
   - Example: `analyze correlate entropy_symbolic phase_stability`.

## Data Logging and Export

The Prime Resonance Computer can record data for external analysis:

### Basic Logging

1. **Starting a Log Session:**
   - Type: `log start <filename>`.
   - Begins recording system activity to file.
   - Example: `log start experiment_20250509.log`.

2. **Configuring Log Detail:**
   - Type: `log level <level>`.
   - Options:
     - `basic`: Core results only.
     - `standard`: Results and key metrics.
     - `detailed`: Comprehensive data (large files).
     - `debug`: All system information (very large files).

3. **Stopping Logging:**
   - Type: `log stop`.
   - Finalizes and closes the log file.

### Data Export Options

1. **CSV Export:**
   - Type: `export csv <data_type> <filename>`.
   - Creates comma-separated value file for spreadsheet analysis.
   - Example: `export csv prime_test results.csv`.

2. **JSON Export:**
   - Type: `export json <data_type> <filename>`.
   - Creates structured JSON data for programmatic analysis.
   - Example: `export json phase_data phases.json`.

3. **Raw Data Export:**
   - Type: `export raw <sensor> <duration> <filename>`.
   - Records direct sensor readings at maximum sampling rate.
   - Example: `export raw phase_detector1 30 raw_phase.dat`.

### Real-Time Data Streaming

Stream data over serial or network:

1. **Serial Streaming:**
   - Type: `stream <data_type> <interval>`.
   - Streams data over the serial connection at the specified interval (ms).
   - Example: `stream phase_angles 100`.

2. **Network Streaming (if WiFi enabled):**
   - Type: `stream network <data_type> <interval> <port>`.
   - Streams data over the network (e.g., WebSocket) at the specified interval (ms) and port.
   - Example: `stream network resonance 200 8888`.

## Remote Operation and Control

The Prime Resonance Computer can be controlled remotely via serial or network interfaces.

### Serial Control

Connect to the device via USB and use a serial terminal at 115200 baud to issue commands as described in the Command Reference (Appendix C).

### WiFi Remote Control (if WiFi enabled)

If WiFi is enabled and configured, you can access the web interface and API:

1. **Web Interface:**
   - Access the device's IP address in a web browser.
   - Provides a graphical interface for monitoring and control.

2. **REST API:**
   - Use HTTP requests to interact with the device programmatically.
   - Refer to Appendix C for API endpoint documentation.

3. **WebSocket API:**
   - Connect to the WebSocket endpoint for real-time data streaming.
   - Refer to Appendix C for WebSocket message types.

### ESP-NOW Network Control

In a multi-node system, the coordinator can issue commands to peer nodes using the network commands (Appendix C).

## Experimentation Techniques

### Scientific Method Application

To perform rigorous experiments with the Prime Resonance Computer:

1. **Formulate a hypothesis** about number properties or relationships.
2. **Design an experiment** using the device's capabilities.
3. **Control variables** by maintaining consistent parameters.
4. **Collect data** through multiple trials.
5. **Analyze results** using statistical methods.
6. **Draw conclusions** based on observed patterns.

### Optimization Experiments

These experiments focus on finding optimal parameters for specific computations:

1. **Define the performance metric** (speed, accuracy, etc.).
2. **Identify parameters** to optimize (computation intensity, sampling rate, etc.).
3. **Create a parameter grid** with various combinations.
4. **Run tests across the parameter grid**.
5. **Measure performance** for each combination.
6. **Identify optimal settings** for your specific application.

### Long-term Studies

For phenomena that evolve over time:

1. **Set up continuous monitoring** using data streaming.
2. **Establish baseline measurements**.
3. **Record environmental conditions** that might affect results.
4. **Analyze time-series data** for patterns and trends.
5. **Correlate with external factors** if applicable.

### Comparative Computing

Compare resonance-based computation with conventional algorithms:

1. **Select a computational problem** suitable for both approaches.
2. **Implement solutions** using both methods.
3. **Measure performance metrics** (speed, accuracy, scalability).
4. **Analyze strengths and weaknesses** of each approach.
5. **Identify complementary capabilities** for hybrid solutions.

## Practical Applications

### Educational Demonstrations

The Prime Resonance Computer serves as an exceptional educational tool:

1. **Number Theory Visualization:**
   - Demonstrate the physical embodiment of mathematical properties.
   - Visualize abstract concepts like primality and factorization.
   - Show the relationship between numbers and physical systems.

2. **Interactive Classroom Activities:**
   - Predictive challenges: Have students predict outcomes.
   - Pattern discovery: Identify patterns in sequences.
   - Hypothesis testing: Design and run experiments.

3. **STEM Integration Projects:**
   - Combine mathematics, physics, electronics, and computing.
   - Demonstrate interdisciplinary connections.
   - Inspire creative approaches to problem-solving.

### Mathematical Research

The system is a valuable tool for mathematical research:

1. **Prime Distribution Studies:**
   - Investigate patterns in prime number distribution.
   - Explore prime gaps and their properties.
   - Visualize prime number sequences.

2. **Number Theoretic Conjectures:**
   - Empirically test conjectures like the Twin Prime Conjecture.
   - Explore the behavior of number theoretic functions.

3. **Resonance Pattern Analysis:**
   - Study the unique resonance signatures of different numbers.
   - Analyze the structure of the entropy landscape.

### Cryptography Education

The Prime Resonance Computer can illustrate cryptographic concepts:

1. **Factorization Challenges:**
   - Demonstrate the difficulty of factoring large numbers.
   - Visualize the factorization process through resonance.

2. **Random Number Generation:**
   - Explain the importance of true random numbers in cryptography.
   - Show how physical processes can generate randomness.

3. **Post-Quantum Concepts:**
   - Introduce alternative computational approaches relevant to post-quantum cryptography.

### Interdisciplinary Explorations

The system facilitates interdisciplinary research:

1. **Physics and Number Theory:**
   - Explore the physical manifestation of mathematical properties.
   - Investigate connections between resonance and number theory.

2. **Biology and Computation:**
   - Draw parallels between neural oscillations and prime resonance.
   - Explore bio-inspired computing architectures.

3. **Information Theory and Physics:**
   - Study the information content of physical systems.
   - Investigate the relationship between entropy and computation.

## Conclusion

This operation guide provides the necessary information to effectively use your Prime Resonance Computer for basic tasks, advanced operations, and experimental explorations. By understanding the interface, computational functions, and analysis tools, you can leverage the unique capabilities of this system to investigate the fascinating world of prime resonance computing.