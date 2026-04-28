# emerging-technologies
# introduction

This notebook explores the difference between classical and quantum algorithms through the Deutsch and Deutsch–Jozsa problems, demonstrating how quantum computation can solve certain tasks exponentially faster than any classical approach.

## Setup

### Requirements

Make sure you have Python 3.8+ and install the dependencies:

```bash
pip install -r requirements.txt
```

### Running the Notebook

Open `problems.ipynb` and run cells in order as each problem builds on the previous one.


## Problem 1: Generating Random Boolean Functions

The [Deutsch–Jozsa algorithm](https://en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm) is built around a promise: any function it receives is guaranteed to be either constant or balanced. A constant function returns the same output for every input, while a balanced function returns `True` for exactly half of all possible inputs and `False` for the other half.

With four Boolean inputs there are $2^4 = 16$ possible input combinations, so the balanced case requires exactly 8 inputs mapped to `True` and 8 to `False`.

This problem implements `random_constant_balanced`, a function that randomly returns a Boolean function satisfying this promise with equal probability of producing either type.

## Problem 2: Classical Testing for Function Type

When making use of the classical solution, given a black-box function `f`, the only way to determine its type classically is to call it with inputs and observe the outputs.

The naive approach checks all $2^4 = 16$ inputs, but to avoid this its possible to stop early once the answer is certain. This problem implements `determine_constant_balanced`, a function that identifies whether `f` is constant or balanced using **at most 9 calls**.

### Classical vs. Quantum Complexity

The 9-query classical bound stands in sharp contrast to Deutsch's quantum algorithm, which solves the equivalent single-bit problem in a **single query**.

## Problem 3: Quantum Oracles

Where Problem 2 established the classical cost of up to 9 queries, Problem 3 moves to the quantum approach, starting with the building block every quantum algorithm needs, an oracle.

An oracle is a reversible circuit that encodes a function as a quantum operation without revealing its internal structure. For Deutsch's algorithm the oracle acts on two qubits either a input qubit (qubit 0) that holds x, and a ancilla qubit (qubit 1) that starts at |0> and is flipped whenever f(x) = 1. The input qubit is always left unchanged, which is a requirement for [any valid quantum oracle](https://quantumcomputing.stackexchange.com/questions/4625/what-exactly-is-an-oracle/4626#4626).

In the single-input case there are exactly four possible Boolean functions, giving four oracles:

| Function   | f(0) | f(1) | Type     | Circuit                |
|------------|------|------|----------|------------------------|
| Constant-0 | 0    | 0    | Constant | No gates               |
| Constant-1 | 1    | 1    | Constant | X on ancilla           |
| Identity   | 0    | 1    | Balanced | CNOT (input, ancilla)  |
| Negation   | 1    | 0    | Balanced | X on ancilla then CNOT |

This problem implements all four oracles in Qiskit and demonstrates their behaviour by running each one through a simulator for both possible inputs. Understanding [quantum superposition](https://scienceexchange.caltech.edu/topics/quantum-science-explained/quantum-superposition) is helpful context here, as the reason these oracles are useful is that in Problem 4 the input qubit will be in superposition when the oracle is called, allowing the function to be evaluated on both inputs at once. The [Stanford Encyclopedia entry on quantum entanglement](https://plato.stanford.edu/archives/fall2008/entries/qt-entangle/#5) provides further background on how quantum algorithms can determine global properties of a function without checking every input individually.

Note on Qiskit bitstring ordering: Qiskit writes measurement results with the least significant bit on the right, so for a 2-qubit circuit the result string `"XY"` means qubit 1 = `X` (index `[0]`) and qubit 0 = `Y` (index `[1]`). Throughout the tests, `most_likely[0]` reads the ancilla qubit and `most_likely[1]` reads the input qubit.

## Problem 4: Deutsch's Algorithm with Qiskit

 Problem 4 uses the oracles built in problem 3 inside a full quantum circuit to determine whether a function is constant or balanced in a single query. This demonstrates  quantum advantage, classically the worst case required 9 calls, here only 1 is needed regardless of the function.

The circuit works by putting both qubits into superposition before calling the oracle. The ancilla qubit is flipped to 1 before the superposition step, which causes the oracle's effect to be encoded into the phase of the input qubit rather than into any measurable bit flip. A second superposition step on the input qubit after the oracle converts that phase difference into a measurable outcome. If the function is constant the two paths interfere constructively and the input qubit collapses to 0, if the function is balanced they interfere destructively and it collapses to 1.

This problem implements `deutsch_circuit`, a function that wraps any of the four oracles from Problem 3 in the full Deutsch circuit and demonstrates that it correctly classifies all four functions using [Qiskit](https://www.ibm.com/quantum/qiskit) and its [quantum circuit tools](https://quantum.cloud.ibm.com/learning/en/courses/basics-of-quantum-information/quantum-circuits/introduction).

 [IBM Quantum Learning module on Deutsch's algorithm](https://quantum.cloud.ibm.com/learning/en/courses/fundamentals-of-quantum-algorithms/quantum-query-algorithms/deutsch-algorithm).

## Problem 5: Scaling to the Deutsch–Jozsa Algorithm

Problem 5 builds off of problem 4's single-bit solving of deutch's in one query by scales that same approach to the four-bit functions from Problem 1. The [Deutsch–Jozsa algorithm](https://quantum.cloud.ibm.com/learning/en/modules/computer-science/deutsch-jozsa) generalises Deutsch's circuit to handle any number of input bits while still using only a single query to the oracle, compared to the classical worst case of 9 queries established in Problem 2.

The circuit follows the same structure as Problem 4 but wider. Instead of one input qubit there are now four, plus one ancilla qubit. All five qubits are put into superposition before the oracle runs. After the oracle, a second superposition step is applied to the four input qubits only. If the function is constant all four input qubits collapse to 0, if the function is balanced they collapse to any other combination.

This problem implements `build_oracle_from_function`, which encodes any four-bit Boolean function from Problem 1 as a quantum oracle, and `dj_circuit`, which wraps that oracle in the full Deutsch–Jozsa circuit. The algorithm is demonstrated on both constant functions and two randomly chosen balanced functions, and verified against the classical solution from Problem 2 across 20 randomly generated functions..

## References

- Deutsch–Jozsa algorithm — https://en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm
- [What exactly is an oracle?](https://quantumcomputing.stackexchange.com/questions/4625/what-exactly-is-an-oracle/4626#4626): Explains the oracle abstraction, why functions are treated as black boxes, and what reversibility requires of the circuit.
- [Quantum Superposition](https://scienceexchange.caltech.edu/topics/quantum-science-explained/quantum-superposition): Background on superposition, which is what makes a single oracle query sufficient in Problem 4.
- [Quantum Entanglement and Global Properties](https://plato.stanford.edu/archives/fall2008/entries/qt-entangle/#5): Context for why quantum algorithms can determine global properties such as constant or balanced without checking every input.
- [Group Theory and Quantum Algorithms](https://www.ibm.com/quantum/blog/group-theory): Broader context on the mathematical structures underlying quantum speedups of the kind demonstrated here
- [Deutsch's Algorithm](https://quantum.cloud.ibm.com/learning/en/courses/fundamentals-of-quantum-algorithms/quantum-query-algorithms/deutsch-algorithm): Primary reference for the Deutsch circuit structure used in Problem 4 and the interference argument that makes a single query sufficient and for problem 5 structure and the scaling argument to extend deutch's single bit to n input bits
- [Quantum Circuits](https://quantum.cloud.ibm.com/learning/en/courses/basics-of-quantum-information/quantum-circuits/introduction): Qiskit documentation covering the circuit model used to implement the algorithm.