# Appendix D: Troubleshooting Guide

## Introduction

This appendix provides comprehensive troubleshooting information for the Prime Resonance Computer. Even with careful assembly and operation, you may encounter issues due to component variations, environmental factors, or software bugs. This guide organizes common problems by category, provides diagnostic procedures, and offers specific solutions.

For each issue, we provide:
- Symptoms for identification
- Potential causes
- Diagnostic steps
- Solutions ranked from simplest to most complex
- Prevention strategies

## Common Hardware Issues

### Oscillator Problems

#### Oscillator Not Generating Signal

**Symptoms**:
- Status LED for specific oscillator is off
- Phase detector shows constant values
- System reports "Oscillator Fault" on display
- Computation results have very low confidence

**Potential Causes**:
1. Incorrect wiring or loose connections
2. Faulty 555 timer IC
3. Power supply issues
4. Incorrect component values

**Diagnostic Steps**:
1. Check oscillator status: `oscillator status`
2. Measure voltage at oscillator output pin (should be square wave)
3. Verify power to 555 timer (pin 8 should be 5V)
4. Check timing resistor and capacitor connections

**Solutions**:
1. Reseat all connections to the oscillator circuit
2. Replace the 555 timer IC
3. Verify 5V supply voltage
4. Double-check resistor values against schematic
5. Replace timing components if necessary

**Prevention**:
1. Use IC sockets for easy replacement
2. Secure all connections during assembly
3. Use quality components from reputable suppliers

#### Unstable Oscillator Frequency

**Symptoms**:
- System repeatedly fails calibration
- "Frequency Drift Detected" warnings
- Inconsistent computation results
- Phase detector readings fluctuate significantly

**Potential Causes**:
1. Temperature fluctuations affecting components
2. Poor quality timing components
3. Interference from nearby electronics
4. Low power supply voltage or instability

**Diagnostic Steps**:
1. Monitor frequency stability: `oscillator stability <n>`
2. Check ambient temperature changes during operation
3. Isolate system from other electronics
4. Measure power supply voltage over time

**Solutions**:
1. Implement auto-calibration: `set auto_calibration true`
2. Replace timing components with 1% tolerance parts
3. Use temperature compensation (see Appendix A)
4. Shield oscillator circuit from interference
5. Improve power supply filtering

**Prevention**:
1. Operate in stable temperature environment
2. Use precision components for timing circuits
3. Shield oscillator section in metal enclosure
4. Add power supply filtering capacitors

#### Incorrect Oscillator Frequency

**Symptoms**:
- System reports frequency mismatch during calibration
- Computation results consistently incorrect
- ESP32 frequency measurements don't match expected values

**Potential Causes**:
1. Incorrect resistor/capacitor values
2. Component tolerance variations
3. Circuit loading effects
4. Missing calibration

**Diagnostic Steps**:
1. Check measured frequency: `oscillator status`
2. Verify component values with multimeter
3. Calculate expected frequency using formula: f = 1.44/((R1 + 2×R2)×C1)
4. Check for calibration status

**Solutions**:
1. Run calibration procedure: `calibration mode enter`
2. Adjust trimmer potentiometer if installed
3. Replace timing resistors with correct values
4. Add/remove small-value capacitors to fine-tune

**Prevention**:
1. Always install trimmer potentiometers for fine-tuning
2. Use 1% tolerance components
3. Calibrate the system after any temperature changes
4. Verify component values before installation

### Phase Detector Issues

#### No Phase Detection

**Symptoms**:
- ADC readings show constant values regardless of oscillator state
- "Phase Detector Fault" message
- Computation fails with "Invalid Phase Data" error

**Potential Causes**:
1. XOR gate not functioning
2. Incorrect wiring to ADC
3. Missing pull-up/pull-down resistors
4. Filter circuit issues

**Diagnostic Steps**:
1. Verify oscillators are functioning correctly
2. Check XOR gate outputs with multimeter
3. Verify connections between XOR outputs and ESP32 ADC inputs
4. Check RC filter components

**Solutions**:
1. Reseat connections to XOR gate
2. Replace XOR gate IC
3. Verify RC filter component values
4. Check ADC pin assignments in firmware
5. Rebuild phase detector circuit on new breadboard

**Prevention**:
1. Test XOR gates before installation
2. Use IC socket for easy replacement
3. Follow wiring diagram precisely
4. Double-check ADC pin connections

#### Noisy Phase Readings

**Symptoms**:
- Erratic phase detector values
- Inconsistent computation results
- High variance in repeated measurements
- Poor confidence scores

**Potential Causes**:
1. Insufficient filtering
2. Power supply noise
3. Digital noise coupling into analog inputs
4. Poor ground connections
5. Environmental interference

**Diagnostic Steps**:
1. Monitor raw phase readings: `data export phase_raw 10`
2. Check voltage ripple on power supply
3. Verify ground connections between all components
4. Test in different environments

**Solutions**:
1. Increase filter capacitor values (try 0.22µF or 0.33µF)
2. Improve power supply filtering
3. Add ferrite beads on signal lines
4. Separate digital and analog ground planes
5. Shield the phase detector circuit

**Prevention**:
1. Use star-point grounding
2. Keep analog and digital signals separated
3. Add bypass capacitors near ICs
4. Use shielded enclosure

#### Phase Detector Saturation

**Symptoms**:
- Phase readings stuck at max/min values
- Limited dynamic range in measurements
- Poor discrimination between different inputs

**Potential Causes**:
1. Incorrect resistor values in filter
2. ADC reference voltage issues
3. Signal levels too high

**Diagnostic Steps**:
1. Observe raw ADC values across input range
2. Check filter output voltage range
3. Verify ESP32 ADC reference voltage

**Solutions**:
1. Adjust resistor values in RC filter
2. Add voltage divider to reduce signal amplitude
3. Calibrate ADC reference voltage
4. Modify filter time constant

**Prevention**:
1. Calculate filter values to match ADC input range
2. Include buffer amplifier for impedance matching
3. Test phase detector with known inputs

### ESP32 and Interface Issues

#### ESP32 Not Responding

**Symptoms**:
- No output on serial console
- Display remains blank
- No response to button presses
- Cannot connect via WiFi or USB

**Potential Causes**:
1. Power supply issues
2. Boot failure
3. Flash corruption
4. Hardware failure

**Diagnostic Steps**:
1. Check power LED on ESP32 board
2. Verify USB connection (try different cable)
3. Monitor serial output during boot
4. Check boot button functionality

**Solutions**:
1. Reset ESP32 (press reset button or cycle power)
2. Re-flash firmware using Arduino IDE
3. Hold boot button during power-up for recovery mode
4. Replace ESP32 module if all else fails

**Prevention**:
1. Use quality USB cable and power supply
2. Add robust power regulation
3. Implement watchdog in firmware
4. Keep backup of working firmware

#### Display Not Working

**Symptoms**:
- Blank or corrupted display
- System functions normally otherwise
- Serial interface works correctly

**Potential Causes**:
1. Incorrect I2C connections
2. Address mismatch
3. Contrast/initialization issues
4. Defective display

**Diagnostic Steps**:
1. Check power to display (should be 3.3V)
2. Verify I2C connections (SDA/SCL)
3. Scan I2C bus for devices: `test i2c scan`
4. Check display initialization in serial logs

**Solutions**:
1. Verify wiring against schematic
2. Reset display power
3. Try different I2C address (0x3C or 0x3D common)
4. Adjust contrast setting: `set display_contrast 127`
5. Replace display module

**Prevention**:
1. Verify display works before complete assembly
2. Use quality display module
3. Add pull-up resistors on I2C lines
4. Keep display away from noise sources

#### Button Malfunctions

**Symptoms**:
- Buttons not registering presses
- Random button activations
- Buttons require multiple presses

**Potential Causes**:
1. Poor connections
2. Missing pull-up resistors
3. Switch bounce
4. GPIO configuration issues

**Diagnostic Steps**:
1. Test button inputs: `test gpio`
2. Check button connections
3. Verify pull-up configuration
4. Monitor button state changes

**Solutions**:
1. Clean button contacts
2. Add/replace pull-up resistors
3. Increase debounce time: `set button_debounce 100`
4. Replace button switch
5. Update GPIO configuration

**Prevention**:
1. Use quality tactile buttons
2. Implement software debouncing
3. Include pull-up resistors (10kΩ typical)

#### WiFi Connectivity Issues

**Symptoms**:
- Cannot connect to WiFi network
- Connection drops frequently
- Poor range or intermittent connectivity

**Potential Causes**:
1. Incorrect WiFi credentials
2. Signal interference
3. Power supply issues
4. Antenna problems

**Diagnostic Steps**:
1. Check WiFi status: `network status`
2. Verify credentials
3. Test in different locations
4. Monitor power during connection attempts

**Solutions**:
1. Reset WiFi settings: `network reset`
2. Update credentials: `set wifi_ssid "YourSSID"` and `set wifi_password "YourPassword"`
3. Change WiFi channel: `set ap_channel 6`
4. Add external antenna if possible
5. Reduce power saving for better connectivity: `set wifi_power_save false`

**Prevention**:
1. Position device for optimal WiFi reception
2. Use external antenna for better range
3. Keep WiFi credentials updated
4. Maintain adequate power supply

### Power Issues

#### System Resets Randomly

**Symptoms**:
- Unexpected restarts during operation
- Display briefly blanks then reinitializes
- Serial connection drops periodically

**Potential Causes**:
1. Insufficient power supply
2. Voltage drops during peak current
3. Watchdog timeout due to code issues
4. Brownout detection triggering

**Diagnostic Steps**:
1. Monitor power consumption during different operations
2. Check power supply capacity
3. Review serial logs for reset reason
4. Observe behavior during high-load operations

**Solutions**:
1. Use higher capacity power supply (>=1A)
2. Add bulk capacitors (100-470µF) near power input
3. Disable brownout detection temporarily for testing
4. Add voltage regulation if using battery power
5. Optimize code to reduce peak current demands

**Prevention**:
1. Always use adequate power supply
2. Include power filtering capacitors
3. Implement gradual startup sequence
4. Use separate regulators for digital and analog sections

#### Battery Drains Quickly

**Symptoms**:
- Short operation time on battery power
- Battery voltage drops rapidly
- System shuts down unexpectedly

**Potential Causes**:
1. High power consumption components
2. Missing power optimization
3. Battery capacity issues
4. Parasitic current draws

**Diagnostic Steps**:
1. Measure current consumption in different modes
2. Monitor battery voltage over time
3. Test with different power saving settings
4. Check for warm components

**Solutions**:
1. Enable power saving: `set power_save true`
2. Increase sleep intervals: `set deep_sleep_timeout 1800`
3. Disable unused peripherals
4. Use lower power oscillator alternatives
5. Switch to higher capacity battery

**Prevention**:
1. Design with power efficiency in mind
2. Use low-power components when possible
3. Implement proper power management in firmware
4. Size battery appropriately for expected usage

#### Overheating Issues

**Symptoms**:
- Components hot to touch
- System performance degrades over time
- Frequency drift in oscillators
- Automatic shutdown

**Potential Causes**:
1. Excessive current draw
2. Component failure
3. Short circuits
4. Poor ventilation

**Diagnostic Steps**:
1. Feel for unusually hot components
2. Measure current consumption
3. Check for short circuits
4. Monitor performance over time

**Solutions**:
1. Improve ventilation around device
2. Check for and fix short circuits
3. Add heat sinks to hot components
4. Replace damaged components
5. Reduce clock speeds or duty cycles

**Prevention**:
1. Design enclosure with ventilation
2. Use components within specified ratings
3. Implement thermal monitoring
4. Include thermal shutdown protection

## Firmware Problems

### Boot and Initialization Issues

#### System Won't Boot

**Symptoms**:
- ESP32 power LED on but no initialization
- No serial output or garbled text
- Display remains blank or shows initialization error

**Potential Causes**:
1. Corrupted firmware
2. Failed configuration
3. Hardware conflict
4. Memory initialization failure

**Diagnostic Steps**:
1. Connect serial monitor at 115200 baud
2. Observe boot messages
3. Check for error codes or panic messages
4. Try booting in safe mode (hold button 2 during power-up)

**Solutions**:
1. Re-flash firmware using Arduino IDE or esptool
2. Reset to factory defaults: Power on while holding both buttons
3. Clear configuration: `system factory_reset`
4. Erase flash completely and re-program

**Prevention**:
1. Keep firmware backups
2. Verify uploads before rebooting
3. Implement failsafe boot mode
4. Maintain recovery instructions

#### Calibration Failures

**Symptoms**:
- System reports "Calibration Failed" on boot
- "Unable to calibrate oscillator X" messages
- Poor computation reliability

**Potential Causes**:
1. Oscillator hardware issues
2. Environmental factors affecting frequency
3. Incorrect calibration parameters
4. ADC reading problems

**Diagnostic Steps**:
1. Check oscillator outputs
2. Try manual calibration: `calibration mode enter`
3. Verify ADC readings for each input
4. Check temperature stability

**Solutions**:
1. Adjust calibration tolerance: `set frequency_tolerance 0.05`
2. Increase calibration attempts: `set calibration_attempts 5`
3. Manually set frequency values if stable
4. Fix underlying hardware issues (see Hardware section)

**Prevention**:
1. Use stable components for oscillators
2. Add temperature compensation
3. Calibrate in stable environment
4. Allow warm-up time before calibration

### Computation and Algorithm Issues

#### Inaccurate Prime Detection

**Symptoms**:
- System incorrectly identifies non-primes as prime or vice-versa
- Inconsistent results for the same number
- Very low confidence scores

**Potential Causes**:
1. Oscillator frequency instability
2. Phase detection issues
3. Algorithm parameters not optimized
4. Signal noise affecting measurements

**Diagnostic Steps**:
1. Test with known primes and non-primes
2. Check confidence scores across multiple runs
3. Verify oscillator stability
4. Review phase detector readings

**Solutions**:
1. Increase computation intensity: `set compute_intensity 4`
2. Raise the confidence threshold: `set confidence_threshold 0.85`
3. Increase iterations: `set compute_iterations 5`
4. Calibrate the system
5. Fix underlying hardware issues

**Prevention**:
1. Regularly calibrate the system
2. Use higher computation intensity for important operations
3. Implement majority voting across multiple runs
4. Shield system from interference

#### Slow Computation Speed

**Symptoms**:
- Operations take much longer than expected
- System becomes unresponsive during computation
- Timeouts during complex operations

**Potential Causes**:
1. Excessive computation parameters
2. Memory limitations
3. ESP32 CPU overload
4. Background tasks interfering

**Diagnostic Steps**:
1. Monitor computation time for benchmark operations
2. Check memory usage: `system info`
3. Review active background tasks
4. Test with different computation parameters

**Solutions**:
1. Optimize computation parameters: `set compute_intensity 2`
2. Disable unnecessary features
3. Reduce sampling rate: `set phase_detect_samples 50`
4. Use distributed computation for large tasks
5. Update to latest firmware for optimizations

**Prevention**:
1. Balance accuracy vs. speed parameters
2. Close unused network connections
3. Implement progress reporting for long operations
4. Consider hardware upgrades for intensive applications

#### Memory-Related Crashes

**Symptoms**:
- System crashes during large computations
- "Out of memory" or "Stack overflow" errors
- Failure to start complex operations

**Potential Causes**:
1. Insufficient heap memory
2. Memory fragmentation
3. Stack overflow in recursion
4. Memory leaks

**Diagnostic Steps**:
1. Monitor free memory: `system info`
2. Check for memory fragmentation
3. Observe if crashes occur after extended operation
4. Test with simpler operations first

**Solutions**:
1. Increase heap reserve: `set heap_reserve 30`
2. Break large operations into smaller steps
3. Restart system before critical operations
4. Update to firmware with memory optimizations

**Prevention**:
1. Monitor memory usage during development
2. Implement graceful failure for memory-intensive operations
3. Use ESP32 model with more RAM if needed
4. Optimize algorithms for memory efficiency

### Network and Communication Issues

#### Multi-Node Synchronization Failures

**Symptoms**:
- "Node Sync Failed" messages
- Inconsistent results across nodes
- Nodes disconnect frequently

**Potential Causes**:
1. Insufficient signal strength
2. Timing inconsistencies
3. Channel interference
4. Incompatible firmware versions

**Diagnostic Steps**:
1. Check network status: `network status`
2. Verify node distances and barriers
3. Test different network channels
4. Compare firmware versions across nodes

**Solutions**:
1. Reduce distance between nodes
2. Change network channel: `set network_channel 11`
3. Increase sync interval: `set sync_interval 120`
4. Update all nodes to same firmware version
5. Add external antennas if possible

**Prevention**:
1. Position nodes for optimal communication
2. Maintain consistent firmware across all nodes
3. Use dedicated network channel
4. Implement robust error handling for sync failures

#### Serial Communication Problems

**Symptoms**:
- Garbled text on serial console
- Commands not recognized
- Missing or partial responses

**Potential Causes**:
1. Baud rate mismatch
2. Serial buffer overflow
3. USB driver issues
4. Hardware UART problems

**Diagnostic Steps**:
1. Try different baud rates
2. Check serial cable connections
3. Test with different terminal programs
4. Verify system load during communication

**Solutions**:
1. Reset serial baud rate: `set serial_baud 115200`
2. Update USB drivers on computer
3. Use hardware flow control if available
4. Try different USB port or cable

**Prevention**:
1. Use consistent baud rate settings
2. Keep command responses concise
3. Implement flow control for large data transfers
4. Use quality USB cables

## Calibration Challenges

### Oscillator Calibration Issues

#### Unable to Achieve Target Frequency

**Symptoms**:
- "Frequency adjustment limit reached" messages
- Calibration never completes
- Frequency remains off target despite adjustments

**Potential Causes**:
1. Component values too far from required
2. Adjustment mechanism insufficient
3. Environmental factors affecting frequency
4. Hardware limitations

**Diagnostic Steps**:
1. Measure actual oscillator frequency
2. Check component values
3. Calculate theoretical frequency with actual values
4. Verify calibration parameters

**Solutions**:
1. Replace timing components with values closer to target
2. Widen acceptable tolerance: `set frequency_tolerance 0.05`
3. Add trimmer potentiometers for fine adjustment
4. Switch to crystal-based oscillators for precision

**Prevention**:
1. Use adjustable components (trimmers)
2. Verify component values before installation
3. Design oscillator circuits with adjustment range
4. Consider temperature effects in component selection

#### Drift After Calibration

**Symptoms**:
- Initially calibrates successfully
- Frequency drifts over time
- Requires frequent recalibration

**Potential Causes**:
1. Temperature changes affecting components
2. Power supply voltage variations
3. Component aging
4. Circuit loading effects

**Diagnostic Steps**:
1. Monitor frequency over time
2. Measure ambient temperature changes
3. Check power supply stability
4. Observe drift pattern (gradual vs. sudden)

**Solutions**:
1. Allow longer warm-up time before calibration
2. Enable automatic recalibration: `set auto_calibration true`
3. Add temperature compensation with NTC thermistor
4. Improve power supply regulation
5. Use more stable component types (polystyrene capacitors, metal film resistors)

**Prevention**:
1. Thermally isolate oscillator components
2. Use low-temperature coefficient components
3. Schedule regular recalibration
4. Control operating environment temperature

### Phase Detector Calibration Issues

#### Inconsistent Phase Measurements

**Symptoms**:
- Phase values jump erratically
- Poor repeatability in measurements
- Calibration fails to establish baseline

**Potential Causes**:
1. Noise in phase detector circuit
2. Insufficient filtering
3. Ground loops or interference
4. ADC instability

**Diagnostic Steps**:
1. Monitor raw phase values: `data export phase_raw 10`
2. Check filtering components
3. Look for interference sources
4. Verify ADC reference stability

**Solutions**:
1. Increase filter capacitor values
2. Improve circuit isolation
3. Add shielding around phase detector
4. Increase ADC averaging: `set adc_averaging 128`
5. Implement digital filtering in firmware

**Prevention**:
1. Design with proper signal isolation
2. Use adequate filtering
3. Shield sensitive circuits
4. Implement both hardware and software filtering

#### Phase Offset Issues

**Symptoms**:
- Consistent bias in phase measurements
- Zero point calibration fails
- Phase relationships show systematic error

**Potential Causes**:
1. Component tolerance affecting filter response
2. ADC reference voltage offset
3. Signal path differences
4. Improper baseline calibration

**Diagnostic Steps**:
1. Check phase detector output with oscillators at known phase
2. Measure filter component actual values
3. Verify ADC reference voltage
4. Compare measurements across multiple runs

**Solutions**:
1. Run phase detector calibration: `calibration phase_detector`
2. Adjust software compensation values
3. Match filter components across channels
4. Implement offset correction in firmware

**Prevention**:
1. Use matched components for all phase detector channels
2. Include calibration routine in regular maintenance
3. Compensate for known offsets in software
4. Design for symmetrical signal paths

## Network Configuration Issues

### WiFi Setup Problems

#### Cannot Connect to Access Point

**Symptoms**:
- "WiFi Connection Failed" messages
- System continually retries connection
- IP address shows as 0.0.0.0

**Potential Causes**:
1. Incorrect SSID or password
2. WiFi signal too weak
3. Incompatible security settings
4. Router issues

**Diagnostic Steps**:
1. Verify WiFi credentials
2. Check signal strength near router
3. Confirm router is operating correctly
4. Test with different security settings

**Solutions**:
1. Update WiFi credentials: `set wifi_ssid "YourSSID"` and `set wifi_password "YourPassword"`
2. Move device closer to router
3. Try different WiFi channel: `set ap_channel 1`
4. Reset network settings: `network reset`
5. Use ESP32 with external antenna

**Prevention**:
1. Store WiFi credentials securely
2. Position device for optimal WiFi reception
3. Configure fallback access point mode
4. Implement connection retry with backoff

#### Access Point Mode Not Working

**Symptoms**:
- Cannot find device WiFi network
- Unable to connect to device AP
- Web interface not accessible

**Potential Causes**:
1. AP mode not enabled
2. Channel interference
3. SSID broadcast issues
4. IP address conflicts

**Diagnostic Steps**:
1. Check AP configuration: `network status`
2. Verify AP mode is enabled
3. Scan for conflicting networks
4. Check device IP configuration

**Solutions**:
1. Enable AP mode: `set wifi_mode "ap"` or `set wifi_mode "ap+sta"`
2. Change AP channel: `set ap_channel 6`
3. Make SSID visible: `set ap_hidden false`
4. Set static IP if needed
5. Restart network services: `network reset`

**Prevention**:
1. Configure unique SSID
2. Use less congested WiFi channels
3. Set appropriate security settings
4. Configure distinctive hostname

### Multi-Node Network Issues

#### Nodes Cannot Discover Each Other

**Symptoms**:
- "No nodes found" messages
- Network status shows 0 peers
- Unable to perform distributed operations

**Potential Causes**:
1. Excessive distance between nodes
2. Channel configuration mismatch
3. ESP-NOW limitations
4. MAC address filtering

**Diagnostic Steps**:
1. Check network configuration on all nodes
2. Verify distances between nodes
3. Test with nodes in close proximity
4. Review ESP-NOW channel settings

**Solutions**:
1. Position nodes closer together
2. Ensure all nodes use same channel: `set network_channel 1`
3. Verify MAC addresses are properly registered
4. Reset network on all nodes and rediscover
5. Use external antennas for extended range

**Prevention**:
1. Plan node placement for optimal coverage
2. Configure consistent network parameters
3. Use mesh networking for larger installations
4. Implement node discovery verification

#### Distributed Computation Failures

**Symptoms**:
- "Distributed operation failed" messages
- Results inconsistent across nodes
- Some nodes disconnect during operation
- Timeout errors

**Potential Causes**:
1. Network latency issues
2. Node processing capability differences
3. Synchronization problems
4. Inconsistent firmware versions

**Diagnostic Steps**:
1. Check connectivity between all nodes
2. Verify firmware versions match
3. Monitor node performance during operations
4. Test with simpler distributed tasks

**Solutions**:
1. Update all nodes to latest firmware
2. Reduce complexity of distributed operations
3. Increase operation timeout: `set distributed_timeout 120`
4. Adjust workload distribution
5. Improve network conditions

**Prevention**:
1. Maintain consistent firmware across all nodes
2. Test distributed operations incrementally
3. Implement robust error handling and recovery
4. Balance workloads based on node capabilities

## Advanced Diagnostic Procedures

### System-Level Diagnostics

#### Comprehensive System Test

Run the built-in test suite to check all system functions:

```
test all
```

This performs sequential tests of:
- All oscillators
- Phase detectors
- ADC functioning
- Memory allocation
- Storage access
- Network interfaces
- Display operation

#### Memory Analysis

For memory-related issues:

```
debug memory
```

This command reports:
- Free heap
- Fragmentation percentage
- Largest free block
- Stack high water marks
- Memory allocation patterns

#### Performance Benchmarking

To diagnose performance problems:

```
benchmark run
```

This measures:
- Oscillator frequency stability
- Phase detection precision
- Computation speed for standard tests
- Memory efficiency
- Network throughput (if applicable)

Compare results against baseline values in documentation.

### Hardware-Specific Diagnostics

#### Oscillator Waveform Analysis

If you have access to an oscilloscope:

1. Measure oscillator output waveforms
2. Verify square wave with proper amplitude (5V peak-to-peak)
3. Check frequency stability over time
4. Look for clean transitions without ringing or noise
5. Compare actual frequency with reported values

#### Phase Detector Signal Path Testing

Test the complete phase detector signal path:

1. Use `oscillator enable <n>` to selectively enable oscillators
2. Monitor phase detector outputs with:
   ```
   data export phase_raw 10
   ```
3. Verify expected patterns:
   - One oscillator enabled: Steady reading
   - Two oscillators: Varying pattern based on frequencies
4. Check signal levels at each stage (with multimeter or oscilloscope)

#### ADC Performance Verification

Test ESP32 ADC functionality:

```
test adc detailed
```

This performs:
- Linearity checks
- Noise measurement
- Reference voltage verification
- Input range testing

#### ESP32 GPIO Testing

Verify all GPIO pins used in the system:

```
test gpio
```

This tests:
- Input pins (readings and interrupt functionality)
- Output pins (setting high/low states)
- Button functioning
- LED control

### Advanced Configuration Recovery

#### Safe Mode Boot

If normal operation fails, boot into safe mode:

1. Power off the device
2. Hold Button 2 while powering on
3. Release when "Safe Mode" appears on display
4. Use minimal functionality to diagnose issues

#### Recovery from Flash Corruption

If firmware is corrupted:

1. Connect to computer with USB cable
2. Use esptool.py for complete erase:
   ```
   esptool.py --port COM# erase_flash
   ```
3. Re-flash firmware:
   ```
   esptool.py --port COM# --baud 921600 write_flash 0x0 firmware.bin
   ```

#### Configuration Reset Procedure

For configuration-related issues:

1. Connect via serial terminal
2. Enter the following command:
   ```
   config reset --force
   ```
3. Or for complete factory reset:
   ```
   system factory_reset --full
   ```

## Community Support Resources

### Online Resources

#### Project Documentation and Updates

- **Project Website**: [prime-resonance.org](https://prime-resonance.org)
- **GitHub Repository**: [github.com/prime-resonance/computer](https://github.com/prime-resonance/computer)
- **Documentation Wiki**: [wiki.prime-resonance.org](https://wiki.prime-resonance.org)

#### Community Forums

- **Main Discussion Forum**: [forum.prime-resonance.org](https://forum.prime-resonance.org)
- **Reddit Community**: [r/PrimeResonanceComputing](https://reddit.com/r/PrimeResonanceComputing)
- **Stack Exchange Tag**: [electronics.stackexchange.com/tags/prime-resonance](https://electronics.stackexchange.com/tags/prime-resonance)

#### Video Tutorials

- **YouTube Channel**: [youtube.com/PrimeResonanceProject](https://youtube.com/PrimeResonanceProject)
- **Assembly Walkthrough Series**: Step-by-step video guides for construction
- **Troubleshooting Playlist**: Common issues and their solutions

### Getting Help

#### Bug Reporting

If you encounter a bug not covered in this troubleshooting guide:

1. Check if the issue is already reported in the GitHub issues
2. Gather relevant information:
   - Exact error messages
   - Steps to reproduce
   - System configuration
   - Firmware version
   - Environment conditions
3. Submit a detailed bug report on GitHub or the forum

#### Support Channels

For personalized support:

- **Community Chat**: [Discord Server](https://discord.gg/prime-resonance)
- **Email Support**: support@prime-resonance.org
- **Weekly Help Sessions**: Online meetups (schedule on website)

#### Contributing Improvements

If you develop solutions or improvements:

1. Document your changes thoroughly
2. Submit pull requests to the GitHub repository
3. Share your experience on the forum
4. Consider writing a tutorial for the wiki

## Conclusion

This troubleshooting guide covers the most common issues you might encounter with the Prime Resonance Computer. Remember that this system combines digital and analog electronics, so problems can sometimes manifest in unexpected ways. A systematic approach to diagnosis is key.

If you encounter an issue not covered in this guide, refer to the community resources. The prime resonance computing community is collaborative and supportive, with many experienced builders who can help troubleshoot unusual problems.

We continuously update this guide based on user feedback and new discoveries. Check the project website for the latest version and additional troubleshooting information.