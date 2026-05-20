# Virtual CZ Gate Simulation via Local Operations & QPD

## Objective
This project manually implements and verifies the decomposition of a non-local Controlled-Z (CZ) gate into Local Operations (LO) using a Quasi-Probability Distribution (QPD). This serves as a foundational simulation for Distributed Quantum Computing (DQC) architectures, where virtual gates are used to connect modular Quantum Processing Units (QPUs).

## Theoretical Background
In a distributed quantum architecture, applying a gate across two separated chips requires either classical communication (LOCC) or Local Operations (LO) combined with circuit cutting techniques. 
This simulation leverages the mathematical identity to decompose a CZ gate:
$$CZ = \frac{I \otimes I + Z \otimes I + I \otimes Z - Z \otimes Z}{2}$$
To implement this without a physical 2-qubit gate, the channel is expressed as a QPD of 6 local circuits utilizing virtual $R_z$ rotations and Mid-Circuit Measurements (MCMs). 

## Methodology
Instead of relying on automated toolboxes (like IBM's `qiskit-addon-cutting`), this repository builds the logic from scratch to analyze the exact statistical sampling overhead:
1. Input Basis: Prepared 16 linearly independent 2-qubit basis states using $X$ and $H$ gates.
2. Circuit Construction: Hard-coded the 6 LO circuits corresponding to the QPD for each input state.
3. Measurement: Simulated the circuits across 9 measurement bases (XX, YY, ZZ, XY, YX, XZ, ZX, YZ, ZY) using Qiskit's `AerSimulator` (5000 shots per circuit).
4. Classical Feed-forward: Engineered the classical post-processing logic to adjust expectation values based on MCM outcomes ($\pm 1$).

## Results & Verification
To verify the fidelity of the virtual gate, we performed Quantum State Tomography (QST) across all 16 basis states. 
* The reconstructed density matrices were compared against the output of an ideal simulated CZ gate.
* The Frobenius norm of the difference between the ideal and simulated density matrices yielded deviations ranging from `0.04` to `0.09`. 
* This deviation confirms theoretical expectations, representing the statistical sampling variance introduced by the QPD overhead factor ($\gamma^2 = 9$), rather than a systemic error in the channel identity.

## Next Steps
* Quantum Process Tomography (QPT): The current script verifies the channel via 16 independent state fidelities. The next update will apply a linear basis inversion to the $16 \times 16$ expectation matrix to reconstruct the full Pauli Transfer Matrix (PTM) and compute the exact Process Fidelity.
* Algorithmic Application: Use these 6 LO circuits to replace long-range SWAP gates in a Distributed Quantum Walk or QAOA MaxCut graph.
