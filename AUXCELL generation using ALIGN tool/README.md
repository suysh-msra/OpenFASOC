# AUX CELL GENERATION FOR - OpenFASoC(Fully Open-Source Autonomous SoC)
In Open FASoC flow, to generate a automated Analog design, few auxilaury cells(.lef,.gds) are required to be created which cannot be implemented with existing library cells (like Header and SLC in temp_sence_gen). To generate these .lef and .gds files of AUX cells we use ALIGN.

## Required inputs from previous step of flow:
Based upon given SCHEMATIC and SPECIFICATIONS of AUX cell, a SPICE Netlist should be created with .sp file extension.

## Introduction to ALIGN tool
ALIGN is an open source automatic layout generator for analog circuits jointly developed under the DARPA IDEA program by the University of Minnesota, Texas A&M University, and Intel Corporation.

The goal of ALIGN (Analog Layout, Intelligently Generated from Netlists) is to automatically translate an unannotated (or partially annotated) SPICE netlist of an analog circuit to a GDSII layout. The repository also releases a set of analog circuit designs.

The ALIGN flow includes the following steps:

Circuit annotation creates a multilevel hierarchical representation of the input netlist. This representation is used to implement the circuit layout in using a hierarchical manner. Design rule abstraction creates a compact JSON-format represetation of the design rules in a PDK. This repository provides a mock PDK based on a FinFET technology (where the parameters are based on published data). These design rules are used to guide the layout and ensure DRC-correctness. Primitive cell generation works with primitives, i.e., blocks at the lowest level of design hierarchy, and generates their layouts. Primitives typically contain a small number of transistor structures (each of which may be implemented using multiple fins and/or fingers). A parameterized instance of a primitive is automatically translated to a GDSII layout in this step. Placement and routing performs block assembly of the hierarchical blocks in the netlist and routes connections between these blocks, while obeying a set of analog layout constraints. At the end of this step, the translation of the input SPICE netlist to a GDSII layout is complete.

### Steps to Install ALIGN:

#### Prerequisites

gcc >= 6.1.0 (For C++14 support)
python >= 3.7
  Use the following commands to install ALIGN tool:
  ```
  export CC=/usr/bin/gcc
export CXX=/usr/bin/g++
git clone https://github.com/ALIGN-analoglayout/ALIGN-public
cd ALIGN-public

#Create a Python virtualenv
python -m venv general
source general/bin/activate
python -m pip install pip --upgrade

# Install ALIGN as a USER
pip install -v .

# Install ALIGN as a DEVELOPER
pip install -e .

pip install setuptools wheel pybind11 scikit-build cmake ninja
pip install -v -e .[test] --no-build-isolation
pip install -v --no-build-isolation -e . --no-deps --install-option='-DBUILD_TESTING=ON'
```
**Making ALIGN Portable to Sky130 tehnology**
Clone the following Repository inside ALIGN-public directory:
```
git clone https://github.com/ALIGN-analoglayout/ALIGN-pdk-sky130
```
move SKY130_PDK folder to /your-relative-path/OpenFASoC/AUXCELL/ALIGN-public/pdks

## Running ALIGN TOOL

Everytime we start running tool in new terminal run following commands.
```
python -m venv general
source general/bin/activate
```
Commands to run ALIGN (goto ALIGN-public directory)

```
mkdir work
cd work
```
**Running an EXAMPLE:**

```
schematic2layout.py ../examples/telescopic_ota -p ../pdks/FinFET14nm_Mock_PDK/
```
**Running an EXAMPLE on Sky130pdk**
```
schematic2layout.py ../ALIGN-pdk-sky130/examples/five_transistor_ota -p ../pdks/SKY130_PDK/
```

### FLOW
Following are some screenshots demonstrating the above steps:
