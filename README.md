# Quantum Algorithms Track - Complete Summary

**Team Name**: To_F_or_Not_to_F  
**Team Member**: Sarvagya Kaushik

---

## Overview
This notebook implements solutions for all eight problems in the Quantum Algorithms Track, demonstrating quantum walks, adiabatic quantum computation, QAOA, and the effects of noise and measurement on quantum systems.

---

## Installation & Setup

### Required Packages
```bash
pip install pylatexenc
pip install qiskit
pip install qiskit-aer
pip install matplotlib
pip install numpy
pip install scipy
```

### Core Dependencies
- **Qiskit**: Quantum circuit construction and simulation
- **Qiskit Aer**: High-performance quantum simulators
- **NumPy**: Numerical computations
- **SciPy**: Optimization and linear algebra
- **Matplotlib**: Visualization

---

## Problem-by-Problem Summary

### **Problem 0: Classical Random Walk**
- **Goal**: Establish baseline RMS displacement scaling
- **Implementation**: Monte Carlo simulation with NumPy
- **Result**: Confirms diffusive RMS(t) = √t scaling
- **Runtime**: ~1 second for 5000 walkers × 200 steps

### **Problem 1: X-Coin Quantum Walk**
- **Implementation**: 
  - Custom increment/decrement gates for signed position register
  - Deterministic X coin flip
- **Key Functions**: `make_increment_gate()`, `make_decrement_gate()`, `make_random_walk_x()`
- **Result**: Structured ballistic support (not diffusive)

### **Problem 2: Hadamard-Coin Quantum Walk**
- **Implementation**:
  - Ripple-carry arithmetic for position shifts
  - Controlled S±gates
- **Key Functions**: `shift_plus()`, `shift_minus()`, `make_quantum_walk()`
- **Result**: Ballistic spreading with interference peaks at edges
- **Comparison**: Quantum RMS exceeds classical √t at t=2,3

### **Problem 3: Graph Quantum Walk**
- **Implementation**:
  - Grover diffusion coin per vertex
  - Flip-flop shift operator on arc space
- **Key Functions**: `grover_coin()`, `flipflop_shift()`, `graph_walk()`
- **Result**: Non-uniform vertex marginals showing quantum transport

### **Problem 4: Oscillator Walk with Potential**
- **Implementation**:
  - Fock-space truncation with reflection boundaries
  - Optional quadratic phase potential
- **Key Functions**: `oscillator_walk_step()`, `simulate_oscillator_walk()`
- **Result**: Phase potential moderates spreading; finite truncation causes localization

### **Problem 5: Statevector Estimation**
- **Implementation**:
  - Random Pauli-basis measurements
  - Linear inversion + physical projection
- **Key Functions**: `sample_product_pauli()`, `estimate_pauli_moments()`, `reconstruct_density_from_moments()`
- **Result**: 200 test states reconstructed with >95% fidelity (500 shots each)

### **Problem 6: Adiabatic Evolution**
- **Implementation**:
  - Spectral gap analysis
  - First-order Trotterization
- **Key Functions**: `scan_spectrum()`, `min_gap()`, `trotterized_adiabatic_state()`
- **Result**: p=3 slices achieve C≈1.92 for triangle MaxCut

### **Problem 7: QAOA**
- **Implementation**:
  - Variational optimization (Nelder-Mead)
  - Noise model: amplitude damping
- **Key Functions**: `qaoa_triangle_circuit()`, `optimize_qaoa()`, `sweep_noise_triangle()`
- **Result**: 
  - p=1,2,3 reach C=2.0 noiseless
  - QAOA outperforms Trotterized evolution under noise

### **Problem 8: Measurement-Induced Effects**
- **Implementation**:
  - Random circuit with midcircuit measurements
  - Entanglement entropy via state vector simulation
- **Key Functions**: `simulate_chain_entropy()`, `apply_two_qubit_unitary()`
- **Result**: 
  - Entanglement transition at measurement rate p≈0.3
  - Zeno effect demonstrated

---

## Helper Functions Reference

### **Quantum Circuit Helpers**
```python
# Pauli matrices and common states
I, X, Y, Z = ... # 2×2 Pauli matrices
ket_from_abc(a,b,c,d) # Normalize (a,b,c,d) to state vector
kron(*ops) # Tensor product of operators

# Gate construction
make_increment_gate(subq) # Controlled INC for signed arithmetic
make_decrement_gate(subq) # Controlled DEC
shift_plus(n) # Ripple-carry increment
shift_minus(n) # Ripple-carry decrement
```

### **Walk Simulation**
```python
# Classical baseline
classical_random_walk(steps, num_walkers)

# Quantum walks
make_random_walk_x(num_steps, npos) # X-coin walk
make_quantum_walk(num_steps, npos) # Hadamard-coin walk
graph_walk(graph, T, start_vertex) # Graph walk
oscillator_walk(N, T, phi_func) # Oscillator ladder walk
```

### **State Reconstruction**
```python
# Measurement
sample_product_pauli(psi, num_shots, rng)

# Tomography
estimate_pauli_moments(shots) # From measurement records
reconstruct_density_from_moments(est) # Linear inversion
project_to_physical_density(rho) # Enforce PSD, trace-1
best_rank1_pure_state(rho) # Extract pure state

# Batch processing
run_file(path, shots, seed) # Process test file
```

### **Optimization & Adiabatic**
```python
# Hamiltonian tools
H_of_s(s) # Interpolating Hamiltonian
scan_spectrum(npoints) # Eigensystem vs s
min_gap(evals_all) # Find minimum gap

# Evolution
evolve_time_dependent(T, nsteps, psi_init) # Trotterized dynamics
trotterized_adiabatic_state(p, T) # p-slice evolution

# QAOA
qaoa_triangle_circuit(gammas, betas)
optimize_qaoa(p, nstarts, seed)
expected_cost_from_counts(counts)
```

### **Noise & Measurement**
```python
# Noise models
amp_damp_noise(p_AD) # Qiskit NoiseModel

# Circuit execution
run_counts(circ, shots, noise_model, backend)

# Entanglement tracking
simulate_chain_entropy(n, depth, pm, gate, ntrajectories)
apply_two_qubit_unitary(state, U2, n, i, j)
```

---

## Reproducibility Notes

### **Seeds & Parameters**
- Most runs use `seed=7` or `seed=1234`
- Shot counts: 500–5000 depending on problem
- Typical runtimes:
  - Problems 0–4: seconds to minutes
  - Problem 5: ~1 minute for 200 states
  - Problems 6–7: 1–3 minutes per optimization
  - Problem 8: 5–10 minutes for entropy scans

### **Known Issues & Fixes**
- **Bit ordering**: Qiskit measures LSB-first; code reverses for signed positions
- **Endianness**: Two's complement positions corrected via `[::-1]` slicing
- **Truncation**: Oscillator walks need N≥2×max_steps to avoid boundary effects

### **Testing**
All implementations validated against:
- Theoretical predictions (RMS scaling, spectral gaps)
- Known optimal values (triangle MaxCut C=2.0)
- State fidelity >95% for tomography

---

## File Structure
```
planck-d-algo-3.ipynb
├── Problem 0: Classical baseline
├── Problem 1: X-coin walk
├── Problem 2: Hadamard walk
├── Problem 3: Graph walk
├── Problem 4: Oscillator walk
├── Problem 5: State reconstruction
├── Problem 6: Adiabatic evolution
├── Problem 7: QAOA
└── Problem 8: Noise & measurements
```

---

## Citation
This work implements the Quantum Algo Track challenges using Qiskit 2.0+ with Aer backend simulation. All numerical results are reproducible with the provided seeds and parameters.
