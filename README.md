# Grafcet Boolean Equation Generator

A lightweight Python library for modeling Grafcet (SFC - Sequential Function Chart) structures and automatically generating boolean equations.

This project is designed for industrial automation engineers who need to:

* Build Grafcet structures programmatically
* Generate PLC boolean equations automatically
* Export state equations
* Generate action equations
* Generate SET / RESET equations
* Manage multiple Grafcets inside a single project

---

# Features

* Pure Python
* No external dependencies
* Multiple Grafcets per project
* Parallel transitions support
* Parallel activations support
* Action conditions support
* Automatic state equation generation
* Automatic action equation generation
* Automatic SET / RESET generation
* Easy integration with PLC code generators

---

# Architecture

```text
Grafcet
    ├── Steps
    ├── Transitions
    └── Actions

            ↓

BooleanEquationGenerator

            ↓

State Equations
Action Equations
SET / RESET Equations
```

The Grafcet class only stores the structure.

The BooleanEquationGenerator is responsible for generating logical equations.

This separation keeps the project clean and extensible.

---

# Installation

No dependencies required.

---

# Basic Example

```python
from grafcet_engine import *

g1 = Grafcet(
    name="MainCycle",
    init_step="X100"
)

g1.add_transition(
    "X100",
    "X101",
    "BTN_START"
)

g1.add_transition(
    "X101",
    "X102",
    "PART_DETECTED"
)

g1.add_transition(
    "X102",
    "X100",
    "CYCLE_END"
)

g1.add_action(
    "X101",
    "MOTOR_CONVEYOR"
)

generator = BooleanEquationGenerator()

generator.add_new_grafcet(g1)

for equation in generator.generate_state_equations():
    print(equation)
```

---

# Industrial Example

## Conveyor Sorting System

This example represents a small automated sorting station.

### Inputs

```text
BTN_START
SENSOR_PART
SENSOR_SORT
SENSOR_END
```

### Outputs

```text
MOTOR_CONVEYOR
CYLINDER_SORTER
```

### Grafcet

```text
X100
 |
 | BTN_START
 v
X101
 |
 | SENSOR_PART
 v
X102
 |
 | SENSOR_SORT
 v
X103
 |
 | SENSOR_END
 v
X100
```

---

## Project Definition

```python
from grafcet_engine import *

main_cycle = Grafcet(
    name="MainCycle",
    init_step="X100"
)

main_cycle.add_transition(
    "X100",
    "X101",
    "BTN_START"
)

main_cycle.add_transition(
    "X101",
    "X102",
    "SENSOR_PART"
)

main_cycle.add_transition(
    "X102",
    "X103",
    "SENSOR_SORT"
)

main_cycle.add_transition(
    "X103",
    "X100",
    "SENSOR_END"
)

main_cycle.add_action(
    "X101",
    "MOTOR_CONVEYOR"
)

main_cycle.add_action(
    "X102",
    "CYLINDER_SORTER"
)

generator = BooleanEquationGenerator()

generator.add_new_grafcet(
    main_cycle
)
```

---

## Generated State Equations

```text
X100 = (X103 & SENSOR_END) | (X100 & !X101) | MainCycle_INIT

X101 = (X100 & BTN_START) | (X101 & !X102)

X102 = (X101 & SENSOR_PART) | (X102 & !X103)

X103 = (X102 & SENSOR_SORT) | (X103 & !X100)
```

---

## Generated Action Equations

```text
MOTOR_CONVEYOR = X101

CYLINDER_SORTER = X102
```

---

## Generated SET / RESET Equations

```text
X101_SET = (X100 & BTN_START)

X101_RESET = (X101 & SENSOR_PART)

X102_SET = (X101 & SENSOR_PART)

X102_RESET = (X102 & SENSOR_SORT)
```

---

# Multiple Grafcets

The generator can manage multiple Grafcets simultaneously.

```python
generator = BooleanEquationGenerator()

generator.add_new_grafcet(main_cycle)

generator.add_new_grafcet(alarm_cycle)

generator.add_new_grafcet(manual_cycle)
```

This allows a project to be separated into independent functional units.

---

# Typical Use Cases

* PLC code generation
* Structured Text generation
* Ladder generation
* Industrial simulation tools
* Grafcet editors
* SCADA development tools
* Digital twin projects
* Educational Grafcet tools

---

# Future Roadmap

* Grafcet validation engine
* Grafcet visualizer
* Graphviz export
* PLC Structured Text export
* Ladder export
* JSON import/export
* YAML import/export
* TOML integration

---

# License

MIT License
