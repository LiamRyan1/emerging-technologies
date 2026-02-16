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


## References

- Deutsch–Jozsa algorithm — https://en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm