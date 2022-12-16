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

![Screenshot from 2022-12-16 09-56-24](https://user-images.githubusercontent.com/84946358/208022592-9b305e37-f682-4a52-9530-ec95565ad07d.png)

![Screenshot from 2022-12-16 10-02-45](https://user-images.githubusercontent.com/84946358/208022890-28ecea64-d320-4e31-bd21-f8fd8e216c15.png)

![Screenshot from 2022-12-15 21-40-39](https://user-images.githubusercontent.com/84946358/208023082-d5361608-b782-45bc-bad4-2ec48c93f743.png)

![Screenshot from 2022-12-15 21-40-25](https://user-images.githubusercontent.com/84946358/208023150-d231603d-7aba-4682-b458-7af2ed021745.png)


We can view the .lef and .gds files thus created using Klayout, as shown below:


![Screenshot from 2022-12-15 21-41-39](https://user-images.githubusercontent.com/84946358/208023402-6cca93f9-b80b-4470-abcb-4ae2e75d3a33.png)


![Screenshot from 2022-12-15 21-51-11](https://user-images.githubusercontent.com/84946358/208023495-8b56137d-0427-4f6b-8b1c-4094c369c455.png)

## Designing AUX cells for PLL:
Source Repo for SPICE FILES - https://github.com/lakshmi-sathi/avsdpll_1v8

The aux cells needed are charge pump, frequency divider, phase detector and voltage controlled oscillator. Their netlist can be found on the abovementioned repo. 

Following is an example on how to generate .lef and .gds files for charge pump, similar steps may be followed for generating the files for generating the rest of the auxcells.

### Circuit: 

![201048674-4052a8b5-e5a2-4ce2-ba25-bcc11d337d27](https://user-images.githubusercontent.com/84946358/208024579-be1eb715-30b7-4220-a2b5-1dabd4bf375b.jpg)

### Spice netlist:
```
.subckt cp dn out up vdd vss

m1 in_2 in_2 vdd vdd sky130_fd_pr__pfet_01v8 L=150n W=420n

m2 in_3 in_2 vdd vdd sky130_fd_pr__pfet_01v8 L=150n  W=540n

m3 out downb in_3 in_3 sky130_fd_pr__pfet_01v8 L=150n  W=420n

m4 out up 7 7 sky130_fd_pr__nfet_01v8 L=150n  W=420n

m5 7 8 vss vss sky130_fd_pr__nfet_01v8 L=150n  W=540n

m6 8 8 vss vss sky130_fd_pr__nfet_01v8 L=150n  W=420n

m7 9 dn in_3 in_3 sky130_fd_pr__pfet_01v8 L=150n  W=540n
m8 9 9 vss vss sky130_fd_pr__nfet_01v8 L=150n  W=420n

m11 up upb vdd vdd sky130_fd_pr__pfet_01v8 L=150n  W=720n

m12 up upb vss vss sky130_fd_pr__nfet_01v8 L=150n  W=420n
m13 dn downb vdd vdd sky130_fd_pr__pfet_01v8 L=150n  W=720n

m14 dn downb vss vss sky130_fd_pr__nfet_01v8 L=150n  W=420n

m9 10 10 vdd vdd sky130_fd_pr__pfet_01v8 L=150n  W=420n

m10 10 upb 7 7 sky130_fd_pr__nfet_01v8 L=150n  W=540n

.ends cp
```

## Terminal view:

![Screenshot from 2022-12-16 10-33-48](https://user-images.githubusercontent.com/84946358/208026437-c2b643cd-bffd-4389-82b0-63f20db4f65d.png)

## GDS file:

![Screenshot from 2022-12-16 10-36-19](https://user-images.githubusercontent.com/84946358/208026644-fbf2a233-a70d-4852-85d1-5455a62d9146.png)

## LEF File:


![cp_0lef](https://user-images.githubusercontent.com/84946358/208026943-38d70de1-4d7d-4cd2-9305-166b3f3c9364.png)

Thus we see that .gds and .lef files are generated for the charge pump auxcell. The same may be generated for the other auxcells too, by following similar steps.

# Author

Suyash Misra, M Tech (VLSI), IIITB

# Contributors

Kunal Ghosh, Co-founder and Director, VSD Corp Pvt Ltd
SANAMPUDI GOPALA KRISHNA REDDY MT2022527, Postgraduate Student, International Institute of Information Technology, Bangalore
GANDI AJAY KUMAR, Postgraduate Student, International Institute of Information Technology, Bangalore











