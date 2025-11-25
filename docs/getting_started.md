# Getting Started with Qumat

Welcome to Qumat! This guide will help you get up and running with Qumat, a high-level Python library for interfacing with multiple quantum computing backends.

## What is Qumat?

Qumat provides a unified interface for quantum circuit operations across multiple backends (Qiskit, Cirq, and Amazon Braket). Write your quantum circuits once and run them on any supported backend - it's designed to be the "write once, run anywhere" solution for quantum computing.

## Prerequisites

Before installing Qumat, ensure you have:
- **Python 3.10** (>=3.10, <3.11)
- **Poetry** (Python package manager)
- **Git**

## Installation

### 1. Install Poetry

If you don't have Poetry installed:

```bash
pip install -U poetry
```

Or follow the [Poetry official documentation](https://python-poetry.org/docs/) for alternative installation methods.

### 2. Clone and Install

Clone the repository and install dependencies:

```bash
git clone https://github.com/apache/mahout.git
cd mahout
poetry install
```

For development work with linting and testing tools:

```bash
poetry install --extras dev
```

### 3. Activate the Environment

```bash
poetry shell
```

## Quick Start

### Basic Circuit with Qiskit Backend

Here's a simple example creating a Bell state (entangled qubits):

```python
from qumat import QuMat

# Configure Qiskit backend
backend_config = {
    "backend_name": "qiskit",
    "backend_options": {
        "simulator_type": "aer_simulator",
        "shots": 1024,
    },
}

# Create QuMat instance
qc = QuMat(backend_config)

# Create a 2-qubit circuit
qc.create_empty_circuit(num_qubits=2)

# Apply gates to create Bell state
qc.apply_hadamard_gate(qubit_index=0)
qc.apply_cnot_gate(control_qubit_index=0, target_qubit_index=1)

# Execute and get results
results = qc.execute_circuit()
print(f"Results: {results}")
```

### Using Cirq Backend

The same circuit with Cirq - just change the backend configuration:

```python
from qumat import QuMat

# Configure Cirq backend
backend_config = {
    "backend_name": "cirq",
    "backend_options": {
        "simulator_type": "simulator",
        "shots": 1024,
    },
}

qc = QuMat(backend_config)
qc.create_empty_circuit(num_qubits=2)

# Same quantum operations work across backends!
qc.apply_hadamard_gate(qubit_index=0)
qc.apply_cnot_gate(control_qubit_index=0, target_qubit_index=1)

results = qc.execute_circuit()
print(f"Results: {results}")
```

### Using Amazon Braket Backend

And the same with Amazon Braket:

```python
from qumat import QuMat

# Configure Amazon Braket backend
backend_config = {
    "backend_name": "amazon_braket",
    "backend_options": {
        "simulator_type": "local_simulator",
        "shots": 1024,
    },
}

qc = QuMat(backend_config)
qc.create_empty_circuit(num_qubits=2)

qc.apply_hadamard_gate(qubit_index=0)
qc.apply_cnot_gate(control_qubit_index=0, target_qubit_index=1)

results = qc.execute_circuit()
print(f"Results: {results}")
```

## Common Gate Operations

### Single-Qubit Gates

```python
from qumat import QuMat

backend_config = {
    "backend_name": "qiskit",
    "backend_options": {"simulator_type": "aer_simulator", "shots": 1024},
}
qc = QuMat(backend_config)
qc.create_empty_circuit(num_qubits=1)

# Pauli gates
qc.apply_pauli_x_gate(qubit_index=0)  # Bit flip (NOT gate)
qc.apply_pauli_y_gate(qubit_index=0)  # Y rotation
qc.apply_pauli_z_gate(qubit_index=0)  # Phase flip

# Hadamard gate (creates superposition)
qc.apply_hadamard_gate(qubit_index=0)

# Rotation gates
import math
qc.apply_rx_gate(qubit_index=0, angle=math.pi/4)  # Rotate around X-axis
qc.apply_ry_gate(qubit_index=0, angle=math.pi/4)  # Rotate around Y-axis
qc.apply_rz_gate(qubit_index=0, angle=math.pi/4)  # Rotate around Z-axis

# Universal gate
qc.apply_u_gate(qubit_index=0, theta=math.pi/2, phi=0, lambd=math.pi)
```

### Multi-Qubit Gates

```python
from qumat import QuMat

backend_config = {
    "backend_name": "qiskit",
    "backend_options": {"simulator_type": "aer_simulator", "shots": 1024},
}
qc = QuMat(backend_config)
qc.create_empty_circuit(num_qubits=3)

# CNOT gate (controlled-NOT)
qc.apply_cnot_gate(control_qubit_index=0, target_qubit_index=1)

# Toffoli gate (controlled-controlled-NOT)
qc.apply_toffoli_gate(
    control_qubit_index1=0,
    control_qubit_index2=1,
    target_qubit_index=2
)

# SWAP gate
qc.apply_swap_gate(qubit_index1=0, qubit_index2=1)

# Controlled-SWAP (Fredkin gate)
qc.apply_cswap_gate(
    control_qubit_index=0,
    target_qubit_index1=1,
    target_qubit_index2=2
)
```

## Parameterized Circuits

Qumat supports parameterized circuits for variational quantum algorithms:

```python
from qumat import QuMat
import math

backend_config = {
    "backend_name": "qiskit",
    "backend_options": {"simulator_type": "aer_simulator", "shots": 1024},
}
qc = QuMat(backend_config)
qc.create_empty_circuit(num_qubits=2)

# Use parameter names (strings) instead of values
qc.apply_ry_gate(qubit_index=0, angle="theta")
qc.apply_rx_gate(qubit_index=1, angle="phi")

# Bind parameters when executing
results = qc.execute_circuit(parameter_values={
    "theta": math.pi / 4,
    "phi": math.pi / 2
})
print(f"Results: {results}")
```

## Working with State Vectors

Get the final quantum state vector:

```python
from qumat import QuMat

backend_config = {
    "backend_name": "qiskit",
    "backend_options": {"simulator_type": "aer_simulator", "shots": 1024},
}
qc = QuMat(backend_config)
qc.create_empty_circuit(num_qubits=2)

qc.apply_hadamard_gate(qubit_index=0)
qc.apply_cnot_gate(control_qubit_index=0, target_qubit_index=1)

# Get the state vector
state_vector = qc.get_final_state_vector()
print(f"State vector: {state_vector}")
```

## Measuring Quantum State Overlap

Use the swap test to measure overlap between quantum states:

```python
from qumat import QuMat

backend_config = {
    "backend_name": "qiskit",
    "backend_options": {"simulator_type": "aer_simulator", "shots": 1024},
}
qc = QuMat(backend_config)
qc.create_empty_circuit(num_qubits=3)

# Prepare states on qubits 1 and 2
qc.apply_hadamard_gate(qubit_index=1)
qc.apply_hadamard_gate(qubit_index=2)

# Measure overlap using qubit 0 as ancilla
overlap = qc.measure_overlap(qubit1=1, qubit2=2, ancilla_qubit=0)
print(f"Overlap: {overlap}")
```

## Visualizing Circuits

Draw your quantum circuit:

```python
from qumat import QuMat

backend_config = {
    "backend_name": "qiskit",
    "backend_options": {"simulator_type": "aer_simulator", "shots": 1024},
}
qc = QuMat(backend_config)
qc.create_empty_circuit(num_qubits=2)

qc.apply_hadamard_gate(qubit_index=0)
qc.apply_cnot_gate(control_qubit_index=0, target_qubit_index=1)

# Display the circuit
qc.draw()
```

## Troubleshooting

### Import Errors

If you encounter `ModuleNotFoundError` for backends:

```bash
# Make sure you've installed dependencies
poetry install

# Activate the Poetry environment
poetry shell
```

### Circuit Not Initialized Error

Always call `create_empty_circuit()` before applying gates:

```python
qc = QuMat(backend_config)
qc.create_empty_circuit(num_qubits=2)  # Don't forget this!
qc.apply_hadamard_gate(qubit_index=0)
```

### Parameter Not Found Error

Ensure parameter names match when binding:

```python
# Define parameter
qc.apply_rx_gate(qubit_index=0, angle="my_angle")

# Use same name when binding
results = qc.execute_circuit(parameter_values={"my_angle": 3.14})
```

## Next Steps

- Check out [Basic Gates](basic_gates.md) for detailed gate explanations
- Explore [Parameterized Circuits](parameterized_circuits.md) for advanced usage
- See [API Documentation](api.md) for complete API reference
- Try the examples in the `examples/` directory

## Contributing

We welcome contributions! See [CONTRIBUTING.md](../CONTRIBUTING.md) for guidelines on how to contribute to Qumat.

## Support

- **Documentation**: [Apache Mahout Website](http://mahout.apache.org/)
- **Issues**: [GitHub Issues](https://github.com/apache/mahout/issues)
- **Mailing List**: dev@mahout.apache.org

Happy quantum computing! 🚀
