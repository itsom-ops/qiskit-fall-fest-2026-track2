# Team Intellects · Qiskit Fall Fest 2026 · Track 2: Quantum Optimization

| | |
|---|---|
| **Team** | Team Intellects |
| **Members** | Om Viroje, om.viroje@gmail.com |
| **Track** | 2 · Quantum Optimization |
| **Level reached** | Advanced |
| **Code files** | `track2_portfolio_qaoa.ipynb` |
| **Slides** | `none` |

## Summary

We set out to solve the portfolio selection problem at scale by transitioning from standard QAOA penalty constraints to custom constraint-preserving mixers. We found that restricting the quantum walk to the feasible subspace yields 100% valid portfolios, allowing us to successfully compile a 28-qubit utility-scale model and run hardware-aware error suppression on IBM QPUs.

## Approach

For the Beginner level, we mapped the asset covariance matrix to a cost Hamiltonian and used `LinearEqualityToPenalty` to enforce the budget constraint[cite: 6]. For the Intermediate level, we engineered a custom XY-Mixer ($XX + YY$) combined with a Dicke initial state to ensure the optimizer only explored valid portfolios. For the Advanced level hardware run, we compiled the circuit with `optimization_level=3` and enabled Dynamical Decoupling (XX sequence) to suppress idle decoherence.

## Results

| Quantity | Your result | Reference | Difference |
|---|---|---|---|
| **n=6, p=4 Approx Ratio** | ~0.30 | Brute Force Baseline | Dependent on initialization |
| **n=6 Valid Fraction ($\lambda=3.0$)** | 80.0% | 100% (Classical) | -20.0% |
| **n=12 Valid Fraction (XY-Mixer)** | 100.0% | 100% (Classical) | 0.0% |
| **n=20 Qubit Requirement** | 28 Qubits | N/A | N/A |

## How we checked our results

We built exact classical solvers that evaluated all $2^n$ state vectors[cite: 6]. For $n=6$ (64 total states) and $n=12$ (924 feasible states), the quantum outputs were directly benchmarked against the absolute minimal cost solutions[cite: 6]. Hardware samples were evaluated against the noiseless `StatevectorSampler` distributions[cite: 6].

## Hardware runs

ibm_fez, Job ID: db50aicvf2bc73cv7svg, 2s QPU seconds.

## Limitations

Synthesizing the XY-Mixer for $n=12$ required transpilation prior to the classical optimization loop to keep simulation latency manageable[cite: 6]. At $n=20$ with sector constraints, slack variables inflated the operator to 28 qubits[cite: 6]. At this scale, the method stops working efficiently on NISQ hardware because the parameter landscape flattens into barren plateaus, and transpiled depths exceed coherence limits without Probabilistic Error Cancellation (PEC).

## How to run

- Python version: 3.11+
- Install: `pip install qiskit qiskit_aer qiskit_ibm_runtime qiskit-optimization qiskit-finance matplotlib`
- Run: Execute `track2_portfolio_qaoa.ipynb` sequentially top-to-bottom. The $n=12$ XY-Mixer cell takes roughly 1-2 minutes locally due to pre-loop transpilation.

## Credits

- Track tutorial: adapted from the BasQ Qiskit Fall Fest 2026 tutorials by Benjamin Tirado (text CC BY 4.0, code Apache 2.0).
- AI tools used: none
