# Prime Computer Application Interface

## Overview

This document details the application programming interface (API) for interacting with the Prime Resonance Computer. It provides comprehensive information on developing applications that leverage the computational capabilities of the prime resonance framework, including interface methods, data structures, communication protocols, and example usage patterns.

## Table of Contents

1. [Introduction](#introduction)
2. [Communication Methods](#communication-methods)
3. [API Structure](#api-structure)
4. [Command Reference](#command-reference)
5. [Data Formats](#data-formats)
6. [Application Development](#application-development)
7. [Example Applications](#example-applications)
8. [Performance Considerations](#performance-considerations)
9. [Security Guidelines](#security-guidelines)
10. [Future API Extensions](#future-api-extensions)

## Introduction

The Prime Computer Application Interface provides multiple ways to interact with the Prime Resonance Computer system:

1. **Serial Interface:** Direct communication via USB/serial connection
2. **Network API:** Remote access via WiFi or Ethernet
3. **Native Libraries:** Integration into custom applications
4. **Web Interface:** Browser-based control and visualization

Each interface method provides access to the core functionality of the Prime Resonance Computer, including:

- Controlling and monitoring oscillator states
- Initiating and retrieving computational results
- Managing network configurations
- Accessing system diagnostics
- Visualizing resonance patterns
- Programming custom resonance algorithms

## Communication Methods

### Serial Interface

The most direct method of communication with a Prime Computer node:

- **Connection:** USB to Serial (CP2102 or similar)
- **Baud Rate:** 115200
- **Data Format:** 8N1 (8 data bits, no parity, 1 stop bit)
- **Flow Control:** None
- **Command Format:** ASCII text commands with JSON responses

Example serial connection with Python:

```python
import serial

# Connect to Prime Computer
ser = serial.Serial('/dev/ttyUSB0', 115200, timeout=1)

# Send a command
ser.write(b'STATUS\r\n')

# Read response
response = ser.readline().decode('utf-8')
print(response)
```

### Network API

For remote access and multi-node control:

- **Protocol:** HTTP REST API
- **Default Port:** 8080
- **Authentication:** API key or JWT token
- **Data Format:** JSON
- **Connection:** TCP/IP over WiFi or Ethernet

Example HTTP request with Python:

```python
import requests

# Define API endpoint
api_url = 'http://prime-node-1.local:8080/api/v1'
headers = {'Authorization': 'Bearer YOUR_API_KEY'}

# Get system status
response = requests.get(f'{api_url}/status', headers=headers)
status_data = response.json()
print(status_data)
```

### Native Libraries

For deep integration with custom applications:

- **Languages:** C++, Python, JavaScript
- **Dependency Management:** pip, npm, conan
- **Usage Pattern:** Object-oriented API

Example Python library usage:

```python
from prime_resonance import PrimeComputer

# Connect to Prime Computer
pc = PrimeComputer('192.168.1.100')
pc.connect()

# Start computation with specific seed
result = pc.compute(seed=42, timeout=30)
print(f"Computation result: {result}")
```

### Web Interface

Browser-based control dashboard:

- **Access URL:** http://[node-ip-address]/
- **Compatibility:** Modern browsers with JavaScript support
- **Authentication:** Username/password
- **WebSocket:** Real-time updates

## API Structure

The Prime Computer API is organized into several functional groups:

### System APIs

Core system functionality:

- Status and health monitoring
- Configuration management
- Firmware updates
- Diagnostic tools

### Oscillator APIs

Control and monitoring of the physical oscillators:

- Frequency measurement
- Phase detection
- Calibration
- Entropy injection

### Computation APIs

Prime resonance computational functions:

- Computation initialization
- Parameter settings
- Result retrieval
- Computational history

### Network APIs

Node-to-node communication:

- Peer discovery
- Synchronization
- Distributed computation
- Data sharing

### Visualization APIs

Data visualization capabilities:

- Real-time oscillator monitoring
- Phase relationship graphs
- Resonance pattern display
- Network topology visualization

## Command Reference

### System Commands

| Command | Description | Parameters | Response |
|---------|-------------|------------|----------|
| `STATUS` | Get system status | None | JSON with system state |
| `CONFIG GET [key]` | Retrieve configuration | key (optional) | JSON with configuration |
| `CONFIG SET [key] [value]` | Update configuration | key, value | Status success/failure |
| `RESET` | Restart the system | None | Confirmation message |
| `UPDATE` | Update firmware | None | Progress messages |
| `DIAG` | Run diagnostics | None | Diagnostic results |

Example:
```
> STATUS
{
  "nodeId": 1,
  "name": "Prime Node 1",
  "uptime": 3642,
  "firmware": "v1.0.2",
  "oscillators": {
    "osc1": { "frequency": 2.001, "status": "stable" },
    "osc2": { "frequency": 3.003, "status": "stable" },
    "osc3": { "frequency": 5.002, "status": "stable" }
  },
  "network": {
    "connected": true,
    "peers": 2,
    "role": "coordinator"
  }
}
```

### Oscillator Commands

| Command | Description | Parameters | Response |
|---------|-------------|------------|----------|
| `OSC GET [id]` | Get oscillator data | id (1-3) | JSON with oscillator state |
| `OSC FREQ [id]` | Get oscillator frequency | id (1-3) | Frequency in Hz |
| `OSC PHASE [id1] [id2]` | Get phase difference | id1, id2 (1-3) | Phase difference (0-1) |
| `OSC CALIBRATE [id]` | Calibrate oscillator | id (1-3) | Calibration results |
| `OSC TRIM [id] [value]` | Adjust oscillator trim | id, value | Status success/failure |

Example:
```
> OSC GET 1
{
  "id": 1,
  "frequency": 2.001,
  "pulseCount": 62438,
  "stability": 0.998,
  "duty": 0.501,
  "calibration": {
    "trim": 0.02,
    "lastCalibration": "2025-04-12T15:30:22Z"
  }
}
```

### Computation Commands

| Command | Description | Parameters | Response |
|---------|-------------|------------|----------|
| `COMPUTE START [seed]` | Start computation | seed (optional) | Confirmation message |
| `COMPUTE STATUS` | Get computation status | None | JSON with status |
| `COMPUTE STOP` | Abort computation | None | Status success/failure |
| `COMPUTE RESULT [id]` | Get computation result | id (optional) | JSON with result |
| `COMPUTE HISTORY` | List recent computations | None | JSON with history |

Example:
```
> COMPUTE START 42
{
  "status": "started",
  "computeId": "ad7f3e",
  "seed": 42,
  "timestamp": "2025-05-09T16:28:13Z"
}

> COMPUTE STATUS
{
  "computeId": "ad7f3e",
  "status": "running",
  "progress": 0.45,
  "elapsed": 12.3,
  "estimatedCompletion": 15.1
}
```

### Network Commands

| Command | Description | Parameters | Response |
|---------|-------------|------------|----------|
| `NET STATUS` | Network status | None | JSON with network state |
| `NET SCAN` | Scan for peers | None | JSON with discovered peers |
| `NET CONNECT [id]` | Connect to peer | node id | Status success/failure |
| `NET PING [id]` | Test connection | node id | Ping time in ms |
| `NET SYNC` | Synchronize with peers | None | Synchronization results |

Example:
```
> NET STATUS
{
  "state": "connected",
  "role": "coordinator",
  "peers": [
    {
      "id": 2,
      "name": "Prime Node 2",
      "rssi": -65,
      "latency": 12.3,
      "lastSeen": 1.2
    },
    {
      "id": 3,
      "name": "Prime Node 3",
      "rssi": -72,
      "latency": 18.7,
      "lastSeen": 0.8
    }
  ]
}
```

### Visualization Commands

| Command | Description | Parameters | Response |
|---------|-------------|------------|----------|
| `VIZ OSC [format]` | Oscillator visualization | format (json/svg/bin) | Visualization data |
| `VIZ PHASE [format]` | Phase visualization | format (json/svg/bin) | Visualization data |
| `VIZ NET [format]` | Network visualization | format (json/svg/bin) | Visualization data |
| `VIZ STREAM [type] [on/off]` | Stream visualization | type, state | Status success/failure |

Example:
```
> VIZ OSC json
{
  "timestamp": 1620567123.45,
  "oscillators": [
    {
      "id": 1,
      "frequency": 2.001,
      "samples": [0, 1, 0, 1, 0, 1, ...]
    },
    {
      "id": 2,
      "frequency": 3.003,
      "samples": [0, 0, 1, 0, 0, 1, ...]
    },
    {
      "id": 3,
      "frequency": 5.002,
      "samples": [0, 0, 0, 0, 1, 0, ...]
    }
  ]
}
```

## Data Formats

### JSON Schema

All API responses use JSON with the following general structure:

```json
{
  "status": "success",
  "timestamp": 1620567123.45,
  "data": {
    // Response-specific data here
  }
}
```

Error responses use this structure:

```json
{
  "status": "error",
  "timestamp": 1620567123.45,
  "error": {
    "code": 400,
    "message": "Invalid parameter value",
    "details": "Parameter 'seed' must be an integer"
  }
}
```

### Binary Data

For efficient transmission of time-series data, binary formats are available:

- **Oscillator Samples:** 
  - 8-bit unsigned values (0-255)
  - 1024 samples per stream
  - First 16 bytes as header with metadata

- **Phase Data:**
  - 16-bit unsigned values (0-65535)
  - Normalized to 0-1 phase difference
  - 512 samples per stream

### WebSocket Streaming

Real-time data is available via WebSocket connections:

- **Connection URL:** ws://[node-ip-address]/ws
- **Authentication:** Same as REST API
- **Message Format:** JSON or binary, depending on subscription

Example WebSocket subscription:

```javascript
const ws = new WebSocket('ws://prime-node-1.local:8080/ws');

ws.onopen = () => {
  // Subscribe to oscillator data at 10Hz
  ws.send(JSON.stringify({
    action: 'subscribe',
    topic: 'oscillators',
    frequency: 10,
    format: 'json'
  }));
};

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Oscillator update:', data);
};
```

## Application Development

### Development Environment Setup

To develop applications for the Prime Computer, set up your environment:

1. **Install required tools:**
   - Python 3.8+ or Node.js 14+
   - Serial communication libraries
   - HTTP client libraries

2. **Install the Prime Computer SDK:**
   - Python: `pip install prime-computer-sdk`
   - JavaScript: `npm install prime-computer-sdk`
   - C++: Available via GitHub

3. **Configure development settings:**
   - Set API key or credentials
   - Configure connection parameters
   - Set up logging and debugging

### SDK Installation

Python SDK installation:

```bash
pip install prime-computer-sdk
```

Node.js SDK installation:

```bash
npm install prime-computer-sdk
```

C++ SDK installation:

```bash
git clone https://github.com/prime-resonance/prime-computer-cpp.git
cd prime-computer-cpp
mkdir build && cd build
cmake ..
make install
```

### Basic Application Template

Python application template:

```python
from prime_computer import PrimeComputer, ComputeMode

def main():
    # Connect to Prime Computer
    pc = PrimeComputer('192.168.1.100')
    
    try:
        # Connect and verify status
        pc.connect()
        status = pc.get_status()
        print(f"Connected to {status['name']} (Node {status['nodeId']})")
        
        # Configure computation parameters
        pc.set_computation_mode(ComputeMode.STANDARD)
        pc.set_entropy_weight(0.2)
        
        # Start computation with seed value
        result = pc.compute(seed=42, timeout=60)
        print(f"Computation result: {result}")
        
        # Visualize the oscillator states
        pc.save_visualization('oscillators.png')
        
    except Exception as e:
        print(f"Error: {e}")
    finally:
        pc.disconnect()

if __name__ == "__main__":
    main()
```

JavaScript application template:

```javascript
const { PrimeComputer, ComputeMode } = require('prime-computer-sdk');

async function main() {
  // Connect to Prime Computer
  const pc = new PrimeComputer('192.168.1.100');
  
  try {
    // Connect and verify status
    await pc.connect();
    const status = await pc.getStatus();
    console.log(`Connected to ${status.name} (Node ${status.nodeId})`);
    
    // Configure computation parameters
    pc.setComputationMode(ComputeMode.STANDARD);
    pc.setEntropyWeight(0.2);
    
    // Start computation with seed value
    const result = await pc.compute({ seed: 42, timeout: 60 });
    console.log(`Computation result: ${result}`);
    
    // Visualize the oscillator states
    await pc.saveVisualization('oscillators.png');
    
  } catch (error) {
    console.error(`Error: ${error.message}`);
  } finally {
    pc.disconnect();
  }
}

main();
```

## Example Applications

### Prime Number Generator

This example generates prime numbers using the resonance state of the system:

```python
from prime_computer import PrimeComputer

def generate_primes(count, start_seed=1000):
    pc = PrimeComputer('192.168.1.100')
    pc.connect()
    
    primes = []
    seed = start_seed
    
    while len(primes) < count:
        # Use the previous prime as seed for next computation
        result = pc.compute(seed=seed, timeout=30)
        
        # Verify primality
        if is_prime(result):
            primes.append(result)
            print(f"Found prime: {result}")
        
        seed = result + 1
    
    pc.disconnect()
    return primes

def is_prime(n):
    if n < 2:
        return False
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
            return False
    return True

# Generate 10 primes
prime_list = generate_primes(10)
print(f"Generated primes: {prime_list}")
```

### Real-time Oscillator Monitor

This example creates a real-time visualization of oscillator states:

```python
import time
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation
from prime_computer import PrimeComputer

# Connect to Prime Computer
pc = PrimeComputer('192.168.1.100')
pc.connect()

# Setup the plot
fig, ax = plt.subplots(3, 1, figsize=(10, 8))
lines = [ax[i].plot([], [])[0] for i in range(3)]
data_buffer = [[] for _ in range(3)]
max_points = 500

# Initialize plot properties
for i, axis in enumerate(ax):
    axis.set_ylim(-0.1, 1.1)
    axis.set_xlim(0, max_points)
    axis.set_title(f"Oscillator {i+1}")
    axis.grid(True)

# Animation update function
def update(frame):
    # Get latest oscillator data
    osc_data = pc.get_oscillator_samples(50)  # Get 50 samples per oscillator
    
    # Update data buffers
    for i in range(3):
        data_buffer[i].extend(osc_data[i])
        if len(data_buffer[i]) > max_points:
            data_buffer[i] = data_buffer[i][-max_points:]
        
        # Update line data
        lines[i].set_data(range(len(data_buffer[i])), data_buffer[i])
    
    return lines

# Create animation
ani = FuncAnimation(fig, update, frames=None, interval=50, blit=True)
plt.tight_layout()
plt.show()

# Cleanup
pc.disconnect()
```

### Distributed Computing Cluster

This example creates a distributed computing cluster using multiple Prime Computer nodes:

```python
from prime_computer import PrimeCluster, ComputationTask

def run_distributed_task(nodes, iterations=100):
    # Create cluster from node addresses
    cluster = PrimeCluster(nodes)
    
    # Connect to all nodes
    connected_nodes = cluster.connect_all()
    print(f"Connected to {len(connected_nodes)} nodes")
    
    # Create a distributed computation task
    task = ComputationTask(
        name="DistributedPrimeSearch",
        iterations=iterations,
        entropy_weight=0.3,
        sync_interval=5  # Synchronize every 5 iterations
    )
    
    # Start the task on the cluster
    result = cluster.execute(task)
    
    # Process results from all nodes
    combined_result = cluster.aggregate_results()
    
    # Disconnect from all nodes
    cluster.disconnect_all()
    
    return combined_result

# Define list of node addresses
node_addresses = [
    '192.168.1.100',  # Coordinator node
    '192.168.1.101',  # Node 2
    '192.168.1.102'   # Node 3
]

# Run distributed task
result = run_distributed_task(node_addresses, iterations=100)
print(f"Distributed computation result: {result}")
```

## Performance Considerations

### Computation Time

Factors affecting computation performance:

1. **Oscillator Stability:**
   - More stable oscillators produce more consistent results
   - Allow 5+ minutes of warm-up time for best stability

2. **Network Synchronization:**
   - Network latency impacts multi-node synchronization
   - Keep nodes within reliable WiFi range
   - Consider wired connections for critical applications

3. **Computational Load:**
   - Standard mode: ~10 operations per second
   - High-performance mode: ~50 operations per second
   - Batch processing: ~5,000 operations per minute

4. **Memory Usage:**
   - Each computation requires ~2KB of state data
   - Maximum simultaneous computations: ~100
   - Stream data buffer: 32KB per oscillator

### Optimizing Performance

Tips for maximizing computational throughput:

1. **Computation Batching:**
   - Group related computations into batches
   - Avoid rapid start/stop cycles
   - Use the batch API for multiple operations

2. **Reduced Polling:**
   - Use WebSocket streams instead of polling
   - Subscribe only to needed data
   - Use appropriate sampling rates

3. **Connection Management:**
   - Maintain persistent connections
   - Implement proper error handling
   - Use connection pooling for multiple clients

4. **Data Compression:**
   - Use binary formats for large datasets
   - Enable gzip compression for HTTP
   - Minimize unnecessary metadata

## Security Guidelines

### Authentication

Secure your connections with proper authentication:

1. **API Key Authentication:**
   - Generate secure API keys through the web interface
   - Use environment variables to store keys
   - Rotate keys periodically

2. **JWT Authentication:**
   - Available for web and HTTP API access
   - Configurable token expiration
   - Role-based access control

3. **Local Authentication:**
   - USB/Serial connections require physical access
   - Optional PIN code for serial console

### Network Security

Protect your Prime Computer network:

1. **Isolated Network:**
   - Create dedicated WiFi network for nodes
   - Use non-default SSID
   - Enable WPA2/WPA3 security

2. **Firewall Configuration:**
   - Restrict API access to trusted IPs
   - Block unnecessary inbound ports
   - Configure proper egress filtering

3. **HTTPS Configuration:**
   - Enable HTTPS for web interface
   - Use proper certificates
   - Configure secure TLS settings

### Data Protection

Safeguard your computation data:

1. **Encryption:**
   - All API communications use TLS
   - Local storage uses AES-256
   - WebSocket streams are encrypted

2. **Access Controls:**
   - User account management
   - Role-based permissions
   - Audit logging for all access

## Future API Extensions

The Prime Computer API roadmap includes:

### Planned Enhancements (2025-2026)

1. **Extended Computation Models:**
   - Multi-prime resonance patterns
   - Nonlinear resonance modes
   - Quantum-inspired algorithms

2. **Advanced Visualization:**
   - 3D phase space visualization
   - Real-time resonance mapping
   - AR/VR integration

3. **Cloud Integration:**
   - Secure cloud backup
   - Result sharing platform
   - Online computation monitoring

### Experimental Features

1. **Machine Learning Integration:**
   - Resonance pattern recognition
   - Predictive oscillator monitoring
   - Adaptive computation steering

2. **External Data Sources:**
   - Environmental sensors for entropy
   - Integration with physical phenomena
   - Natural resonance detection

### API Versioning

The API uses semantic versioning:

- **Current stable API:** v1.0
- **Development API:** v1.1-beta
- **Legacy support:** Maintained for 24 months

API versioning is specified in HTTP headers or URL paths, e.g.:
- `https://[node-ip]/api/v1/status`
- `https://[node-ip]/api/v1.1-beta/extended-status`