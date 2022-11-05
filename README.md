# openFASOC - Placement
<h4> Verification of existing openFasoc flow </h4>

FASoC stands for Fully Autonomous System-on-Chip

The FASoC Program is focused on developing a complete system-on-chip (SoC) synthesis tool from user specification to GDSII. FASoC leverages a differentiating technology to automatically synthesize “correct-by-construction” Verilog descriptions for both analog and digital circuits and enable a portable, single pass implementation flow. The SoC synthesis tool realizes analog circuits, including PLLs, power management, ADCs, and sensor interfaces by recasting them as structures composed largely of digital components while maintaining analog performance. They are then expressed as synthesizable Verilog blocks composed of digital standard cells augmented with a few auxiliary cells generated with an automatic cell generation tool. By expanding the IPXACT format and the Socrates tool from ARM, we then enable composition of vast numbers of digital and analog components into a single correct-by-construction design. This project is led by a team of researchers at the Universities of Michigan, Virginia, and Arm.

<h4> Tool installation steps can be found here: </h4>

https://openfasoc.readthedocs.io/en/latest/getting-started.html#installation

You can either follow the express installation or the manual installation steps.

The following tools will be required for complete flow
1. OpenRoad
2. Magic
3. Yosys
4. Klayout
5. Open_PDK
6. Netgen

Note: Miniconda installation might help in easy installation

Make sure to download and install all the required dependencies as mentiioned in the link above.

If you are facing issues in installating Klayout, follow below steps:
1. Go to the below mentioned website and install the suitable klayout version package for your OS
https://www.klayout.de/build.html
2. Follow one of the below for installation
```
sudo dpkg -i <packagename>.deb
cd klayout-*
./build.sh
````
If ./build.sh doesn't work, try ``` sudo ./build.sh ``` or ``` sudo bash ./build.sh ```

OR 

```
sudo apt-get install <packagename>.deb
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



### OpenFASoC flow 

![image](https://user-images.githubusercontent.com/110677094/199426029-2676771d-e8bc-406f-bf9c-22f617aab4aa.png)


<h4> Running the OpenFASOC Flow for temperature sensor generator </h4>

1. Clone the openfasoc git repo using the below command

```
git clone https://github.com/idea-fasoc/openfasoc
```

2. Move to the required temp-sense-gen directory 
```
cd openfasoc/generators/temp-sense-gen
```

3. Give the following commands

```
make

```

## Author 

- **Anshul Madurwar** 


## Contributors 

- **Anshul Madurwar** 
- **Kunal Ghosh** 


## Acknowledgments

- Kunal Ghosh, Director, VSD Corp. Pvt. Ltd.

## Contact Information

- Anshul Madurwar, Integrated MTech Student, International Institute of Information Technology, Bangalore  Anshul.Madurwar@iiitb.ac.in
- Kunal Ghosh, Director, VSD Corp. Pvt. Ltd. kunalghosh@gmail.com