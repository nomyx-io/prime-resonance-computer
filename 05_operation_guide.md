# Operation Guide

## Introduction

This section guides you through the day-to-day operation of your Prime Resonance Computer. Now that your system is assembled, programmed, and calibrated, you're ready to perform various computational tasks and experiments. This guide covers basic operations, computational functions, result interpretation, and advanced usage scenarios.

The Prime Resonance Computer combines digital control with analog resonance phenomena to perform unique computational tasks. Understanding how to effectively operate the system will help you achieve optimal results in your explorations of prime resonance computing.

## Basic Operation

### Powering the System

1. **Power Connection**
   - Connect the power supply to the barrel jack or USB port
   - Ensure stable power delivery (5V, at least 1A)

2. **Boot Sequence**
   - The system runs through an initialization sequence:
     - Power LED illuminates
     - Display shows boot logo
     - System performs self-test
     - Oscillators activate
     - Status LEDs indicate readiness
   - The boot process takes approximately 5-10 seconds

3. **Normal Operation Indicators**
   - Power LED: Solid green
   - Status LEDs: Periodic flashing pattern
   - OLED display: Home screen with system status

4. **Power Management**
   - The system automatically enters low-power mode after inactivity
   - Press any button to wake from low-power mode
   - To fully power down, disconnect the power source

### User Interface Navigation

Navigate the Prime Resonance Computer's interface using the four buttons on the front panel:

1. **Mode Button**
   - Single press: Cycle through main menu options
   - Long press (2 seconds): Return to home screen
   - Double press: Enter/exit settings menu

2. **Select Button**
   - Single press: Select highlighted option
   - Long press (2 seconds): Back/cancel operation
   - When entering values: Confirm current value

3. **Up/Down Buttons**
   - Navigate through menu options
   - Increase/decrease values during parameter entry
   - Scroll through information on display screens

### Main Menu Structure

The main menu provides access to all system functions:

1. **Home**
   - System status overview
   - Active operation indicators
   - Network status (for multi-node systems)

2. **Compute**
   - Prime verification
   - Factorization
   - Pattern detection
   - Custom algorithms

3. **Monitor**
   - Oscillator frequencies
   - Phase relationships
   - Resonance patterns
   - Real-time visualization

4. **Network** (multi-node systems)
   - Node status and connections
   - Synchronization controls
   - Data sharing settings

5. **Settings**
   - System configuration
   - Display options
   - Power management
   - Profile selection

6. **Tools**
   - Diagnostics
   - Calibration
   - Testing
   - File management

### Display Screens and Indicators

The OLED display provides visual feedback and information:

1. **Home Screen**
   - Top row: System name and status
   - Middle: Current operation or mode
   - Bottom: Quick stats and notifications

2. **Compute Screen**
   - Input value
   - Operation progress
   - Results display
   - Confidence metrics

3. **Monitor Screen**
   - Real-time waveform visualization
   - Frequency values
   - Phase angle meters
   - Resonance indicators

4. **Settings Screen**
   - Parameter categories
   - Current values
   - Adjustment controls
   - Save/cancel options

### LED Indicator Patterns

The status LEDs provide system state information:

1. **Green LED**
   - Solid: System idle, ready for operation
   - Slow blink: Operation in progress
   - Fast blink: Results ready

2. **Blue LED**
   - Oscillator 1 activity indicator
   - Blinks with oscillator cycles (2 Hz)
   - Brightness indicates signal strength

3. **Yellow LED**
   - Network activity indicator
   - Flashes during data transmission
   - Off when no network activity

4. **Red LED**
   - Error indication
   - Off during normal operation
   - Solid: System error
   - Blinking: Operation error or warning

## Computational Operations

The Prime Resonance Computer can perform several types of computational tasks. Here's how to use each function:

### Prime Verification

Determine whether a number is prime using resonance patterns:

1. **Using the Display Interface**
   - Navigate to: Compute > Prime Test
   - Use Up/Down buttons to enter the number
   - Press Select to begin verification
   - Wait for computation to complete
   - Results show with confidence percentage

2. **Using the Serial Command Interface**
   - Connect to serial console (115200 baud)
   - Type: `compute prime <number>`
   - Example: `compute prime 17`
   - The system performs analysis and returns result:
     ```
     Computing prime verification for: 17
     Initializing resonance pattern...
     Analyzing pattern signatures...
     Result: 17 is PRIME
     Confidence: 99.7%
     Computation time: 3.4 seconds
     ```

3. **Interpreting Results**
   - "PRIME" or "NOT PRIME" determination
   - Confidence percentage (higher is better)
   - Computation time
   - Resonance pattern characteristics (advanced mode)

### Factorization

Find the prime factors of a composite number:

1. **Using the Display Interface**
   - Navigate to: Compute > Factorize
   - Use Up/Down buttons to enter the number
   - Press Select to begin factorization
   - Wait for computation to complete
   - Results show prime factors

2. **Using the Serial Command Interface**
   - Type: `compute factorize <number>`
   - Example: `compute factorize 15`
   - The system performs analysis and returns result:
     ```
     Computing factorization for: 15
     Initializing resonance pattern...
     Detecting factor resonances...
     Result: 15 = 3 × 5
     Confidence: 99.8%
     Computation time: 2.7 seconds
     ```

3. **Interpreting Results**
   - Prime factorization expressed as multiplication
   - Confidence percentage for each factor
   - Computation time
   - For large numbers, factorization may be partial

### Prime Sequence Generation

Generate prime numbers within a specified range:

1. **Using the Display Interface**
   - Navigate to: Compute > Prime Sequence
   - Enter start and end ranges
   - Press Select to begin generation
   - Results show as list of primes
   - Use Up/Down to scroll through results

2. **Using the Serial Command Interface**
   - Type: `compute prime_sequence <start> <end>`
   - Example: `compute prime_sequence 10 50`
   - The system generates all primes in the range:
     ```
     Generating primes between 10 and 50
     Primes in range: 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47
     Count: 11 primes found
     Computation time: 5.3 seconds
     ```

3. **Performance Considerations**
   - Practical range limit: ~10,000 for reasonable times
   - For large ranges, results stream as they're found
   - Cancel operation with Select button (long press)
   - Results can be exported via serial connection

### Pattern Recognition

Identify patterns in numerical sequences:

1. **Using the Serial Command Interface**
   - Type: `compute pattern <sequence>`
   - Example: `compute pattern 2,4,6,8,10`
   - The system analyzes the sequence:
     ```
     Analyzing pattern: 2,4,6,8,10
     Pattern detected: Arithmetic progression
     Formula: a(n) = 2n
     Next 3 elements: 12, 14, 16
     Confidence: 97.4%
     Computation time: 1.2 seconds
     ```

2. **Supported Pattern Types**
   - Arithmetic progressions
   - Geometric progressions
   - Prime sequences
   - Fibonacci-like sequences
   - Power sequences
   - Custom pattern recognition (with plugins)

3. **Advanced Pattern Recognition**
   - Type: `compute pattern_deep <sequence>`
   - This performs more intensive analysis
   - Can detect complex or combined patterns
   - Takes significantly longer to compute

### Custom Computational Tasks

For researchers and advanced users, the system supports custom computational tasks:

1. **Loading Custom Algorithms**
   - Place algorithm file in SPIFFS
   - Type: `compute load <filename>`
   - System loads and validates algorithm
   - Example: `compute load factor_sieves.alg`

2. **Running Custom Computations**
   - Type: `compute custom <algorithm_name> <parameters>`
   - The system runs your custom algorithm
   - Results format depends on algorithm design

3. **Installing Computation Plugins**
   - Create plugin following documentation format
   - Upload to system using web interface or ESP32 file uploader
   - Register plugin with: `plugin register <plugin_name>`
   - Use like built-in functions

## Advanced Operational Modes

### Multi-Node Distributed Computation

For Prime Resonance Computer systems with multiple nodes:

1. **Preparing the Network**
   - Ensure all nodes are powered on
   - Verify network connectivity (`network status`)
   - Synchronize nodes (`network sync`)

2. **Distributed Computation Setup**
   - From coordinator node, type: `compute distributed enable`
   - Verify all nodes acknowledge (`network check`)

3. **Running Distributed Computations**
   - Use standard computation commands with `distributed` flag
   - Example: `compute distributed factorize 1273`
   - The system distributes workload across nodes

4. **Performance Gains**
   - Speedup approximately 70-80% per additional node
   - Most effective for complex computations
   - Coordination overhead makes simple tasks slower

5. **Monitoring Node Activity**
   - Type: `network monitor`
   - Shows real-time activity across all nodes
   - Displays computation distribution and progress

### Research Mode

For detailed analysis of resonance phenomena:

1. **Enabling Research Mode**
   - Type: `mode research`
   - This activates additional sensors and data collection
   - OLED displays advanced metrics

2. **Data Logging**
   - Type: `log start <filename>`
   - Records comprehensive resonance data
   - Example: `log start experiment_12.dat`

3. **Accessing Raw Data**
   - Type: `data export <parameter> <duration>`
   - Streams raw sensor data to serial connection
   - Can be captured and analyzed externally
   - Example: `data export phase_raw 10` (exports 10 seconds of data)

4. **Visualization Options**
   - Type: `visualize <parameter>`
   - Display shows real-time plots of selected parameter
   - Options include:
     - `phase_space`: 3D phase relationship visualization
     - `resonance_spectrum`: Frequency domain analysis
     - `entropy_landscape`: Entropy surface visualization

### Benchmark Mode

To evaluate and optimize system performance:

1. **Running Benchmarks**
   - Type: `benchmark run`
   - System performs standard test suite
   - Results compare to reference performance

2. **Specific Benchmark Tests**
   - Type: `benchmark <test_name>`
   - Options include:
     - `prime`: Prime verification performance
     - `factor`: Factorization performance
     - `resonance`: Resonance detection sensitivity
     - `stability`: System stability metrics

3. **Performance Optimization**
   - Type: `benchmark optimize`
   - System tests various parameters
   - Suggests optimal configuration
   - Apply recommendations with: `benchmark apply`

## Result Interpretation and Analysis

Understanding and interpreting the results from your Prime Resonance Computer requires knowledge of both the output formats and the underlying theoretical principles.

### Standard Result Metrics

For most computational operations, results include these key metrics:

1. **Confidence Value**
   - Range: 0.0-1.0 (0-100%)
   - Interpretation:
     - >95%: Very high certainty
     - 80-95%: High certainty
     - 60-80%: Moderate certainty
     - <60%: Low certainty, consider rerunning with higher precision
   - Factors affecting confidence:
     - Signal clarity
     - Oscillator stability
     - Resonance pattern distinctiveness
     - Computational intensity setting

2. **Resonance Score**
   - Range: 0.0-10.0
   - Higher values indicate stronger resonance patterns
   - Prime numbers typically score >7.0
   - Non-primes typically score <3.0
   - "Gray area" between 3.0-7.0 requires higher precision

3. **Entropy Metrics**
   - Symbolic Entropy: Measure of prime factor distribution
   - Phase Entropy: Variability in phase relationships
   - Higher values generally correspond to prime characteristics

4. **Processing Time**
   - Reported in seconds or milliseconds
   - Affected by:
     - Number size
     - Computational intensity setting
     - Hardware temperature
     - Multi-node distribution

### Visualizing Results

The Prime Resonance Computer offers visualization tools for deeper analysis:

1. **Phase Space Visualization**
   - Type: `visualize phase_space <number>`
   - Displays 3D representation of phase relationships
   - Prime numbers show distinctive spiral patterns
   - Composite numbers show more complex patterns with "factor echoes"

2. **Resonance Spectrum**
   - Type: `visualize spectrum <number>`
   - Displays frequency-domain representation of resonance
   - Peaks correspond to prime factors or resonance harmonics
   - Prime numbers show clean, single-peaked spectra

3. **Entropy Surface**
   - Type: `visualize entropy <number>`
   - Shows how entropy changes across computational iterations
   - Primes develop smooth, stable surfaces
   - Composites show discontinuities at factor boundaries

### Advanced Analysis Techniques

For researchers and advanced users:

1. **Comparative Analysis**
   - Type: `analyze compare <number1> <number2>`
   - Shows resonance pattern similarities and differences
   - Useful for studying related numbers or sequences

2. **Trend Analysis**
   - Type: `analyze trend <start> <end> <step>`
   - Analyzes patterns across a range of numbers
   - Example: `analyze trend 100 200 10` 
   - Shows how resonance characteristics evolve across the range

3. **Factor Relationship Mapping**
   - Type: `analyze factors <number>`
   - Maps resonance interactions between factors
   - Visualizes hierarchical resonance structure
   - Provides insights into factor relationships

4. **Correlation Analysis**
   - Type: `analyze correlate <parameter1> <parameter2>`
   - Examines relationship between chosen parameters
   - Generates correlation coefficient and visualization
   - Example: `analyze correlate entropy_symbolic phase_stability`

## Data Logging and Export

The Prime Resonance Computer can record data for external analysis:

### Basic Logging

1. **Starting a Log Session**
   - Type: `log start <filename>`
   - Begins recording system activity to file
   - Example: `log start experiment_20250509.log`

2. **Configuring Log Detail**
   - Type: `log level <level>`
   - Options:
     - `basic`: Core results only
     - `standard`: Results and key metrics
     - `detailed`: Comprehensive data (large files)
     - `debug`: All system information (very large files)

3. **Stopping Logging**
   - Type: `log stop`
   - Finalizes and closes the log file

### Data Export Options

1. **CSV Export**
   - Type: `export csv <data_type> <filename>`
   - Creates comma-separated value file for spreadsheet analysis
   - Example: `export csv prime_test results.csv`

2. **JSON Export**
   - Type: `export json <data_type> <filename>`
   - Creates structured JSON data for programmatic analysis
   - Example: `export json phase_data phases.json`

3. **Raw Data Export**
   - Type: `export raw <sensor> <duration> <filename>`
   - Records direct sensor readings at maximum sampling rate
   - Example: `export raw phase_detector1 30 raw_phase.dat`

4. **Retrieving Exported Files**
   - Connect via web interface (navigate to system IP address)
   - Use "Files" section to download exports
   - Alternatively, use ESP32 file downloader tools

### Real-Time Data Streaming

For live analysis with external tools:

1. **Serial Data Streaming**
   - Type: `stream <data_type> <interval>`
   - Continuously sends data via serial connection
   - Example: `stream phase_angles 100` (every 100ms)
   - Stop with any key press

2. **Network Streaming** (if WiFi enabled)
   - Type: `stream network <data_type> <interval> <port>`
   - Broadcasts UDP packets with selected data
   - Can be received by network analysis tools
   - Example: `stream network resonance_metrics 200 8888`

## Remote Operation and Control

The Prime Resonance Computer can be operated remotely through various interfaces:

### Serial Control

1. **Basic Serial Connection**
   - Connect via USB cable
   - Use terminal emulator at 115200 baud
   - All commands available through serial interface
   - Supports scripted operation

2. **Scripted Operation**
   - Create text file with command sequence
   - Upload script to SPIFFS
   - Execute with: `script run <filename>`
   - Example: `script run batch_primes.cmd`

### WiFi Remote Control (if WiFi enabled)

1. **Web Interface**
   - Enable web server: `web enable`
   - Connect to the IP address shown on display
   - Web interface provides:
     - System status dashboard
     - Computation interface
     - File management
     - Settings configuration

2. **REST API**
   - Enable API: `api enable`
   - Interact programmatically via HTTP requests
   - Documentation available at: `http://<device-ip>/api/docs`
   - Supports GET/POST operations for all functions

### ESP-NOW Network Control

For multi-node systems:

1. **Coordinator Control**
   - All nodes controlled from coordinator
   - Commands prefixed with node ID target specific nodes
   - Example: `node2 compute prime 17`

2. **Broadcast Commands**
   - Send same command to all nodes
   - Prefix with `broadcast`
   - Example: `broadcast reboot`

## Troubleshooting Common Issues

### Oscillator Problems

1. **Frequency Drift**
   - Symptom: Declining computation accuracy
   - Solution: Recalibrate oscillators (`calibration mode enter`)
   - Prevention: Maintain stable operating temperature

2. **Unstable Readings**
   - Symptom: Fluctuating confidence values
   - Solution: Check power supply stability
   - Try: `oscillator stability test` to diagnose

3. **Oscillator Failure**
   - Symptom: Red LED and error message
   - Solution: Check oscillator circuit connections
   - Advanced: `debug oscillator <number>` for diagnostics

### Computation Issues

1. **Low Confidence Results**
   - Symptom: Confidence values below 70%
   - Solution: Increase computation intensity (`set compute_intensity 3`)
   - Try: `compute prime <number> extended` for deeper analysis

2. **Inconsistent Results**
   - Symptom: Different answers on repeated runs
   - Solution: Increase iterations (`set compute_iterations 5`)
   - Check: Phase detector calibration status

3. **Extremely Slow Computation**
   - Symptom: Operations taking much longer than expected
   - Solution: Check for background tasks (`system status`)
   - Try: Reset the system or use lower compute_intensity

### Network Problems (Multi-Node Systems)

1. **Node Connection Failures**
   - Symptom: "Node Unreachable" errors
   - Solution: Verify all nodes are powered on
   - Check: Distance between nodes (should be <10m)
   - Try: `network rescan` to rediscover nodes

2. **Synchronization Issues**
   - Symptom: "Sync Failed" messages
   - Solution: Verify clean power to all nodes
   - Try: `network reset` followed by `network sync`

3. **Inconsistent Network Performance**
   - Symptom: Variable network latency
   - Solution: Change WiFi channel to avoid interference
   - Try: `set network_channel 6` (try different channels)

### System Management

1. **System Reset**
   - Soft reset: `system reset`
   - Factory reset: `system factory_reset`
   - Remember: Factory reset erases all calibration data

2. **Firmware Update**
   - Over-the-air: `system update`
   - Via USB: Flash using Arduino IDE
   - Always backup settings first: `config backup`

3. **File System Management**
   - List files: `fs list`
   - Delete file: `fs delete <filename>`
   - Format file system: `fs format` (warning: erases all data)

## Conclusion

You now have the knowledge to operate your Prime Resonance Computer effectively. The system provides a unique platform for exploring computational approaches based on resonance phenomena and prime number theory.

As you become familiar with the system's operation, you can explore more advanced functions and develop your own computational experiments. The Prime Resonance Computer is not just a computational tool but a research platform for investigating the relationships between physical resonance and abstract mathematical concepts.

In the next section, we'll explore advanced projects and research possibilities that you can undertake with your Prime Resonance Computer.