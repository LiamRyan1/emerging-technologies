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

## References

- Deutsch–Jozsa algorithm — https://en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm
- [What exactly is an oracle?](https://quantumcomputing.stackexchange.com/questions/4625/what-exactly-is-an-oracle/4626#4626): Explains the oracle abstraction, why functions are treated as black boxes, and what reversibility requires of the circuit.
- [Quantum Superposition](https://scienceexchange.caltech.edu/topics/quantum-science-explained/quantum-superposition): Background on superposition, which is what makes a single oracle query sufficient in Problem 4.
- [Quantum Entanglement and Global Properties](https://plato.stanford.edu/archives/fall2008/entries/qt-entangle/#5): Context for why quantum algorithms can determine global properties such as constant or balanced without checking every input.
- [Group Theory and Quantum Algorithms](https://www.ibm.com/quantum/blog/group-theory): Broader context on the mathematical structures underlying quantum speedups of the kind demonstrated here