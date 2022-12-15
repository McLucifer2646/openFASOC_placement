# openFASOC - Placement
<h4> Verification of existing openFasoc flow </h4>

FASoC stands for Fully Autonomous System-on-Chip

The FASoC Program is focused on developing a complete system-on-chip (SoC) synthesis tool from user specification to GDSII. FASoC leverages a differentiating technology to automatically synthesize “correct-by-construction” Verilog descriptions for both analog and digital circuits and enable a portable, single pass implementation flow. The SoC synthesis tool realizes analog circuits, including PLLs, power management, ADCs, and sensor interfaces by recasting them as structures composed largely of digital components while maintaining analog performance. They are then expressed as synthesizable Verilog blocks composed of digital standard cells augmented with a few auxiliary cells generated with an automatic cell generation tool. By expanding the IPXACT format and the Socrates tool from ARM, we then enable composition of vast numbers of digital and analog components into a single correct-by-construction design. This project is led by a team of researchers at the Universities of Michigan, Virginia, and Arm.

# Installation
## OpenFASOC:
The command used to install OpenFASOC are 
```
git clone https://github.com/idea-fasoc/openfasoc
cd openfasoc
pip install -r requirements.txt
```
For the complete steps of installing OpenFASOC, refer Manual Installation from [here](https://github.com/idea-fasoc/OpenFASOC/blob/main/docs/source/getting-started.rst). 

## OpenROAD: 
OpenROAD is an integrated chip physical design tool that takes a design from synthesized Verilog to routed layout. OpenROAD uses the OpenDB database and OpenSTA for static timing analysis. Documentation is also available [here](https://openroad.readthedocs.io/en/latest/main/README.html).


The commands to install OpenROAD are,
```
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD.git
cd OpenROAD
./etc/DependencyInstaller.sh
./etc/DependencyInstaller.sh -run
./etc/DependencyInstaller.sh -dev
mkdir build
cd build
cmake ..
make
```

## Klayout
Downlaod the latest version of the Klayout from [here](https://www.klayout.de/build.html). Install the following dependencies: qt5-default, qttools5-dev, libqt5xmlpatterns5-dev, qtmultimedia5-dev, libqt5multimediawidgets5 and libqt5svg5-dev.
```
sudo apt-get install -y libqt5widgets5
sudo dpkg -i klayout_0.27.11-1_amd64.deb
```

## Netgen
To install Netgen, 
```
sudo add-apt-repository ppa:ngsolve/ngsolve
sudo apt-get update
sudo apt-get install ngsolve
```

## Yosys
The software used to run gate level synthesis is Yosys. Yosys is a framework for Verilog RTL synthesis. It currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains. Yosys can be adapted to perform any synthesis job by combining the existing passes (algorithms) using synthesis scripts and adding additional passes as needed by extending the Yosys C++ code base.


To install yosys, install the prerequisites using the following command 
 ```
 sudo apt-get install build-essential clang bison flex \
	libreadline-dev gawk tcl-dev libffi-dev git \
	graphviz xdot pkg-config python3 libboost-system-dev \
	libboost-python-dev libboost-filesystem-dev zlib1g-dev
```
To install latest Version of Yosys, 
```
git clone https://github.com/YosysHQ/yosys.git
make
sudo make install 
make test
```

## Magic
Run following commands one by one to fulfill the system requirement.
#### Prerequisites for magic
```
sudo apt-get install m4
sudo apt-get install tcsh
sudo apt-get install csh
sudo apt-get install libx11-dev
sudo apt-get install tcl-dev tk-dev
sudo apt-get install libcairo2-dev
sudo apt-get install mesa-common-dev libglu1-mesa-dev
sudo apt-get install libncurses-dev
```
#### Install magic
```
git clone https://github.com/RTimothyEdwards/magic
cd magic/
./configure
sudo make
sudo make install
```

## Understanding the Verilog Generation flow for Placement 
```
 ==============================================================================
  ____  _        _    ____ _____
 |  _ \| |      / \  / ___| ____|
 | |_) | |     / _ \| |   |  _|
 |  __/| |___ / ___ \ |___| |___
 |_|   |_____/_/   \_\____|_____|

 ==============================================================================
```
The following steps are as mentioned in the Makefile found at the following path https://github.com/idea-fasoc/OpenFASOC/blob/main/openfasoc/generators/temp-sense-gen/flow/Makefile. This file defines the proper flow of generating the temp-sense-gen from auxilliary files to finished product.

**Inputs:** This step receives the 2_floorplan.odb , 2_floorplan.sdc files as the input along with aux_files in .gds format. 

**Outputs:** It will give out a 3_place.odb file. The output of this phase will have all instances placed in their corresponding voltage domain, ready for routing.  

**Process post placement:** The input odb files obtained as an output from OpenROAD flow are first converted to .def, and now the aux files along with newly generated .def files are converted into gds, these files and tech files (klayout.lyt) are read and the corresponding placement image in SVG format is obtained.


**The steps involved in this process are:**

STEP 1: Global placement without placed IOs, timing-driven, and routability-driven. 

STEP 2: IO placement (non-random)

STEP 3: Global placement with placed IOs, timing-driven, and routability-driven.

STEP 4: Resizing & Buffering (not needed for the tempsense)

STEP 5: Detail placement

STEP 6: Clean Targets

![image](https://github.com/McLucifer2646/openFASOC_placement/blob/7ce9ee1831767abfd174417887a3dfc0500286f5/Images/Makefile1.png)

![image](https://github.com/McLucifer2646/openFASOC_placement/blob/f3e61d224f602e26cdf49927c8a83861c401fef3/Images/Makefile_2.png)


**These steps can be used in the project file by using a python script, present at the following location.**
https://github.com/idea-fasoc/OpenFASOC/blob/main/openfasoc/generators/temp-sense-gen/tools/temp-sense-gen.py

**In this file we have the following snippet explaining the section of the code responsible for PLACEMENT.**

![image](https://github.com/McLucifer2646/openFASOC_placement/blob/f3e61d224f602e26cdf49927c8a83861c401fef3/Images/p1.png)



## OpenFASoC flow for Temperature Sensor Generator

I have considered a pre-existing example of the temp_sense_gen file as provided in the openFASOC repository for showing the workflow of the system. In this project I have majorly worked on the placement of the furnished auxilliary cells optained from my fellow team-mates.

The physical implementation of the analog blocks in the circuit is done using two manually designed standard cells (auxillary cells):<br>
- HEADER cell, containing the transistors in subthreshold operation;
- SLC cell, containing the Split-Control Level Converter.


### HEADER cell in klayout

```
/temp-sense-gen/blocks/sky130hd/gds$ klayout HEADER.gds
```

<p align="center">
  <img src="/Images/Headers.png">
</p><br>

### SLC cell in klayout

```
/temp-sense-gen/blocks/sky130hd/gds$ klayout SLC.gds 
```

<p align="center">
  <img src="/Images/SLC.png">
</p><br>

## Placement using OpenFASOC Flow for Temperature Sensor Generation

Placement takes place after the floorplan is ready and has two phases: global placement and detailed placement. The output of this phase will have all objects placed in their corresponding voltage domain, ready for routing.

### The Global Placement power and area report is shown below:

<p align="center">
  <img src="/Images/global_place_rpt.png">
</p><br>

### The Detailed Placement power and area report is shown below:

<p align="center">
  <img src="/Images/detailed_place_rpt.png">
</p><br>

## Final design after running the entire flow

```
/temp-sense-gen/flow/results/sky130hd/tempsense$ klayout 6_final.gds
```

<p align="center">
  <img src="/Images/Placement.png">
</p>

## Future Works


# Author

- **Anshul Madurwar**

# Acknowledgments

- Kunal Ghosh, Director, VSD Corp. Pvt. Ltd.
- Madhav Rao, Associate Professor, IIIT Bangalore
- Tejas BN, MTech Student, IIIT Bangalore