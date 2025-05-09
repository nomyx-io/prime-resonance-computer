# Calibration and Configuration

## Introduction

The Prime Resonance Computer relies on precise oscillator frequencies and accurate phase measurements to function correctly. This section guides you through the calibration procedures that are essential for optimal performance. 

Proper calibration ensures:
- Oscillators operate at exact prime frequencies
- Phase detectors provide accurate measurements 
- The system can accurately detect prime resonance patterns
- Computations yield reliable results

A well-calibrated Prime Resonance Computer will show significantly higher accuracy in prime verification, factorization, and other computational tasks. The calibration process may seem detailed, but this precision is what enables the unique computational capabilities of the system.

## Oscillator Calibration Procedure

The oscillator calibration process fine-tunes each oscillator to its target prime frequency (2 Hz, 3 Hz, and 5 Hz). This requires careful adjustment of the trim potentiometers and software-based calibration.

### Prerequisites

Before starting oscillator calibration, ensure:
- The system is fully assembled as described in Section 2
- The firmware is properly installed as described in Section 3
- The system has been powered on for at least 10 minutes for stabilization
- You have a reference frequency measurement device (optional but helpful)
  - Digital multimeter with frequency measurement
  - Oscilloscope
  - Smartphone with frequency counter app
- The ambient temperature is stable (ideally 20-25°C/68-77°F)

### Initial Frequency Verification

1. **Enter Calibration Mode**
   - Connect to the serial console at 115200 baud
   - Type `calibration mode enter`
   - The system will respond with "Entering calibration mode"
   - The display will show the calibration interface

2. **Check Current Frequencies**
   - Type `oscillator measure all`
   - Note the reported frequencies for all three oscillators
   - Expected values before calibration:
     - Oscillator 1: Approximately 2 Hz (±0.5 Hz)
     - Oscillator 2: Approximately 3 Hz (±0.5 Hz)
     - Oscillator 3: Approximately 5 Hz (±0.5 Hz)

3. **Enable External Measurement** (Optional but recommended)
   - Type `oscillator output enable`
   - This routes oscillator signals to designated GPIO pins for external measurement
   - Connect your measurement device to the test points or pins

### Detailed Calibration Procedure

#### Oscillator 1 (2 Hz)

1. **Select Oscillator**
   - Type `calibration select 1`
   - The system will indicate "Calibrating Oscillator 1"
   - The corresponding LED will begin flashing

2. **Start Continuous Measurement**
   - Type `oscillator measure continuous`
   - The system will display real-time frequency measurements
   - The display will also show this information

3. **Adjust Hardware**
   - Locate the trim potentiometer for Oscillator 1
   - Slowly turn the trim pot while observing the frequency readings
   - Aim to get as close as possible to 2.000 Hz
   - Note: Clock resolution means you'll see small fluctuations (±0.002 Hz)

4. **Apply Software Calibration**
   - Once you're close to 2.000 Hz through hardware adjustment:
   - Type `calibration fine 1 X.XXX` (replace X.XXX with the currently measured frequency)
   - This sets the base frequency for software adjustment
   - The system will calculate the necessary software trim value

5. **Verify Calibration**
   - Type `oscillator measure 1`
   - Verify the frequency is now 2.000 Hz (±0.002 Hz)
   - If not, repeat the software calibration step

6. **Save Calibration Value**
   - Type `calibration save 1`
   - The system will store the calibration value in non-volatile memory
   - Response: "Calibration for Oscillator 1 saved"

#### Oscillator 2 (3 Hz)

1. **Select Oscillator**
   - Type `calibration select 2`
   - The system will indicate "Calibrating Oscillator 2"
   - The corresponding LED will begin flashing

2. **Start Continuous Measurement**
   - Type `oscillator measure continuous` (if not already running)
   - Observe the frequency readings

3. **Adjust Hardware**
   - Locate the trim potentiometer for Oscillator 2
   - Slowly adjust while observing the frequency readings
   - Aim to get as close as possible to 3.000 Hz

4. **Apply Software Calibration**
   - Once you're close to 3.000 Hz:
   - Type `calibration fine 2 X.XXX` (with current frequency)
   - The system will calculate the trim value

5. **Verify Calibration**
   - Type `oscillator measure 2`
   - Verify the frequency is now 3.000 Hz (±0.003 Hz)
   - If not, repeat the software calibration step

6. **Save Calibration Value**
   - Type `calibration save 2`
   - The system will store the calibration value

#### Oscillator 3 (5 Hz)

1. **Select Oscillator**
   - Type `calibration select 3`
   - The system will indicate "Calibrating Oscillator 3"
   - The corresponding LED will begin flashing

2. **Start Continuous Measurement**
   - Type `oscillator measure continuous` (if not already running)
   - Observe the frequency readings

3. **Adjust Hardware**
   - Locate the trim potentiometer for Oscillator 3
   - Slowly adjust while observing the frequency
   - Aim to get as close as possible to 5.000 Hz

4. **Apply Software Calibration**
   - Once you're close to 5.000 Hz:
   - Type `calibration fine 3 X.XXX` (with current frequency)
   - The system will calculate the trim value

5. **Verify Calibration**
   - Type `oscillator measure 3`
   - Verify the frequency is now 5.000 Hz (±0.005 Hz)
   - If not, repeat the software calibration step

6. **Save Calibration Value**
   - Type `calibration save 3`
   - The system will store the calibration value

### Stability Verification

After calibrating all oscillators:

1. **Run Stability Test**
   - Type `oscillator stability test`
   - This will monitor all oscillators for 60 seconds
   - The system will report frequency variation statistics

2. **Check Results**
   - Look for these stability parameters:
     - Maximum deviation < ±0.01 Hz
     - Standard deviation < 0.005 Hz
     - Drift rate < 0.001 Hz/min
   - If any parameter exceeds these limits, repeat calibration for that oscillator

3. **Temperature Sensitivity Check**
   - Optionally, monitor frequency stability across temperatures:
   - Type `oscillator temptest`
   - This runs an extended test while measuring temperature effects
   - Results help determine how often recalibration may be needed

4. **Exit Frequency Calibration**
   - Type `oscillator output disable` to disable external measurement pins
   - Type `oscillator measure stop` to stop continuous measurement

## Phase Detector Calibration

With accurately calibrated oscillators, the next step is to calibrate the phase detectors to ensure precise phase measurement.

### Phase Detector Zero Offset Calibration

1. **Enter Phase Calibration Mode**
   - Type `calibration mode phase`
   - The system will enter phase detector calibration mode

2. **Run Automatic Zero Calibration**
   - Type `phase calibrate zero`
   - The system will:
     - Sync oscillators to a known phase relationship
     - Measure and store the baseline ADC readings
     - Calculate zero offset values for each phase detector
   - This process takes approximately 30 seconds

3. **Verify Zero Calibration**
   - Type `phase test zero`
   - The system will report the zero point accuracy
   - All detectors should read within ±2 degrees of 0° when inputs are in phase

### Phase Detector Scale Calibration

1. **Run Automatic Scale Calibration**
   - Type `phase calibrate scale`
   - The system will:
     - Generate specific phase differences between oscillators
     - Measure the ADC readings at these known differences
     - Calculate scaling factors for each detector
   - This process takes approximately 3 minutes

2. **Verify Scale Calibration**
   - Type `phase test scale`
   - The system will test phase readings at 90°, 180°, and 270°
   - All readings should be within ±3 degrees of expected values
   - The system will display the accuracy for each test point

### Phase Linearity Calibration

1. **Run Linearity Calibration**
   - Type `phase calibrate linearity`
   - The system will generate a sweep of phase differences
   - This creates a calibration curve for non-linear ADC response
   - This process takes approximately 5 minutes

2. **Verify Linearity Calibration**
   - Type `phase test linearity`
   - The system will test phase readings at multiple points
   - Linearity error should be less than ±5 degrees across the range
   - The system will display maximum error and RMS error

3. **Save Phase Calibration Data**
   - Type `phase calibrate save`
   - The system will store all phase calibration values to non-volatile memory
   - Confirmation: "Phase detector calibration saved successfully"

## System Parameter Configuration

With calibrated oscillators and phase detectors, the next step is to configure system parameters for optimal performance.

### Computational Parameters

1. **Access Computation Configuration**
   - Type `config compute`
   - The system will display current computational parameters

2. **Set Resonance Threshold**
   - Type `set compute_threshold 0.85`
   - This sets the threshold for prime detection (0.0-1.0)
   - Higher values increase certainty but may increase false negatives
   - Recommended range: 0.80-0.90

3. **Set Computational Intensity**
   - Type `set compute_intensity 2`
   - This sets the depth of analysis (1-5)
   - Higher values increase accuracy but take longer
   - Recommended settings:
     - 1: Quick tests, lower accuracy
     - 2: Good balance for most use cases
     - 3-5: Maximum accuracy for research

4. **Configure Entropy Weight**
   - Type `set entropy_weight 0.2`
   - This sets how strongly entropy source affects computation (0.0-1.0)
   - Higher values may increase detection of certain patterns
   - Recommended range: 0.1-0.3

5. **Set Analysis Iterations**
   - Type `set compute_iterations 3`
   - This sets how many times each computation is repeated
   - Higher values increase consistency but take longer
   - Recommended range: 3-7

6. **Save Computational Configuration**
   - Type `save`
   - The system will store the computational parameters

### Performance Optimization

1. **Run Auto-Optimization**
   - Type `optimize auto`
   - The system will:
     - Perform benchmark tests with various settings
     - Determine optimal parameters for your specific hardware
     - Suggest optimal values
   - This process takes approximately 5-10 minutes

2. **Review Suggested Parameters**
   - The system will display recommended settings
   - These will balance accuracy and performance for your hardware

3. **Apply Optimized Settings**
   - Type `optimize apply` to use the recommended settings
   - Or selectively apply individual parameters as needed

4. **Verify Optimized Performance**
   - Type `benchmark basic`
   - Compare the results to pre-optimization benchmarks
   - You should see improved performance or accuracy metrics

## Network Setup for Multi-Node Systems

For multi-node Prime Resonance Computer systems, proper network configuration is essential for coordinated operation.

### Network Role Configuration

1. **Designate Coordinator Node**
   - On the first node (Node 1):
     - Type `set network_role coordinator`
     - Type `save` to store this setting
   - The coordinator node orchestrates all network operations

2. **Configure Peer Nodes**
   - On each additional node (Nodes 2, 3, etc.):
     - Type `set network_role peer`
     - Type `save` to store this setting

### Network Pairing

1. **Obtain MAC Addresses**
   - On each node, type `network info`
   - Note the MAC address displayed for each node

2. **Configure Peer Connections on Coordinator**
   - On Node 1, add each peer:
     - Type `network add_peer XX:XX:XX:XX:XX:XX` for each peer node
     - Replace XX:XX:XX:XX:XX:XX with the actual MAC address
     - Example: `network add_peer 3C:71:BF:10:CE:A2`

3. **Configure Coordinator Connection on Peers**
   - On each peer node, add the coordinator:
     - Type `network add_peer XX:XX:XX:XX:XX:XX` with coordinator's MAC
     - Example: `network add_peer 3C:71:BF:10:CE:A1`

4. **Save Network Configuration**
   - On each node, type `save` to store network settings

### Network Testing and Optimization

1. **Verify Connectivity**
   - On the coordinator, type `network scan`
   - Verify all peers are discovered
   - Type `network ping all`
   - Verify all nodes respond with reasonable ping times

2. **Optimize Network Parameters**
   - If needed, adjust network power:
     - Type `set network_power 20` (range 0-20, higher for longer distances)
   - Adjust channel to avoid interference:
     - Type `set network_channel 6` (choose least congested channel)
   - Save settings with `save`

3. **Test Synchronization**
   - Type `network sync`
   - Verify all nodes synchronize correctly
   - Check synchronization quality with `network sync_status`

4. **Configure Network Operation Mode**
   - Type `set network_mode 2` to set operating mode:
     - 1: Independent operation with data sharing
     - 2: Synchronized computation (recommended)
     - 3: Distributed processing
   - Save settings with `save`

## Performance Validation Tests

After completing all calibration and configuration steps, run validation tests to verify system performance.

### Basic Functionality Tests

1. **Prime Verification Test**
   - Type `test prime_verification`
   - The system will test a set of known primes and composites
   - Expected accuracy: >98% correct classification
   - Note the detection confidence and processing time

2. **Factorization Test**
   - Type `test factorization`
   - The system will attempt to factorize several composite numbers
   - Expected success rate: >90% for numbers under 10,000
   - Note the processing time and complexity metrics

3. **Resonance Pattern Test**
   - Type `test resonance_patterns`
   - The system will test detection of specific resonance patterns
   - Verify pattern recognition accuracy meets expectations

### Accuracy Assessment

1. **Run Comprehensive Test Suite**
   - Type `test comprehensive`
   - This runs an extensive test of all computational functions
   - The test takes approximately 15-20 minutes
   - Results are stored for comparison with future calibrations

2. **Review Test Results**
   - The system will display detailed performance metrics:
     - Prime detection accuracy (false positive/negative rates)
     - Factorization success rate and speed
     - Resonance pattern recognition accuracy
     - Phase measurement precision
     - System stability under load

3. **Compare to Reference Performance**
   - Results are compared to baseline performance metrics
   - The system will display percentage differences
   - Most metrics should be within 95% of reference values

### Stress Testing

1. **Extended Stability Test**
   - Type `test stability_extended`
   - This runs an extended test for 30+ minutes
   - Monitors for frequency drift, temperature effects, and computation accuracy

2. **Heavy Load Test**
   - Type `test compute_load`
   - This performs intensive calculations to stress the system
   - Monitors power stability, thermal performance, and error rates

3. **Network Load Test** (for multi-node systems)
   - Type `test network_load`
   - This tests communication under heavy network traffic
   - Verifies reliable data transfer between nodes

## Creating and Saving Configuration Profiles

The Prime Resonance Computer supports multiple configuration profiles for different use cases or experiments.

### Creating Configuration Profiles

1. **Create a New Profile**
   - Type `profile create profile_name`
   - Replace "profile_name" with a descriptive name
   - Example: `profile create high_precision`

2. **Configure Profile Settings**
   - Adjust all relevant parameters as described earlier
   - Example:
     - `set compute_threshold 0.90`
     - `set compute_intensity 4`
     - `set entropy_weight 0.15`

3. **Save Profile**
   - Type `profile save`
   - Confirmation: "Profile saved: profile_name"

4. **Create Additional Profiles as Needed**
   - Repeat the process with different settings
   - Example profiles:
     - "high_speed" (optimized for computation speed)
     - "balanced" (balanced accuracy and speed)
     - "research" (maximum precision for research)
     - "demo" (fast response for demonstrations)

### Managing Configuration Profiles

1. **List Available Profiles**
   - Type `profile list`
   - The system will display all saved profiles

2. **View Profile Details**
   - Type `profile show profile_name`
   - This displays all settings in the selected profile

3. **Switch Between Profiles**
   - Type `profile load profile_name`
   - The system will load all settings from the profile
   - Example: `profile load high_precision`

4. **Delete Unused Profiles**
   - Type `profile delete profile_name`
   - Confirmation will be required

5. **Set Default Profile**
   - Type `profile set_default profile_name`
   - This profile will be loaded on system startup

### Exporting and Importing Profiles

1. **Export a Profile**
   - Type `profile export profile_name`
   - The profile will be saved to a JSON file in SPIFFS
   - Essential for backing up configurations

2. **Import a Profile**
   - Type `profile import filename.json`
   - The system will load settings from the file
   - This allows sharing configurations between devices

3. **Backup All Profiles**
   - Type `profile export_all`
   - All profiles will be saved to separate files
   - Essential before firmware updates

## Regular Calibration Schedule

For optimal performance, follow this calibration schedule:

| Component | Calibration Frequency | Indicators for Recalibration |
|-----------|---------------------|----------------------------|
| Oscillators | Monthly | Frequency drift > 0.01 Hz |
| Phase Detectors | Quarterly | Phase error > 5 degrees |
| System Parameters | As needed | Performance degradation > 5% |
| Network | After adding/removing nodes | Communication errors |

### Environmental Considerations

Be aware that these factors may necessitate more frequent calibration:

1. **Temperature Variations**
   - Large temperature changes (>10°C/18°F) can affect oscillator stability
   - Consider recalibration after significant ambient temperature changes

2. **Power Supply Changes**
   - Changes in power supply voltage or quality
   - Switching from USB to external power or vice versa

3. **Physical Disturbance**
   - Physical shocks or vibration to the system
   - Relocation of the system

4. **Component Aging**
   - Natural aging of electronic components
   - Most significant in the first 100 hours of operation

## Conclusion

Proper calibration and configuration are essential for the accurate operation of the Prime Resonance Computer. By following the procedures outlined in this section, you've ensured that:

1. Your oscillators are operating at precise prime frequencies
2. Phase detectors are providing accurate measurements
3. System parameters are optimized for your use case
4. Network nodes (if applicable) are properly synchronized
5. The system has been validated for computational accuracy

With calibration complete, your Prime Resonance Computer is ready for operation. The next section will cover day-to-day operation and usage of the system.

Keep a log of your calibration results and procedures. This will help track system performance over time and identify any patterns that might indicate component aging or other issues requiring attention.