# Prime Computer Visualization and Control Interface

## Overview

The Prime Computer Visualization and Control Interface provides a user-friendly way to interact with the prime resonator network, observe computation processes in real-time, and analyze results. This web-based interface connects to the prime computer network via WiFi and offers both monitoring capabilities and interactive control functions.

## System Architecture

The interface system consists of three main components:

1. **Backend Server**: Runs on the prime computer network's coordinator node
2. **Web Interface**: Accessible from any device on the same network
3. **API Layer**: Provides communication between the web interface and the prime computer network

```
+-----------------------------------+
|         Web Interface             |
|  (HTML5, CSS, JavaScript, D3.js)  |
+----------------+------------------+
                 |
                 v
+-----------------------------------+
|            API Layer              |
|      (WebSockets, REST API)       |
+----------------+------------------+
                 |
                 v
+-----------------------------------+
|         Backend Server            |
|       (Node.js or Python)         |
+----------------+------------------+
                 |
                 v
+-----------------------------------+
|      Prime Computer Network       |
+-----------------------------------+
```

## Backend Server Implementation

### Using Node.js

The backend server is implemented using Node.js for its event-driven architecture, which aligns well with the asynchronous nature of prime computer operations.

#### Server Setup

```javascript
// server.js - Main Backend Server

const express = require('express');
const http = require('http');
const WebSocket = require('ws');
const SerialPort = require('serialport');
const cors = require('cors');

// Create Express application
const app = express();
app.use(cors());
app.use(express.json());
app.use(express.static('public'));

// Create HTTP server
const server = http.createServer(app);

// Create WebSocket server
const wss = new WebSocket.Server({ server });

// Serial connection to coordinator node
let serialPort;
try {
  serialPort = new SerialPort('/dev/ttyUSB0', {
    baudRate: 115200,
  });
} catch (error) {
  console.error('Failed to open serial port:', error);
}

// Handle WebSocket connections
wss.on('connection', (ws) => {
  console.log('Client connected');
  
  // Send system status when client connects
  ws.send(JSON.stringify({
    type: 'systemStatus',
    data: getSystemStatus()
  }));
  
  // Handle messages from clients
  ws.on('message', (message) => {
    const msg = JSON.parse(message);
    
    switch (msg.type) {
      case 'command':
        handleCommand(msg.data);
        break;
      case 'configUpdate':
        updateConfiguration(msg.data);
        break;
      case 'requestData':
        sendRequestedData(ws, msg.data);
        break;
    }
  });
  
  ws.on('close', () => {
    console.log('Client disconnected');
  });
});

// Start the server
const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});

// Handle serial data from prime computer
serialPort.on('data', (data) => {
  const parsedData = parseSerialData(data);
  
  // Broadcast to all connected clients
  wss.clients.forEach((client) => {
    if (client.readyState === WebSocket.OPEN) {
      client.send(JSON.stringify({
        type: 'nodeData',
        data: parsedData
      }));
    }
  });
});

// Helper functions
function getSystemStatus() {
  // Get current system status
  return {
    nodes: [
      { id: 1, status: 'online', coherence: 0.85 },
      { id: 2, status: 'online', coherence: 0.72 },
      { id: 3, status: 'online', coherence: 0.91 }
    ],
    networkStatus: 'connected',
    computationStatus: 'idle'
  };
}

function handleCommand(command) {
  console.log('Executing command:', command);
  
  // Send command to prime computer coordinator
  const commandBuffer = prepareCommand(command);
  serialPort.write(commandBuffer);
}

function updateConfiguration(config) {
  console.log('Updating configuration:', config);
  
  // Convert config to appropriate format and send to coordinator
  const configBuffer = prepareConfig(config);
  serialPort.write(configBuffer);
}

function sendRequestedData(ws, request) {
  // Fetch requested data and send to client
  const data = fetchData(request);
  ws.send(JSON.stringify({
    type: 'requestedData',
    requestId: request.id,
    data: data
  }));
}

function parseSerialData(data) {
  // Parse binary data from serial port into structured format
  // Implementation depends on the prime computer's data protocol
  // ...
  
  return parsedData;
}

function prepareCommand(command) {
  // Convert command object to binary buffer for serial transmission
  // ...
  
  return commandBuffer;
}

function prepareConfig(config) {
  // Convert config object to binary buffer for serial transmission
  // ...
  
  return configBuffer;
}

function fetchData(request) {
  // Fetch specific data based on request
  // ...
  
  return responseData;
}
```

#### REST API Routes

```javascript
// api.js - REST API routes

// System status endpoint
app.get('/api/status', (req, res) => {
  res.json(getSystemStatus());
});

// Network topology endpoint
app.get('/api/network', (req, res) => {
  res.json(getNetworkTopology());
});

// Node configuration endpoint
app.get('/api/node/:id', (req, res) => {
  const nodeId = req.params.id;
  res.json(getNodeConfig(nodeId));
});

// Update node configuration
app.post('/api/node/:id/config', (req, res) => {
  const nodeId = req.params.id;
  const config = req.body;
  
  updateNodeConfig(nodeId, config);
  res.json({ success: true });
});

// Trigger computation
app.post('/api/compute', (req, res) => {
  const params = req.body;
  
  startComputation(params);
  res.json({ success: true, computationId: generateComputationId() });
});

// Get computation results
app.get('/api/results/:computationId', (req, res) => {
  const computationId = req.params.computationId;
  
  const results = getComputationResults(computationId);
  res.json(results);
});
```

### Alternative: Python Implementation

For those preferring Python, a Flask-based implementation can be used:

```python
# app.py - Flask-based backend server

from flask import Flask, request, jsonify, send_from_directory
from flask_socketio import SocketIO, emit
from flask_cors import CORS
import serial
import threading
import time
import json

app = Flask(__name__, static_folder='public')
CORS(app)
socketio = SocketIO(app, cors_allowed_origins="*")

# Serial connection to coordinator node
try:
    ser = serial.Serial('/dev/ttyUSB0', 115200)
except Exception as e:
    print(f"Failed to open serial port: {e}")
    ser = None

# Routes for serving the web interface
@app.route('/')
def index():
    return send_from_directory('public', 'index.html')

@app.route('/<path:path>')
def serve_static(path):
    return send_from_directory('public', path)

# REST API routes
@app.route('/api/status')
def get_status():
    return jsonify(get_system_status())

@app.route('/api/network')
def get_network():
    return jsonify(get_network_topology())

@app.route('/api/node/<node_id>')
def get_node(node_id):
    return jsonify(get_node_config(node_id))

@app.route('/api/node/<node_id>/config', methods=['POST'])
def update_node_config(node_id):
    config = request.json
    update_node_configuration(node_id, config)
    return jsonify({"success": True})

@app.route('/api/compute', methods=['POST'])
def start_compute():
    params = request.json
    computation_id = start_computation(params)
    return jsonify({"success": True, "computationId": computation_id})

@app.route('/api/results/<computation_id>')
def get_results(computation_id):
    results = get_computation_results(computation_id)
    return jsonify(results)

# WebSocket events
@socketio.on('connect')
def handle_connect():
    print("Client connected")
    emit('systemStatus', get_system_status())

@socketio.on('command')
def handle_command(data):
    print(f"Received command: {data}")
    send_command_to_coordinator(data)

@socketio.on('configUpdate')
def handle_config_update(data):
    print(f"Config update: {data}")
    update_configuration(data)

@socketio.on('requestData')
def handle_data_request(data):
    requested_data = fetch_data(data)
    emit('requestedData', {
        'requestId': data['id'],
        'data': requested_data
    })

@socketio.on('disconnect')
def handle_disconnect():
    print("Client disconnected")

# Function to read from serial port and broadcast to all clients
def serial_reader():
    if ser is None:
        return
        
    while True:
        if ser.in_waiting > 0:
            data = ser.readline().decode('utf-8').strip()
            try:
                parsed_data = json.loads(data)
                socketio.emit('nodeData', parsed_data)
            except json.JSONDecodeError:
                print(f"Error parsing JSON from serial: {data}")
        time.sleep(0.1)

# Start serial reader thread
if ser is not None:
    threading.Thread(target=serial_reader, daemon=True).start()

# Start the server
if __name__ == '__main__':
    socketio.run(app, host='0.0.0.0', port=3000)
```

## Web Interface Implementation

The web interface provides a visual representation of the prime computer's state and allows interaction through an intuitive dashboard.

### HTML Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Prime Computer Interface</title>
    <link rel="stylesheet" href="styles.css">
    <script src="https://d3js.org/d3.v7.min.js"></script>
</head>
<body>
    <header>
        <h1>Prime Resonator Computer</h1>
        <div class="system-status">
            <span id="connection-status">Connecting...</span>
        </div>
    </header>
    
    <main>
        <div class="dashboard">
            <div class="node-grid" id="node-grid">
                <!-- Node status cards will be inserted here dynamically -->
            </div>
            
            <div class="visualization-panel">
                <div class="tabs">
                    <button class="tab-btn active" data-tab="network">Network</button>
                    <button class="tab-btn" data-tab="phases">Phase Relations</button>
                    <button class="tab-btn" data-tab="resonance">Resonance Patterns</button>
                </div>
                
                <div class="tab-content active" id="network">
                    <!-- Network visualization will be inserted here -->
                    <div id="network-viz"></div>
                </div>
                
                <div class="tab-content" id="phases">
                    <!-- Phase visualization will be inserted here -->
                    <div id="phase-viz"></div>
                </div>
                
                <div class="tab-content" id="resonance">
                    <!-- Resonance pattern visualization will be inserted here -->
                    <div id="resonance-viz"></div>
                </div>
            </div>
            
            <div class="control-panel">
                <h3>Control Panel</h3>
                
                <div class="control-section">
                    <h4>Oscillator Control</h4>
                    <div class="oscillator-controls">
                        <!-- Oscillator control elements will be inserted here -->
                    </div>
                </div>
                
                <div class="control-section">
                    <h4>Entropy Injection</h4>
                    <div class="entropy-slider-container">
                        <label for="entropy-slider">Entropy Level:</label>
                        <input type="range" id="entropy-slider" min="0" max="100" value="0">
                        <span id="entropy-value">0</span>
                    </div>
                    <button id="inject-entropy">Inject Entropy</button>
                </div>
                
                <div class="control-section">
                    <h4>Computation</h4>
                    <select id="computation-type">
                        <option value="factorization">Prime Factorization</option>
                        <option value="gcd">Greatest Common Divisor</option>
                        <option value="primality">Primality Test</option>
                    </select>
                    <input type="number" id="computation-input" placeholder="Enter number">
                    <button id="start-computation">Compute</button>
                </div>
            </div>
            
            <div class="results-panel">
                <h3>Results</h3>
                <div id="computation-results">
                    <p>No computation results yet</p>
                </div>
            </div>
        </div>
    </main>
    
    <footer>
        <p>Prime Resonance Computer Interface v1.0</p>
    </footer>
    
    <script src="main.js"></script>
    <script src="visualizations.js"></script>
</body>
</html>
```

### JavaScript Implementation

```javascript
// main.js - Main interface functionality

// WebSocket connection
let ws;
let nodeData = [];
let networkTopology = {};

// Initialize application
document.addEventListener('DOMContentLoaded', () => {
    initializeWebSocket();
    setupEventListeners();
    setupTabNavigation();
});

// Initialize WebSocket connection
function initializeWebSocket() {
    const protocol = window.location.protocol === 'https:' ? 'wss://' : 'ws://';
    const host = window.location.hostname;
    const port = 3000; // Match the server port
    
    ws = new WebSocket(`${protocol}${host}:${port}`);
    
    ws.onopen = () => {
        document.getElementById('connection-status').textContent = 'Connected';
        document.getElementById('connection-status').className = 'connected';
    };
    
    ws.onclose = () => {
        document.getElementById('connection-status').textContent = 'Disconnected';
        document.getElementById('connection-status').className = 'disconnected';
        
        // Try to reconnect after 5 seconds
        setTimeout(initializeWebSocket, 5000);
    };
    
    ws.onerror = (error) => {
        console.error('WebSocket Error:', error);
    };
    
    ws.onmessage = (event) => {
        const message = JSON.parse(event.data);
        
        switch (message.type) {
            case 'systemStatus':
                updateSystemStatus(message.data);
                break;
            case 'nodeData':
                updateNodeData(message.data);
                break;
            case 'networkTopology':
                updateNetworkTopology(message.data);
                break;
            case 'computationResult':
                displayComputationResult(message.data);
                break;
            case 'requestedData':
                handleRequestedData(message.data);
                break;
        }
    };
}

// Set up event listeners for controls
function setupEventListeners() {
    // Entropy injection control
    const entropySlider = document.getElementById('entropy-slider');
    const entropyValue = document.getElementById('entropy-value');
    
    entropySlider.addEventListener('input', (e) => {
        entropyValue.textContent = e.target.value;
    });
    
    document.getElementById('inject-entropy').addEventListener('click', () => {
        const entropyLevel = parseInt(entropySlider.value);
        
        // Send entropy injection command
        ws.send(JSON.stringify({
            type: 'command',
            data: {
                command: 'injectEntropy',
                params: {
                    level: entropyLevel
                }
            }
        }));
    });
    
    // Computation control
    document.getElementById('start-computation').addEventListener('click', () => {
        const computationType = document.getElementById('computation-type').value;
        const inputValue = parseInt(document.getElementById('computation-input').value);
        
        if (isNaN(inputValue)) {
            alert('Please enter a valid number');
            return;
        }
        
        // Send computation command
        ws.send(JSON.stringify({
            type: 'command',
            data: {
                command: 'startComputation',
                params: {
                    type: computationType,
                    value: inputValue
                }
            }
        }));
        
        // Display loading indicator
        document.getElementById('computation-results').innerHTML = 
            '<p>Computing... <span class="spinner"></span></p>';
    });
}

// Update system status display
function updateSystemStatus(status) {
    // Update node grid with status information
    const nodeGrid = document.getElementById('node-grid');
    nodeGrid.innerHTML = '';
    
    status.nodes.forEach(node => {
        const nodeCard = document.createElement('div');
        nodeCard.className = `node-card ${node.status}`;
        nodeCard.innerHTML = `
            <h4>Node ${node.id}</h4>
            <div class="node-status">Status: ${node.status}</div>
            <div class="node-coherence">Coherence: ${node.coherence.toFixed(2)}</div>
            <button class="node-details-btn" data-node-id="${node.id}">Details</button>
        `;
        nodeGrid.appendChild(nodeCard);
    });
    
    // Add event listeners to node detail buttons
    document.querySelectorAll('.node-details-btn').forEach(btn => {
        btn.addEventListener('click', (e) => {
            const nodeId = e.target.dataset.nodeId;
            requestNodeDetails(nodeId);
        });
    });
}

// Request detailed node information
function requestNodeDetails(nodeId) {
    ws.send(JSON.stringify({
        type: 'requestData',
        data: {
            id: `node-${nodeId}`,
            dataType: 'nodeDetails',
            nodeId: nodeId
        }
    }));
}

// Update node data display
function updateNodeData(data) {
    // Update internal data store
    const existingNodeIndex = nodeData.findIndex(n => n.id === data.id);
    if (existingNodeIndex >= 0) {
        nodeData[existingNodeIndex] = data;
    } else {
        nodeData.push(data);
    }
    
    // Update visualizations
    updateNetworkVisualization();
    updatePhaseVisualization();
    updateResonanceVisualization();
}

// Update network topology display
function updateNetworkTopology(topology) {
    networkTopology = topology;
    updateNetworkVisualization();
}

// Display computation result
function displayComputationResult(result) {
    const resultsDiv = document.getElementById('computation-results');
    
    let resultHTML = '';
    
    switch (result.type) {
        case 'factorization':
            resultHTML = `
                <h4>Prime Factorization</h4>
                <p>Input: ${result.input}</p>
                <p>Factors: ${result.factors.join(' × ')}</p>
                <p>Computation time: ${result.time}ms</p>
            `;
            break;
        case 'gcd':
            resultHTML = `
                <h4>Greatest Common Divisor</h4>
                <p>Inputs: ${result.inputs.join(', ')}</p>
                <p>GCD: ${result.result}</p>
                <p>Computation time: ${result.time}ms</p>
            `;
            break;
        case 'primality':
            resultHTML = `
                <h4>Primality Test</h4>
                <p>Input: ${result.input}</p>
                <p>Result: ${result.isPrime ? 'Prime' : 'Not Prime'}</p>
                <p>Computation time: ${result.time}ms</p>
            `;
            break;
    }
    
    resultsDiv.innerHTML = resultHTML;
}

// Handle requested data
function handleRequestedData(data) {
    if (data.requestId.startsWith('node-')) {
        displayNodeDetails(data.data);
    }
}

// Display detailed node information
function displayNodeDetails(nodeDetails) {
    // Create modal dialog
    const modal = document.createElement('div');
    modal.className = 'modal';
    modal.innerHTML = `
        <div class="modal-content">
            <span class="close-btn">&times;</span>
            <h3>Node ${nodeDetails.id} Details</h3>
            
            <h4>Oscillator Status</h4>
            <ul>
                ${nodeDetails.oscillators.map(osc => `
                    <li>Oscillator ${osc.id}: ${osc.frequency}Hz (${osc.status})</li>
                `).join('')}
            </ul>
            
            <h4>Phase Relationships</h4>
            <table>
                <thead>
                    <tr>
                        <th>Oscillators</th>
                        <th>Phase Difference</th>
                        <th>Coherence</th>
                    </tr>
                </thead>
                <tbody>
                    ${nodeDetails.phaseRelationships.map(rel => `
                        <tr>
                            <td>${rel.from}-${rel.to}</td>
                            <td>${rel.phaseDiff.toFixed(2)} rad</td>
                            <td>${rel.coherence.toFixed(2)}</td>
                        </tr>
                    `).join('')}
                </tbody>
            </table>
            
            <h4>Hardware Info</h4>
            <p>CPU: ${nodeDetails.hardware.cpuLoad.toFixed(1)}%</p>
            <p>Memory: ${nodeDetails.hardware.memoryUsage.toFixed(1)}%</p>
            <p>Temperature: ${nodeDetails.hardware.temperature.toFixed(1)}°C</p>
            
            <div class="node-controls">
                <button id="restart-node">Restart Node</button>
                <button id="calibrate-node">Calibrate Oscillators</button>
            </div>
        </div>
    `;
    
    document.body.appendChild(modal);
    
    // Add event listener to close button
    modal.querySelector('.close-btn').addEventListener('click', () => {
        document.body.removeChild(modal);
    });
    
    // Add event listeners for node control buttons
    modal.querySelector('#restart-node').addEventListener('click', () => {
        sendNodeCommand(nodeDetails.id, 'restart');
        document.body.removeChild(modal);
    });
    
    modal.querySelector('#calibrate-node').addEventListener('click', () => {
        sendNodeCommand(nodeDetails.id, 'calibrate');
        document.body.removeChild(modal);
    });
}

// Send command to specific node
function sendNodeCommand(nodeId, command) {
    ws.send(JSON.stringify({
        type: 'command',
        data: {
            command: 'nodeControl',
            params: {
                nodeId: nodeId,
                action: command
            }
        }
    }));
}

// Set up tab navigation
function setupTabNavigation() {
    const tabButtons = document.querySelectorAll('.tab-btn');
    const tabContents = document.querySelectorAll('.tab-content');
    
    tabButtons.forEach(button => {
        button.addEventListener('click', () => {
            // Deactivate all tabs
            tabButtons.forEach(btn => btn.classList.remove('active'));
            tabContents.forEach(content => content.classList.remove('active'));
            
            // Activate selected tab
            button.classList.add('active');
            const tabId = button.dataset.tab;
            document.getElementById(tabId).classList.add('active');
            
            // Update the active visualization
            updateActiveVisualization(tabId);
        });
    });
}

// Update the currently active visualization
function updateActiveVisualization(tabId) {
    switch (tabId) {
        case 'network':
            updateNetworkVisualization();
            break;
        case 'phases':
            updatePhaseVisualization();
            break;
        case 'resonance':
            updateResonanceVisualization();
            break;
    }
}
```

### Visualizations Module

```javascript
// visualizations.js - D3.js based visualizations

// Network Visualization
function updateNetworkVisualization() {
    if (!networkTopology.nodes || !networkTopology.links) return;
    
    const width = document.getElementById('network-viz').clientWidth;
    const height = 400;
    
    // Clear existing visualization
    d3.select('#network-viz').html('');
    
    // Create SVG container
    const svg = d3.select('#network-viz')
        .append('svg')
        .attr('width', width)
        .attr('height', height);
    
    // Create force simulation
    const simulation = d3.forceSimulation(networkTopology.nodes)
        .force('link', d3.forceLink(networkTopology.links).id(d => d.id))
        .force('charge', d3.forceManyBody().strength(-100))
        .force('center', d3.forceCenter(width / 2, height / 2));
    
    // Draw links
    const links = svg.append('g')
        .selectAll('line')
        .data(networkTopology.links)
        .enter()
        .append('line')
        .attr('stroke', d => {
            // Color based on connection strength
            const strength = d.strength || 0.5;
            return d3.interpolateViridis(strength);
        })
        .attr('stroke-width', d => ((d.strength || 0.5) * 5) + 1);
    
    // Draw nodes
    const nodes = svg.append('g')
        .selectAll('circle')
        .data(networkTopology.nodes)
        .enter()
        .append('circle')
        .attr('r', 12)
        .attr('fill', d => {
            // Color based on node type or status
            switch (d.type) {
                case 'coordinator': return '#ff7700';
                case 'peripheral': return '#00aaff';
                default: return '#999999';
            }
        })
        .attr('stroke', '#ffffff')
        .attr('stroke-width', 2)
        .call(d3.drag()
            .on('start', dragstarted)
            .on('drag', dragged)
            .on('end', dragended));
    
    // Add node labels
    const nodeLabels = svg.append('g')
        .selectAll('text')
        .data(networkTopology.nodes)
        .enter()
        .append('text')
        .text(d => d.id)
        .attr('font-size', '10px')
        .attr('dx', 15)
        .attr('dy', 4)
        .attr('fill', '#333333');
    
    // Update positions on simulation tick
    simulation.on('tick', () => {
        links
            .attr('x1', d => d.source.x)
            .attr('y1', d => d.source.y)
            .attr('x2', d => d.target.x)
            .attr('y2', d => d.target.y);
        
        nodes
            .attr('cx', d => d.x)
            .attr('cy', d => d.y);
        
        nodeLabels
            .attr('x', d => d.x)
            .attr('y', d => d.y);
    });
    
    // Drag functions
    function dragstarted(event) {
        if (!event.active) simulation.alphaTarget(0.3).restart();
        event.subject.fx = event.subject.x;
        event.subject.fy = event.subject.y;
    }
    
    function dragged(event) {
        event.subject.fx = event.x;
        event.subject.fy = event.y;
    }
    
    function dragended(event) {
        if (!event.active) simulation.alphaTarget(0);
        event.subject.fx = null;
        event.subject.fy = null;
    }
}

// Phase Visualization
function updatePhaseVisualization() {
    if (!nodeData.length) return;
    
    const width = document.getElementById('phase-viz').clientWidth;
    const height = 400;
    const radius = Math.min(width, height) / 2 - 40;
    
    // Clear existing visualization
    d3.select('#phase-viz').html('');
    
    // Create SVG container
    const svg = d3.select('#phase-viz')
        .append('svg')
        .attr('width', width)
        .attr('height', height)
        .append('g')
        .attr('transform', `translate(${width / 2}, ${height / 2})`);
    
    // Draw phase circle
    svg.append('circle')
        .attr('r', radius)
        .attr('fill', 'none')
        .attr('stroke', '#cccccc')
        .attr('stroke-width', 2);
    
    // Draw tick marks for angles
    for (let i = 0; i < 12; i++) {
        const angle = (i * Math.PI * 2) / 12;
        const x1 = Math.cos(angle) * radius;
        const y1 = Math.sin(angle) * radius;
        const x2 = Math.cos(angle) * (radius + 10);
        const y2 = Math.sin(angle) * (radius + 10);
        
        svg.append('line')
            .attr('x1', x1)
            .attr('y1', y1)
            .attr('x2', x2)
            .attr('y2', y2)
            .attr('stroke', '#999999')
            .attr('stroke-width', 1);
        
        svg.append('text')
            .attr('x', Math.cos(angle) * (radius + 25))
            .attr('y', Math.sin(angle) * (radius + 25))
            .attr('text-anchor', 'middle')
            .attr('alignment-baseline', 'middle')
            .attr('font-size', '10px')
            .text(`${(i * 30)}°`);
    }
    
    // Get all oscillators from all nodes
    const oscillators = [];
    nodeData.forEach(node => {
        if (node.oscillators) {
            node.oscillators.forEach(osc => {
                oscillators.push({
                    nodeId: node.id,
                    oscillatorId: osc.id,
                    frequency: osc.frequency,
                    phase: osc.phase || 0,
                    power: osc.power || 1
                });
            });
        }
    });
    
    // Draw oscillator vectors
    oscillators.forEach(osc => {
        const x = Math.cos(osc.phase) * radius * osc.power;
        const y = Math.sin(osc.phase) * radius * osc.power;
        
        // Draw vector line
        svg.append('line')
            .attr('x1', 0)
            .attr('y1', 0)
            .attr('x2', x)
            .attr('y2', y)
            .attr('stroke', getColorForOscillator(osc.nodeId, osc.oscillatorId))
            .attr('stroke-width', 3);
        
        // Draw oscillator point
        svg.append('circle')
            .attr('cx', x)
            .attr('cy', y)
            .attr('r', 8)
            .attr('fill', getColorForOscillator(osc.nodeId, osc.oscillatorId));
        
        // Draw label
        svg.append('text')
            .attr('x', x * 1.1)
            .attr('y', y * 1.1)
            .attr('text-anchor', 'middle')
            .attr('alignment-baseline', 'middle')
            .attr('font-size', '10px')
            .text(`N${osc.nodeId}-O${osc.oscillatorId}`);
    });
    
    // Helper function for oscillator colors
    function getColorForOscillator(nodeId, oscId) {
        const colorScale = d3.scaleOrdinal(d3.schemeCategory10);
        return colorScale(`${nodeId}-${oscId}`);
    }
}

// Resonance Pattern Visualization
function updateResonanceVisualization() {
    if (!nodeData.length) return;
    
    const width = document.getElementById('resonance-viz').clientWidth;
    const height = 400;
    
    // Clear existing visualization
    d3.select('#resonance-viz').html('');
    
    // Create SVG container
    const svg = d3.select('#resonance-viz')
        .append('svg')
        .attr('width', width)
        .attr('height', height);
    
    // Collect all resonance patterns
    let allPatterns = [];
    nodeData.forEach(node => {
        if (node.resonancePattern) {
            allPatterns = allPatterns.concat(node.resonancePattern.map(p => ({
                ...p,
                nodeId: node.id
            })));
        }
    });
    
    // Sort patterns by strength
    allPatterns.sort((a, b) => b.strength - a.strength);
    
    // Calculate pattern positions
    const patternHeight = 30;
    const maxPatterns = Math.floor(height / patternHeight);
    const patterns = allPatterns.slice(0, maxPatterns);
    
    // Draw patterns
    patterns.forEach((pattern, index) => {
        const y = index * patternHeight + 20;
        
        // Draw pattern label
        svg.append('text')
            .attr('x', 10)
            .attr('y', y)
            .attr('alignment-baseline', 'middle')
            .attr('font-size', '12px')
            .text(`Node ${pattern.nodeId} - ${pattern.name}`);
        
        // Draw strength bar
        const barWidth = (width - 200) * pattern.strength;
        
        svg.append('rect')
            .attr('x', 180)
            .attr('y', y - 10)
            .attr('width', barWidth)
            .attr('height', 20)
            .attr('fill', d3.interpolateViridis(pattern.strength));
        
        // Draw strength text
        svg.append('text')
            .attr('x', 185 + barWidth)
            .attr('y', y)
            .attr('alignment-baseline', 'middle')
            .attr('font-size', '12px')
            .text(`${(pattern.strength * 100).toFixed(1)}%`);
    });
}
```

### CSS Styling

```css
/* styles.css - Interface styling */

/* Basic styles */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Roboto', Arial, sans-serif;
}

body {
    background-color: #f5f5f5;
    color: #333;
    line-height: 1.6;
}

header {
    background-color: #1a1a2e;
    color: white;
    padding: 1rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

main {
    padding: 1rem;
}

footer {
    text-align: center;
    padding: 1rem;
    background-color: #1a1a2e;
    color: white;
    font-size: 0.8rem;
}

/* Status indicators */
.system-status {
    padding: 0.5rem;
    border-radius: 4px;
}

#connection-status {
    padding: 0.25rem 0.5rem;
    border-radius: 4px;
}

#connection-status.connected {
    background-color: #4caf50;
}

#connection-status.disconnected {
    background-color: #f44336;
}

/* Dashboard components */
.dashboard {
    display: grid;
    grid-template-columns: 200px 1fr;
    grid-template-rows: auto 1fr auto;
    grid-template-areas: 
        "nodes visualization"
        "control visualization"
        "results results";
    gap: 1rem;
    height: calc(100vh - 120px);
}

.node-grid {
    grid-area: nodes;
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
    overflow-y: auto;
}

.visualization-panel {
    grid-area: visualization;
    background-color: white;
    border-radius: 4px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 1rem;
    display: flex;
    flex-direction: column;
}

.control-panel {
    grid-area: control;
    background-color: white;
    border-radius: 4px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 1rem;
}

.results-panel {
    grid-area: results;
    background-color: white;
    border-radius: 4px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 1rem;
    max-height: 200px;
    overflow-y: auto;
}

/* Node cards */
.node-card {
    background-color: white;
    border-radius: 4px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 0.5rem;
    border-left: 5px solid #999;
}

.node-card.online {
    border-left-color: #4caf50;
}

.node-card.offline {
    border-left-color: #f44336;
}

.node-card.warning {
    border-left-color: #ff9800;
}

.node-card h4 {
    margin-bottom: 0.25rem;
}

.node-coherence {
    font-weight: bold;
    margin: 0.25rem 0;
}

.node-details-btn {
    background-color: #2196f3;
    color: white;
    border: none;
    border-radius: 4px;
    padding: 0.25rem 0.5rem;
    cursor: pointer;
    font-size: 0.8rem;
    width: 100%;
    margin-top: 0.5rem;
}

.node-details-btn:hover {
    background-color: #0b7dda;
}

/* Tab navigation */
.tabs {
    display: flex;
    border-bottom: 1px solid #ddd;
    margin-bottom: 1rem;
}

.tab-btn {
    background-color: transparent;
    border: none;
    padding: 0.5rem 1rem;
    cursor: pointer;
    font-size: 1rem;
    position: relative;
}

.tab-btn.active {
    font-weight: bold;
}

.tab-btn.active::after {
    content: '';
    position: absolute;
    bottom: -1px;
    left: 0;
    right: 0;
    height: 3px;
    background-color: #2196f3;
}

.tab-content {
    display: none;
    height: 100%;
}

.tab-content.active {
    display: block;
}

/* Visualization containers */
#network-viz,
#phase-viz,
#resonance-viz {
    width: 100%;
    height: 100%;
    min-height: 300px;
}

/* Control elements */
.control-section {
    margin-bottom: 1rem;
    padding-bottom: 1rem;
    border-bottom: 1px solid #eee;
}

.control-section:last-child {
    border-bottom: none;
    margin-bottom: 0;
    padding-bottom: 0;
}

.control-section h4 {
    margin-bottom: 0.5rem;
}

.oscillator-controls {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
}

.entropy-slider-container {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    margin-bottom: 0.5rem;
}

#entropy-slider {
    flex-grow: 1;
}

button {
    background-color: #2196f3;
    color: white;
    border: none;
    border-radius: 4px;
    padding: 0.5rem 1rem;
    cursor: pointer;
    font-size: 0.9rem;
}

button:hover {
    background-color: #0b7dda;
}

#computation-type {
    width: 100%;
    padding: 0.5rem;
    margin-bottom: 0.5rem;
}

#computation-input {
    width: 100%;
    padding: 0.5rem;
    margin-bottom: 0.5rem;
}

#start-computation {
    width: 100%;
}

/* Modal dialog */
.modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000;
}

.modal-content {
    background-color: white;
    border-radius: 4px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    padding: 2rem;
    width: 80%;
    max-width: 600px;
    position: relative;
}

.close-btn {
    position: absolute;
    top: 1rem;
    right: 1rem;
    font-size: 1.5rem;
    cursor: pointer;
}

.node-controls {
    display: flex;
    gap: 1rem;
    margin-top: 1rem;
}

/* Tables */
table {
    width: 100%;
    border-collapse: collapse;
    margin: 1rem 0;
}

th, td {
    padding: 0.5rem;
    text-align: left;
    border-bottom: 1px solid #ddd;
}

th {
    background-color: #f5f5f5;
}

/* Loading spinner */
.spinner {
    display: inline-block;
    width: 1em;
    height: 1em;
    border: 2px solid rgba(0, 0, 0, 0.1);
    border-left-color: #2196f3;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    to { transform: rotate(360deg); }
}
```

## Setup and Configuration

### Installation on Coordinator Node

The coordinator node (typically Node #1) hosts the web interface and serves as the gateway between the user and the prime computer network.

1. **Install required software**:
   ```bash
   # If using Node.js
   npm install express ws serialport cors
   
   # If using Python
   pip install flask flask-socketio flask-cors pyserial
   ```

2. **Copy web interface files**:
   ```bash
   mkdir -p public
   cp index.html public/
   cp styles.css public/
   cp main.js public/
   cp visualizations.js public/
   ```

3. **Configure serial connection**:
   Edit the serial port in the server file to match your system:
   ```javascript
   // For Node.js
   const serialPort = new SerialPort('/dev/ttyUSB0', {
     baudRate: 115200,
   });
   ```
   or
   ```python
   # For Python
   ser = serial.Serial('/dev/ttyUSB0', 115200)
   ```

4. **Start the server**:
   ```bash
   # For Node.js
   node server.js
   
   # For Python
   python app.py
   ```

### Accessing the Interface

1. **From local network**:
   Open a web browser and navigate to:
   ```
   http://<coordinator-ip>:3000
   ```

2. **From coordinator node itself**:
   Open a web browser and navigate to:
   ```
   http://localhost:3000
   ```

## Customizing the Interface

### Adding New Visualizations

To add new visualization types:

1. Add a new tab button in `index.html`:
   ```html
   <button class="tab-btn" data-tab="newViz">New Visualization</button>
   ```

2. Add a new tab content div:
   ```html
   <div class="tab-content" id="newViz">
       <div id="new-viz"></div>
   </div>
   ```

3. Add a new visualization function in `visualizations.js`:
   ```javascript
   function updateNewVisualization() {
       // Implementation using D3.js
   }
   ```

4. Update the tab navigation handler in `main.js`:
   ```javascript
   // In updateActiveVisualization function
   case 'newViz':
       updateNewVisualization();
       break;
   ```

### Adding Custom Controls

To add new control features:

1. Add HTML elements to the control panel:
   ```html
   <div class="control-section">
       <h4>New Control</h4>
       <input type="range" id="new-control" min="0" max="100" value="50">
       <button id="apply-new-control">Apply</button>
   </div>
   ```

2. Add event listeners in `main.js`:
   ```javascript
   document.getElementById('apply-new-control').addEventListener('click', () => {
       const value = document.getElementById('new-control').value;
       
       ws.send(JSON.stringify({
           type: 'command',
           data: {
               command: 'newControl',
               params: {
                   value: parseInt(value)
               }
           }
       }));
   });
   ```

### Connecting to Multiple Prime Computer Networks

For research setups with multiple prime computer networks:

1. Add network selector to the interface:
   ```html
   <select id="network-selector">
       <option value="network1">Network 1</option>
       <option value="network2">Network 2</option>
   </select>
   ```

2. Add event handler for network switching:
   ```javascript
   document.getElementById('network-selector').addEventListener('change', (e) => {
       const networkId = e.target.value;
       switchToNetwork(networkId);
   });
   
   function switchToNetwork(networkId) {
       if (ws) {
           ws.close();
       }
       
       // Connect to selected network's coordinator
       initializeWebSocket(networkId);
       
       // Update interface to show selected network
       document.getElementById('current-network').textContent = networkId;
   }
   ```

3. Modify server to support multiple networks.