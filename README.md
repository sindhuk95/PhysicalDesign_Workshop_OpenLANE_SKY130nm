# OpenLANE_sky130_PD_workshop
 
This workshop "Advanced Physical Design using OpenLANE/Sky130" is condcuted by VLSI System Design Corporation . In this we generated GDSII from the RTL netlist for PicoRV32a SoC design with Openlane flow using "Google-Skywater's Opensource 130nm Process Design Kit(PDK)". Using sky130 PDK variant, we designed and characterised our own standard cells. OpenLANE runs complete RTL2GDSII flow automatically. Timing Analysis is performed and optimization is carried out for better timing. Design rules checks are performed too.
 
## Table of contents
### DAY 1  Inception of open-source EDA, OpenLANE and SKY130PDK
- HOW TO TALK TO COMPUTERS
   - QFN-48 PAckage, Foundary IPS and Macros
   - RISC-V Instruction set Architecture (ISA)
- SOC DESIGN USING OPENLANE
   - Components of Opensource Digital ASIC Design
   - Simplified RTL2GDS Flow
   - INTRODUCTION TO OPENLANE and STRIVE Chipsets
- Getting Familiar to OpenSource EDA Tools
   - OpenLANE Directory Structure
   - DOCKER, Package and Interactive Mode
   - Design preparation step
   - Synthesis run and Flop ratio
 
### DAY 2 Floorplan and INtrocution to Library Cells
- Floorplanning considerations
   - Utilization factor and Aspect Ratio
   - Pre-placed cells
   - Decoupling capcitors
   - Powerplanning
   - Pin placement 
   - Floorplan run on OpenLANE & review layout in Magic
- Library Binding and Placement
   - Netlist binding and Initial Place Design
   - Optimize Placement
   - Congestion aware Placement
   - Need for libraries and Characterization 
- Cell design and characterization
   - Standard Cell characterization flow
- Timing Characterization
  - Timing Threshold definition
  - Propagation Delay and Transition Time
  
### DAY 3 Design Library Cell using ngspice simulations
- CMOS inverter ngspice simulations
   - IO placer revision
   - SPICE Deck Creation and Simulation for CMOS inverter
   - Switching Threshold Vm
   - Lab steps to git clone vsdstdcelldesign
- Inception of Layout and CMOS Fabrication Process
   - Mask CMOS Fabrication
   - SKY130 basic layer layout and LEF using inverter
   - Designing standard cell and SPICE extraction in MAGIC
- SKY130 Tech File Labs
   - Create Final SPICE Deck
   - Using ngspice for simulation
   - Standard cell characterisation CMOS Inverter
   - LAB exercise and DRC Challenges
      - Intrdocution of Magic and Skywater DRC's
      - Sky130s pdk intro and Steps to download labs
      - Load Sky130tech rules for drc challenges
     
### DAY 4 Pre-layout Timing analysis and CTS
- Timing Analysis and Clock Tree Synthesis (CTS)
   - Standard cell LEF generation
   - Convert Grid into Track info
   - Create Port definition
   - Set Port class and Port use attributes for layout
   - Custom Cell naming and LEF extraction
   - Intergrating custom cell in OpenLANE
   - Delay tables
- Post-synthesis timing analysis Using OpenSTA
- Clock Tree Synthesis using Tritoncts

### DAY 5 Final steps in RTL2GDS
- Maze Routing and Lee's algorithm
- Design Rule Check (DRC)
- Power Distribution Netwrok generation
- Routing
- Features of Tritonroute
- TritonRoute problem statement
- SPEF extraction

# DAY 1 Inception of open-source EDA, OpenLANE and SKY130PDK

List of Topics being covered

1. How to talk to computers
2. SOC design and OpenLANE
3. Open-source EDA tools

Before getting started, let us talk about what is physical design.

   Physical design is the process of transforming a gate level netlist into a physical layout which is manufacturable. This process starts with partitioning of the design followed by floor/power planning, placement of standard cells, clock tree synthesis and routing. 
                   
**Partitioning :** Dividing the design into smaller parts called macros. In this similar functional blocks are located closed to eachother inorder to minimize the connections between the macros.

**Floor planning :** The most critical step in physical design flow. Quality of the chip depends on how good the floorplan is. In this stage we will get to know the shape and size of the die by the aspect ratio and utilization factor. Placement of Macros is done using Macro guide lines. After macros, halos/blockages, I/O pins and pre placed cells are placed.

**power planning :** Inorder to supply power equally to each and every cell in our design, a power grid has been created where power is drawn from the supply to power rings then to stripes and finally through rails to the standard cells.

**Placement :** Here all the standard cells are placed inside the core. By coarse placement, standard cells which are in the modules gets placed roughly in the core and overlapped. In detailed placement, cells sit in the site rows in abutted manner and blockages/ halos help in non-overlapping and congestion.

**Clock Tree Synthesis:** In a sequentinal design, Clock is the main component. Clock is ideal till placement. The main goal of CTS is to propagate the clock to each and every clock sinks with min skew and insertion delay by inserting clock buffers and inverters along the clock path. Clock Balancing is done by H tree methodology inorder to meet timing and power. 

**Routing :** It is connecting the cells physically using metal straps. In this stage actual interconnect delays will be known. Before this stage, connections are logical i.e., trail route has been done. Once detailed routing is done, the design is checked for DRC,LVS. 
Once verification is done, The output of this PnR i.e., GDSII is given to Fabrication team.

Now, lets talk about the list of topics.

# HOW TO TALK TO COMPUTERS.

## QFN-48 PAckage, Foundary IPS and Macros

 QFN-48  Package : Quad flat No Lead 48 package is an IC component best used for proagrammable module and microcomputers with 7 x 7mm body width and 48pins and these pins are driven by the board which is being seated. The Chip sits in the center of the Package. Package is like a case that surrounds the chip to protect it from corrosion or physical damage.

The Chip has various components.

1. Die  : Size of the entire chip and area where I/O pads sit.
2. Pads : Signals travel through pads in and out of the chip.
3. Core : Area in which Macros, standard cells sit and routing happens.

**FOUNDARY** is a factory where chip gets manufatcured. Every information related to to chip depends on this. It is a set of machines where engineers can communicate with factory. **IPS** is **intellectual property** because these blocks need some intelligence to be built and are manufactured using foundry. Hence together called Foundary IPs.

**Macros** are reusable pieces of logic blocks (Intellectual Properties), that can be used in a design without the necessity of building them from scratch. These are like block box. 

![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/47c315a7-20f0-47d3-b9ce-bf95fb704aa6)


##  RISC-V Instruction set Architecture (ISA)  

Bascially, it is the language through which we communicate with the computers. If u want a c program to run on particular layout, we need to follow a particular flow to pass the information from c program to layout.

 ```Compilation from software to Hardware : C Program - Assembly language(RISC-V) - Machine language format(binary Format) - Layout (executed as per requirement)```
 
 For this to be executed, we need a separate interface between the architecture and the layout called Hardware description language. We need to implement the architecture using RTL and PnR converts RTL to Layout(GDS) and we get this layout as output.

lets us take Apps as examples.
how apps runs on the hardware? Between the apps and hardware, system software is where the implementation takes place. The Major components in system software are **OS,Complier,Assembler.**

**OS        :**  Handles IO operations, allocation of memory. It converts the App into its corresponding assmebly language program and then to binary language. The o/p of OS ais low level system functions i.,e C,C++,JAVA.

**Complier  :** The system functions is then given to the complier which converts into set of instructions depending upon the hardware.

**Assembler :** The instruction sets are then converted to binary language and fed to hardware.

This set of instructions is`` INSTRUCTION SET ARCHITECTURE or ARCHITECTURE OF COMPUTER `` and acts as interface between compliers input and hardware. RTL is implemented using this instruction set and synthesize to gate level netlist and then to layout.

![Screenshot (37)](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/2ff54d97-8521-4ba9-9057-f1ff940b6ad3)


# SOC DESIGN USING OPENLANE

## Components of Opensource Digital ASIC Design

Digital ASIC Design in a automated way requires several elements. The elements are
- RTL (Hardware descrption language) Models
- EDA Tools
- PDK Data

we know about RTL and EDA tools. lets talk about PDK data.

**PDK data (Process Design Kit) :** In order to model a fabrication process for the EDA tools we need collection of files. These are included in that kit. It is the interface between designers and fabrication team.
PDK has Design rules, Device models, Digital Standard cell Libraries,I/O lib etc. 

Google worked on an aggreement with skywater to opensoure PDK for the 130nm process by skywater. Google released the first ever opensoure pdk. The pdk has only data information for successful ASIC implementation by openroad or ocla tools

Few Opensources for these three components are 
 
 ```
 RTL Designs :
 librecores.org
 opecores.org
 github.com
 
 EDA Tools :
 Qflow
 OpenROAD
 OpenLANE
 
 PDK DATA
 Google +skywater 130nm
 
 ```
 
 ![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/bd4892f1-756d-4158-83c4-27e780395e2e)

## Simplified RTL2GDS Flow

**Synthesis                     :**  converting RTL into gate level netlist using standard cell libraries.

**FloorPlanning & PowerPlanning :**  Size and shape of the die, Macro placement, I/O pins are done in FP
power Grid is build and through rings,stripes and rails power is evenly distributed. Upper metal layers are used for power routing because of  more width, indeed less resistance so less IR drop and Electron migration

**Placement                     :** According to the netlist through coarse placement, cells are placed in their resepctive locations and cells might overlap.
so we do detailed placement to avoid the overlapping and flipped to save site row area.

**Clock Tree Synthesis          :** Building a clock tree such that clock reached every clock pin of a sequential block with min skew and insertion delay. 

**Routing                       :** After clock, Signal routing takes place. For each metal layer PDK defines thickness, pitch,width, tracks etc.
**SKY130 has 6 layers. The lowest layer is used for interconnects and is made of titanium nitride and all 5 layers are aluminium.**
Most routers are grid routers and metal tracks form a routing grid. since the grid is huge, we use divide and conquer approach.

**Global Routing   :** performed using coarse grained grids generating routing guides. 

**Detailed Routing :** Fine grained grids uses these routing gudies and impplement the actual routing between wires.

Once Routing is done, we do verifictaion during signoff

- **Physical verification :**  
      Design rule checks (DRC) : verifies whether our design meets design rules. (performed by MAGIC)
      Layout vs schematic (LVS) : verifies whether our layout matches with the netlist schematic.(MAGIC AND NETGEN)
- **Timing Verification :**
      Static Timing Analysis : Checks whether our design meets all teh timing constraints and is running with the designated frequency.

## INTRODUCTION TO OPENLANE and STRIVE Chipsets

When using opensource EDA tools, the problem becomes tougher. We need to worry about 
- Tools qualification 
- Tools calibration 
- Missing tools

By releasing OpenLANE PDK, **efabless** decided On creating reference **open source ASIC implementation methodology and flow called **OpenLANE FLOW**. It comes with **APACHE 20. version license**. 

**OpenLANE** has started for true **open-source Tape-out experiment**.

At efabless, family of **open everything SOC's** is called **striVe**.

The members of striVe SOC family
- striVe
- striVe 2, striVe 2a
- striVe 3, striVe3a
- striVe 5
- striVe 6.

Main goal of OpenLANE Asic flow is to have clean GDSII with out any human intervention(no-human-in-loop).
Clean GDSII implies
- No DRC violations
- No LVS violations
- Timing Violations (still work in progress)

It is Tuned for SkyWater 130nm Open PDK.

Can be used to Harden MAcros and Chips ( harden means generate the layout)

Two Modes of operation:
- Autonomous - Push button flow
- Interactive - manually by flow

OpenLANE has Design SPace Exploration which finds the best set of flow configurations

OpenLANE hhas 43 designs with their best configurations.

![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/26160bdb-9d2b-47af-93c1-34f65b3c47a3)


The flow starts with RTL synthesis and ends with final layout in the GDS format.
OpenLANE is based on several OpenSOurce projects such as:
  - OpenROAD
  - Magic VLSI Layout Tool
  - QFlow
  - ABC
  - Fault
  - KLayout
  - Yosys.

Explaining how the flow works:

**RTL** is fed to ***YOSYS*** with the design constraints and translates into netlist then ***optimised and mapped*** into the synthesizable cells from Standard cell libraby using ***ABC***. ABC is used during optimization. 
**Synthesis exploration: When it comes to OpenLANE we have stratagies where some tragets least area and the other on best timing, this is called SYNTHESIS EXPLORATION.

**DESIGN EXPLORATION :**  When you have more no of configurations in our design, the Design exploration does sweeping job and gives an design matrix which has report on violations, cell count, runtime, utilization etc and from that we can get the best set of configurations and clean layout.

**OpenLANE REGRESSION Testing :** The design exploration utility is used for regression testing. OpenLANE already does this so that we can run the experssions on serveal configurations and compare them with the best one. It is something like Iteration.

DFT (DESIGN FOR TEST) : Its an additional testing that is done for correctness of the design by generating test vectors or stimulus. 
- Scan Insertion
- Automatic Test Pattern Generation(ATPG)
- Test Patterns Compaction
- Fault coverage
- Fault simulation

**Physical Implentation :** Implementation is done by OpenROAD APP. we use automated PnR 
- Floor/power planning
- End decoupling capcitors and Tap cells insertion
- PLacement : global and detailed
- Post placement optimization
- Clock Tree Synthesis
- Routing : Global and Detailed

**Logic Equivalence Check :** Since, we do optomization in Physical Implementation, the output netlist from this might be different from the synthesis netlist. Inorder to avoid any further violations functionally, we need to logically check the design before and after PnR. This is known as Logic Equivalence check

**Antenna Rule Violation :** When a metal wire segment is fabricated and its long enough, it act as antenna and collect charges which can damage the transistor  gate that is connected to that wire.
so length of the wire must be limited. Usually this job is done by router, if he fails, there are 2 solutions.

1. **Bridging :** In this we bridge using top layer so we go upto the top layer and drop back to to the metal layer that has long wire segment.
2. **Antenna diode :** Insert the diode next to the transistor that is getting effected by this long wire.

Using OpenLANE we took a preventive approach. we created a fake antenna diode and placed next to every cell during placement. when Magic runs for antenna checkers on the routed layout and reports a violation on a cell input pin, then replace the fake with the real one.

Signoff in openLANE STA is done by openSTA

- physical signoff includes DRC and LVS
- Magic is used for DRC and spice extraction from layout
- Magic and Netgen are used for LVS
- extracted spice by magic vs verilog netlist

# Getting Familiar to OpenSource EDA Tools

## OpenLANE Directory Structure 

- OpenLANE is a flow that comprises of OpenSource tools
- This flow goal is to implement RTL2GDS without human in loop.

Before using linux, commands that we use regularly are

```
cd : change directory, cd .. : change one driectory back
ls -ltr : lists everything in chronological order
cp : copy
clear - clears the screen not delete the content
less : to view a file 
vi : to view and write into the file (or) vim : to view or write into the file
When you open the file to edit, First press esc then i for inserting into the file(edit) and once done with editing press esc again and give
:wq : save write and exit.
:q : just quit
If I want to search anything in  vi or vim editor, use 
/ - searches  the word in the file EX: /sky130_vsdinv - this searches sky130_vsdinv in the entire file. If you have more than one, press n in the vi or vim editor to know the other locations of the search word.
very very important one is ( I felt it important)
find . -name filename - finds the cell in that directory or sub directories. This is just once, may more are available.
If you want to know the usage of the command, just type command --helo
Ex : rm --help
```

The directory which we will be using for entire implementation and invoking of the tool

`` /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane ``

Next comes process related files i.e., is pdk file.

Foundary files are made compatible only with commerical EDA tools. OpenPDKS has mitigated ths issue by implementing scripts and files that convert these foundary level PDKS to be compatible with OpenSource EDA Tools.

In pdks directory .we have one of such variant file  ``sky130A``. 

![Capture6](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/31f77dd0-9705-4a98-9359-523d8bc6a627)

In this we have libs folder, which has information specific to process technology and tools.

![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/ff0267b3-8345-483f-bbb9-3b3ba12178ad)


``sky130_fd_sc_hd`` variant is used for our design ``picorv32a``

Its abbreviation is:
 - sky130 : the process name, 130nm
 - fd     : foundry name of skywater. For example,  OSU is for oklahoma state university
 - sc     : standard cell
 - hd     : high density, variant of PDK

In this folder, we have lib files that has Timing related information like Process corners, PVT corners and lef and tech lef has physical information of cells.

![Capture9](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/729d2198-e976-4750-907f-4086fa9c63cb)


## DOCKER, Package and Interactive Mode 

Before invoking the tool, lets know about **DOCKER**

When a developer develops a machine,its works fine in his system. But when its given to serves or someone else computers even with same performance or optimization, it wont work properly like images are not loaded properly or path is a little bit different. So, the classic problem between developer and production team is `` it works on my machine Why not in yours? , you might not have included the dependencies ``and this debt goes on. For this compatible issues, DOCKER was implemeneted. Docker is compatible with any programming language or project. Our code is wrapped in airtight containers which are portable.

**Docker is a tool which is used to automate the deployement applications in a light weight containers so the application can work effeciently in different enviroments.
 Container is a package that has all the dependencies required to run an application.**
 
 run `` docker`` 
 Once you run this, bash shell opens and we continue our OpenLANE Flow in that

Now, its time to use OpenLANE Flow. Since **OpenLANE is automatic, every stage runs automatically.** To know manually what is happening in each and every stage, we use **OpenLANE in interactive mode** and the flow.tcl has all the information about openlane flow.  

``./flow.tcl -interactive ``


![Capture](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/f0c88fac-963c-41e8-a613-5a38adcfc4d3)


**package** typically contains related programs that can perform different functions. So, input all the packages that are required for the openlane.

``package require openlane 0.9``

Before synthesis, we need to prepare the setup the data structure for our design ``picorv32a``.
We have only three files in our design. Now, we create a file system to flow where each amd every step of flow will be fetching data from a particular created location (``runs``)

## Design Preparation step

``prep -design picorv32a`` 

![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/c48ae1ae-6c87-46c7-aa25-9faa25387aaa)


A sub folder ``run`` has been created in picorv32a with the date on which you did the prepataion of design

![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/0f81080f-5845-459d-9e9d-2c71625504d4)

As you can clearly see the lefs are being merged from sky130A lef files.

![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/4269ab29-6467-43e5-9199-db62416646df)


## Syntnesis run and Flop ratio

Now, ``run_synthesis``

![fifth pic](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/68f57d15-3689-4569-b08e-0c34c9dd6ef9)

After synthesis, we need to calculate the flop ratio, from the statistics report

![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/b1781f83-ce0c-4a67-851f-0883b34a0dd2)

flop ratio = (no of flops)/(total no of cells).

dfxtp2 = 1613
no of cells = 18036
so flop ratio = 0.08943 or 8.943%

# DAY 2 Floorplan and Introcution to Library Cells

# Floorplanning considerations

Few considerations are made before running floorplan. Key parameters are Aspect ratio, Utilization factors, Macro placement using  ```Macro Guide lines```, I/O pin  and Pre-placed cell placement and generating power grid. But In this OpenLANE flow, Power Grid generation comes after post-cts.So, lets take about power grid feneration after post-cts.

## Utilization Factor & Aspect Ratio  

When it comes to floorplanning, the shape and size of the fllorplan is decided by Aspect Ratio and how good the floor plan is decided by Utilisation Factor. They are defined as follows:

``` Utilisation Factor =  (Area occupied by netlist)/(total Area of the core)```
                   
``` Aspect Ratio = (height/width) ```

A Utilisation Factor of 1 signifies 100% utilisation leaving no place for routing and extra logic. However, In real scenario, the Utilisation Factor will usually be  0.5-0.6 ie., 50 to 60% of the area is used for macros, standard cells and rest is used for routing, extralogic. Likewise, an Aspect ratio of 1 signifies that the chip is square shaped. Any value other than 1 signifies rectanglular chip.

## Pre-placed cells 

Once the Utilisation Factor and Aspect Ratio has been decided, the locations of pre-placed cells need to be defined. lets say we have  a combinational logic which does some function, maybe clock buffers, complex cells like clock gating cell, mux, memory. so these cells are implemented separately. Pre-placed cells are IPs and have user defined locations hence are fixed in those locarions. Since they are placed before automated placement and routing, they are called pre-placed cells.

## Decoupling capacitors

Once we place Pre-placed cells, these must be surrounded with decoupling capacitors (decaps). lets say we have logic cell that needs transition. The capcitor demands a huge current and this is provided by the power supply through physical wires. As we know, Physical wires have resistance, capaictance and inductance in it. when the signal/supply voltage travserse through these long wires, they will be voltage drop and the intende supply cannot reach the logic cell that is transitioning.The voltage can lead to undefined region in noise margin. To avoid this, Decaps are invented. These are placed very close to the critical cells.  Decaps are fully charged and have equivalent voltage as of the power supply. Since Decap is close to the logic, its hard for the voltage to drop and the lies with in the Noise Margin range. Their role is to decouple the circuit from power supply by supplying the necessary amount of current to the circuit. They pervent crosstalk.

## Power Planning

As we know, there are millions of cells in our design and its hard to supplu power equally to all over the design. However we cannot place decaps cells everywhere in the design.  So, separate horizontal and vertical Vdd and Vss wires have been created forming like a mesh. Now, each cell can tap the required supply from the nearest wire. They way the power is supplied to all the standara cells is through rings,stripes and followpins.

## Pin Placement

The netlist is the logical connectivity between cells. The area between the core and die is utilised for placing I/O pins. The connectivity information is defined in either VHDL or Verilog and is used to determine the position of I/O pins of various ports. Then, logical cell placement is bloackage is done inorder to avoid any cells getting placed by automated PnR.

## Floorplan run on OpenLANE & review layout in Magic

**Floorplan envrionment variables or switches:**
1. ```FP_CORE_UTIL``` - core utilization percentage
2. ```FP_ASPECT_RATIO``` - the cores aspect ratio
3. ```FP_CORE_MARGIN``` - The length of the margin surrounding the core area
4. ```FP_IO_MODE``` - defines pin configurations around the core(1 = randomly equidistant/0 = not equidistant)
5. ```FP_CORE_VMETAL``` - vertical metal layer where I/O pins are placed
6. ```FP_CORE_HMETAL``` - horizontal metal layer where I/O pins are placed
 
***Note: Usually, the parameter values for vertical metal layer and horizontal metal layer will be 1 more than that specified in the files***

**Importance files in increasing priority order:**
1. ```floorplan.tcl``` - System default settings
2. ```conifg.tcl```
3. ```sky130A_sky130_fd_sc_hd_config.tcl```
 
 To run the picorv32a floorplan in openLANE:
 
 ```
 run_floorplan
 
 ```
 
![Capture10](https://github.com/sindhuk95/later/assets/135046169/cb20dfe1-29f5-40dd-a5c9-af690a1b9a04)
 
Post the floorplan run, a .def file will have been created within the ```results/floorplan``` directory. We may review floorplan files by checking the ```floorplan.tcl.``` The system defaults will have been overriden by switches set in conifg.tcl and further overriden by switches set in ```sky130A_sky130_fd_sc_hd_config.tcl.```

To view the floorplan, Magic is invoked after moving to the results/floorplan directory:

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def

```

![1](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/8fb94de1-b7db-4bec-a5ed-e770539777c1)


One can zoom into Magic layout by selecting an area with left and right mouse click followed by pressing "z" key.

Various components can be identified by using the what command in tkcon window after making a selection on the component.

Zooming in also provides a view of decaps present in picorv32a chip.

The standard cell can be found at the bottom left corner.

You can clearly see I/O pins, Decap cells and Tap cells. Tap cells are placed in a zig zag manner or you can say diagonally


![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/3a55d83e-1776-4b96-a55c-f1cf00d823bb)


# Library Binding and Placement

## Netlist Binding and initial place design

First we need to bind the netlist with physical cells. We have shapes for OR, AND and every cell for pratice purpose. But in reality we dont have such shapes, we have give an physical dimensions like rectangles or squares weight and width. This information is given in libs and lefs. Now we place these cells in our design by initilaising it. 

## Optimize Placement

The next step is placement. Once we initial the design, the logic cells in netlist in its physical dimisoins is placed on the floorplan. Placement is perfomed in 2 stages:

Global Placement: Cells will be placed randomly in optimal positions which may not be legal and cells may overlap. Optimization is done through reduction of half parameter wire length.
Detailed Placement: It alters the position of cells post global placement so as to legalise them.
Legalisation of cells is important from timing point of view.

Optimization is stage where we estimate the lenght and capictance, based on that we add buffers. Ideally, Optimization is done for better timing.

## Congestion aware Placement 

Post placement, the design can be viewed on magic within results/placement directory:

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def

```

![Capture1](https://github.com/sindhuk95/SKY130_PD_WS_DAY2/assets/135046169/63fb5104-9b15-4976-ac85-b5cb84d0273b)


**Note: Power distribution network generation is usually a part of the floorplan step. However, in the openLANE flow, floorplan does not generate PDN.  It is created after post CTS. The steps are - floorplan, placement, CTS, Post CTS and then PDN**

## Need for libraries and characterization

As we know, From logic synthesis to routing and STA, each and evry stage has one thing in common i.e., logic gates/ logic cells. In order for the tool understand these gates are and their timing, we need to characterize these cells. 

# CELL DESIGN AND CHARACETRIZATION FLOWS

Library is a place where we get information about every cell. It has differents cells with different size, functionality,threshold voltages. There is a typical cell design flow steps.
1. Inputs : PDKS(process design kit) : DRC & LVS, SPICE Models, library & user-defined specs.
2. Design Steps :Circuit design, Layout design (Art of layout Euler's path and stick diagram), Extraction of parasitics, Characterization (timing, noise, power).
3. Outputs: CDL (circuit description language), LEF, GDSII, extracted SPICE netlist (.cir), timing, noise and power .lib files

## Standard Cell Characterization Flow

A typical standard cell characterization flow that is followed in the industry includes the following steps:

1. Read in the models and tech files
2. Read extracted spice Netlist
3. Recognise behavior of the cells
4. Read the subcircuits
5. Attach power sources
6. Apply stimulus to characterization setup
7. Provide neccesary output capacitance loads
8. Provide neccesary simulation commands

Now all these 8 steps are fed in together as a configuration file to a characterization software called GUNA. This software generates timing, noise, power models.
These .libs are classified as Timing characterization, power characterization and noise characterization.

![image](https://github.com/sindhuk95/later/assets/135046169/87348350-fa25-4ef8-99f4-1cdddf070f10)

# TIMING CHARACTERIZATION

In standard cell characterisation, One of the classification of libs is timing characterisation.

## Timing threshold definitions 
Timing defintion |	Value
-------------- | --------------
slew_low_rise_thr	| 20% value
slew_high_rise_thr | 80% value
slew_low_fall_thr |	20% value
slew_high_fall_thr |	80% value
in_rise_thr	| 50% value
in_fall_thr |	50% value
out_rise_thr |	50% value
out_fall_thr | 50% value

## Propagation Delay and Transition Time 

**Propagation Delay** 
The time difference between when the transitional input reaches 50% of its final value and when the output reaches 50% of its final value. Poor choice of threshold values lead to negative delay values. Even thought you have taken good threshold values, sometimes depending upon how good or bad the slew, the dealy might be still +ve or -ve.

```
Propagation delay = time(out_thr) - time(in_thr)
```
**Transition Time**

The time it takes the signal to move between states is the transition time , where the time is measured between 10% and 90% or 20% to 80% of the signal levels.

```
Rise transition time = time(slew_high_rise_thr) - time (slew_low_rise_thr)

Low transition time = time(slew_high_fall_thr) - time (slew_low_fall_thr)
```

# DAY3 Design Library Cell using ngspice simulations

# CMOS inverter ngspice simulations
``ngspice``  is opesoure engine where simulations are done.

### IO Placer revision

 - PnR is a iterative flow and hence, we can make changes to the environment variables in the fly to observe the changes in our design. 
 - Let us say If I want to change my pin configuration along the core from equvi distance randomly placed to someother placement, we just set that IO mode variable on command prompt as shown below
 ```
 set ::env(FP_IO_MODE) 2
```

## SPICE Deck Creation and Simulation for CMOS inverter

- Before performing a SPICE simulation we need to create SPICE Deck
SPICE Deck provides information about the following:
- Component connectivity - Connectivity of the Vdd, Vss,Vin, substrate. Substrate tunes the threshold voltage of the MOS.
- component values - values of PMOS and NMOS, Output load, Input Gate Voltage, supply voltage.
- Node Identification and naming - Nodes are required to define the SPICE Netlist
     For example ```M1 out in vdd vdd pmos w = 0.375u L = 0.25u``` , ```cload out 0 10f```
- Simulation commands
- Model file - information of parameters related to transistors
Simulation of CMOS using different width and lengths. From the waveform, irrespective of switching the shape of it are almost same.

- ![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/5b494ae5-341a-41ff-a2bb-1db066fa2b72)
- 
From the waveform we can see the characteristics are maintained  across all sizes of CMOS. So CMOS as a circuit is a robust device hence use in designing of logic gates. Parameters that define the robustness of the CMOS are

## Switching Threshold Vm

- The Switching Threshold of a CMOS inverter is the point where the Vin = Vout on the DC Transfer characreristics. 
- At this point, both the transistors are in saturation region, means both are turned on and have high chances of current flowing driectly from VDD to Ground called Leakage current.
 
![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/3393442e-1b4b-434a-bc7e-6e2ed4fde218)

Through transient analysis, we calculate the rise and fall delays of the CMOS by SPICE Simulation. As we know delays are calculated at 50% of the final values.


## Lab steps to git clone vsdstdcelldesign

- First, clone the required mag files and spicemodels of inverter,pmos and nmos sky130. The command to clone files from github link is:
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
once I run this command, it will create ``vsdstdcelldesign`` folder in openlane directory. I have got an error saying `` could not  resolve host: github.com`` 
somehow I figured it was due incorrect proxy, so I used this command  `` git config --global --unset https:proxy``. It worked and could clone the files from github to my openlane.

![day3-1](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/5fcb599b-48bf-4667-995c-2ff99e7b6358)

The information of inverter layout is mentioned in ``sky130_inv.mag`` file.

![day3-7](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/d42a28e9-34a0-46c7-8799-03c668709207)

Inorder to open the mag file and run magic, we need tech file for magic in that location. So, lets copy the tech file from magic which is in pdks directory to vsdstdcelldesign folder
command to copy the file is
``` cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/ ```
once we run this command, the tech file will be copied to vsdstdcelldesign folder.

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/172d04b0-5701-4bdd-b020-e32775932bf4)

For layout we run magic command
``` magic -T sky130A.tech sky130_inv.mag & ```
Ampersand at the end makes the next prompt line free, otherwise magic keeps the prompt line busy. Once we run the magic command we get the layout of the inverter in the magic window

![day3-3](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/47174505-3cbc-4110-b342-317eef4d849d)

Press G in the magic console and you can see the grids being disaplyed

![size of row](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/ec4a0ff1-9009-4365-b66c-9e352ba1842a)

# Inception of Layout and CMOS Fabrication Process

## Mask CMOS Fabrication

The 16-mask CMOS process consists of the following steps:
- **Selecting the subtrate**
- **Creating actove region for Transistors** -  Isolation between active region pockets by depositing the oxide layer SiO2, silicon nitride Si3N4,then photoresist and masking the layers followed by photolithography and etch of the silicon nitride and remove photoresist. then this is placed in oxidation furnace, helps in growing oxide layer in other regions. This process is called LOCOS "Local oxidation of silicon" and lastly we strip out Si3N4 using hot phosphoric acid.
- **N-well and P-well formation**: Both wells are created separately. First Pwell is created in steps like Photoresist, mask, photolithpgraphy and a p-type Boron material is diffused into psubstrate using Ion Implantation, forming P-well and same goes for n-well formation by diffusing a n-type Phosphorous material. Till now wells are created, by using High Temperature furnace process, drive-in dussion happens and  creating depths of wells and this process of creating is called tub process.
- **Formation of gate** - Gate is the most important terminal of CMOS transistor, where you control the threshold voltages and these voltages turns off/on the transistor.polysilicon layer is depositied and photolithography techniques are perfomed created NMOS and PMOS gates. The two parameters for gate formation are
   - Oxide capacitance
   - doping concentration    
- **lightly doped drain(LDD) formation** - To prevent hot electron effect and short channel effect
- **Source & drain formation**: In this, a thin layer of oxide is added to avoid the effect of channel during implants. It is where the vector velocity of ions matches with that of the crystalline structure of the p substrate and go directly into the substrate without hitting any silicon atom, when we do alot of ion implanatation. N+,P+ implants are done by Aresenic implantation and Hight temperature annealing.
- **Local interconnect formation**: Remove the thin screen oxide by etching in (hydrofloric) HF solution. There are various step to form local interconnects. One is
- **deposit titanium on wafer using supttering** - Once it is depoisted and heating is done in N2 ambient, chemical reaction happens creating two kind of metal contacts, titanium silicon dioxide which are low resistant,used for interconnect contacts and titanium nitride(etched using RCA cleaning) used for the connection you want bring to the top,local communication
- **Higher level metal formation**: Non Planar surface topography is not suitable for metal interconnects. Planarize the surface by doping silicon oxide with Boron or Phosphorous to polish the surface. This technque is called chemical Mechanical polishing (CMP). Then we perform Photolithography techniques follwed by  TiN and balnket Tungsten layer deposition and do CMP. Deposit AL layer and repeat photolithography and CMP. This is 1st level of interconnect. Add another layer of interconnects to take to higher level metal layers. Lastly, Layer a dielectric i.e., Si3N4 to protect the chip. 

 ![Screenshot (34)](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/ae5675b9-5b61-4472-81c5-c0cb8f282529)
 
## SKY130 basic layer layout and LEF using inverter

- From Layout, we see the layers which are required for CMOS inverter. Inverter is, PMOS and NMOS connected together.
- Gates of both PMOS and NMOS are connected together and fed to input(here ,A), NMOS source connected to ground(here, VGND), PMOS source is connected to VDD(here, VPWR), Drains of PMOS and NMOS are connected together and fed to output(here, Y). 
The First layer in skywater130 is ``localinterconnect layer(locali)`` , above that metal 1 is purple color and metal 2 is pink color.
If you want to see connections between two different parts, place the cursor over that area and press S three times. The tkson window gives the connectivity information.

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/fc51c2de-26f5-4ada-8c39-f5f29604999b)

### Library exchange format (.lef)

- The layout of a design is defined in a specific file called LEF.
-  It includes design rules (tech LEF) and abstract information about the cells. 
    - ```Tech LEF``` -  Technology LEF file contains information about the Metal layer, Via Definition and DRCs.
    - ```Macro LEF``` -  Contains physical information of the cell such as its Size, Pin, their direction.
 
## Designing standard cell and SPICE extraction in MAGIC 

-  First we need to provide bounding box width and height in tkson window. lets say that width of BBOX is 1.38u and height is 2.72u. The command to give these values to magic is
   ``` property Fixed BBOX (0 0 1.32 2.72)  ```
- After this, Vdd, GND segments which are in metal 1 layer, their respective contacts and atlast logic gates layout is defined
Inorder to know the logical functioning of the inverter, we extract the spice and then we do simulation on the spice. To extract it on spice we open TKCON window, the steps are
- Know the present directory - ``pwd ``
- create an extration file -  the command is  `` extract all `` and  ``sky130_inv.ext`` files has been created
          
- create spice file using .ext file to be used with our ngspice tool  - the commands are  
      ``` ext2spice cthresh 0 rthresh 0 ``` - extracts parasatic capcitances also since these are actual layers - nothing is created in the folder
      ``` ext2spice ``` - a file ```sky130_inv.spice``` has been created.
      
![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/80394fcd-2b43-49fb-b51b-af80304888a0)
     
# SKY130 Tech File Labs
     
## Create Final SPICE Deck 
  
let us see what is inside the spice Deck
In the spice file subcircuit(subckt), pmos and nmos node connections are defined
   
For NMOS  ``` XO Y A VGND VGND sky130_fd_pr_nfet_01v8 ``` . The order is  ``` Cell_name Drain Gate Source Substrate model_name ``` .
For PMOS  ``` X1 Y A VPWR VPWR sky130_fd_pr_pfet_01v8 ``` . The order is   ``` cell_name Drain Gate Source Substrate model_name ```.
   
For transient anaylsis, we would like to define these following connections and extra nodes for these in spice file
  - VGND to VSS
  - Supply voltage from VPWR to Ground - extra nodes here will be 0 and VDD with a value of 3.3v 
  - sweep in/pulse between A pin and VGND (0)
Before, editing the file, make sure scaling is proper, we measure the value of the gride size from the magic layout and define using `` .option scale=0.01u`` in the Deck file.
  
Now keeping the connection in mind, define the required commands in the file. Along with this we need to include libs for nmos ``nshort.lib`` and pmos ``pshort.lib`` and define transient analysis commands too. We comment the subckt since we are trying to input the controls and transient analysis also. Model names are changed to ``nshort_model.0`` and ``pshort_model.0`` according to the libs of nmos and pmos.
  
These voltage sources and simulation commands are defined in the Deck file.
   ``
   VDD VPWR 0 3.3V
   VSS VGND 0 0V
   Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)
   .tran 1n 20n
   .control
   run
   .endc
   .end
   ``
  
![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/f55eb2a7-0251-4fbd-9b40-24a8088f3bbb)

## Using ngspice for spice simulation
  
Spice Deck is done and now to run spice simulation invoke ngspice in the tool and pass the source file. 
 
  ``` ngspice sky130_inv.spice ```
  
On the prompt you can see the values the ngspice has taken. To see the plot, use
   
   ``` plot y vs time a ``` .
   
Now we have the transient response, the next objective is to characterise the cell.
   
    
[image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/603a0396-bd48-4d2d-a74e-34815ab26f94)
     
## Standard cell characterization of CMOS Iinverter 
 
characterization of the inverter standard cell depends on Four timing parameters
 
 **Rise Transition**: Time taken for the output to rise from 20% to 80% of max value
 **Fall Transition**: Time taken for the output to fall from 80% to 20% of max value
 **Cell Rise delay**: difference in time(50% output rise) to time(50% input fall)
 **Cell Fall delay**: difference in time(50% output fall) to time(50% input rise)
 
 The above timing parameters can be computed by noting down various values from the ngspice waveform.
 
 ``` Rise Transition : 2.25421 - 2.18636 = 0.006785 ns / 67.85ps ```
 ``` Fall Transitio : 4.09605 - 4.05554 = 0.04051ns/40.51ps ```
 ```Cell Rise Delay : 2.21701 - 2.14989 = 0.06689ns/66.89ps ```
 ```Cell Fall Delay : 4.07816 - 4.05011 = 0.02805ns/28.05ps ```
 
## LAB exercise and DRC Challenges

## Intrdocution of Magic and Skywater DRC's

  - In-depth overview of Magic's DRC engine
  - Introduction to Google/Skywater DRC rules
  - Lab : Warm-up exercise : Fixing a simple rule error
  - Lab : Main exercie : Fixing or create a complex error
 
Before doing the lab, Get the knowledge of how Magic performs DRc's and Syntax for DRC rules  from this URL: ``` http://opencircuitdesign.com/magic```.
Nothing works if there is no Technology file. Fortunatley by Google/ Skywater, we have the opportunity to use the fabication process technology now made as an open source.

skywater sky130 pdk URL : ``` https://skywater-pdk.readthedocs.io/en/main/ ```

The two main sources of information PDK:
  - github.com - ```https://github.com/google/skywater-pdk``` - understand the design rules 
  - Documentation - ``` https://skywater-pdk--136.org.readthedox.build```
  
Since github is not used for tutorial and documentation is critical for lab since browser needs to be open for the lab.
  
## Sky130s pdk intro and Steps to download labs
  
  - setup to view the layouts
  - For extracting and generating views, Google/skywater repo files were built with Magic
  - Technology file dependency is more for any layout. hence, this file is created first.
  - Since, Pdk is still under development, there are some unfinished tech files and these are packaged for magic along with lab exercise layout and bunch of stuff into the tar ball
 
We can download the packaged files from web using ``wget `` command. wget stands for web get, a non-interactive file downloader command.
  
  ``` wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz```
  
The archive file drc_tests.tgz is downloaded into our user directory 
  
![wget](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/3358159c-b338-43f4-b811-58a11284e75b)

Now, we need to unzip the archive file. For this we use tar utility command. 

`` tar - tap archiver`` create and extract a tar archive file.

The command to extract the tar file is 

`` tar -xfz drc_tests.tgz``

once extraction is done, drc_tests file is created and you will have all the information about magic layout for this lab exercise

![tar](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/6c27f1d8-57ce-4796-bbc5-9345a4843920)

Now run MAGIC

For better graphics use command ``magic -d XR ``

Now, lets see an example of simple failing set of rules of metal 3 layer.  you can either run this by magic command line `` magic -d XR metal3.mag `` or from the magic console window, `` menu - file - open -load file9here, metal3.mag) ``

![ms](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/ac4b681b-b486-4db0-9eef-23f532d5b685)

We have few drc errors and calls out a rule number. You can see these rules on google skywater pdk read the doc page.
From metal 3.4 drc rules, Via2 must be enclosed by metal3 by atleast 0.065u. 
Magic tablet is of using many derived layers and in which via is one. Via represents the area filled with contact cuts.
Draw a large area of M3 contatc using mouse pointer hovering over the m3contact icon on the side toolbar. Now with cursor box around the m3 area, type command
``` cif see VIA2 ``` . This create contatc cuts which are bloack sqaure shaped. 
These dont exist on the drawn layout, but they represent as mask layer VIA2, that will end up in the GDS
Futher details about these are metioned in cif output section in tech file.
The view we see is feedback entry and can dismiss it with `` feed clear `` We use snap internal command `` snap int `` for the cursor to move along the edge of the grid.
There are few spacing rukes metioned like spacing between Via2 and metal 3 is 0.065u. 
If we put the curosor between the contact cut and drawn via edge and click box in tkcon command, we will get the distance.
The tool automatically places and do spacing of the contact, there wont be any DRC errors and the distance will not be smaller than the specified one.

![area via2](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/25b99b94-8382-4ca4-b214-1c844ae8dfe8)

## Load Sky130 tech rules for drc challenges 

First load the poly file by ``load poly.mag`` on tkcon window.

Finding the error by mouse cursor and find the box area, Poly.9 is violated due to spacing between polyres and poly.

![polyres to poly](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/4adfd293-2ff0-408e-a657-1e56bffeccbf)

View the tech file and search whether we have created any drc rule for poly.9. If there is none, create a rule defining spacing between polyres and poly.
This is how we add spacing for all the polyres

![earlier for diff tap](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/74529987-6524-4ecd-9cdf-1598f7d7adb1)

Once the tech file has been modified for drc, we load the tech file in tkcon window and tool doesnot check drc automatically so we need to check drc too. Once loaded, the violations are reduced since spacing rule is met.
`` tech load sky130A.tech `` followed by `` drc check ``

![poly to diff](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/b6ec1559-9d32-464b-8d02-a3593a2033df)

Now, poly.9 is spacing between polyres to poly and poly to diff/tap. Once we resolve the polyres to poly, poly to tap / diff got violations by providing spacing for tap/diff. in tech, 

Again load the tech file, check drc and the issue will be solved.

# DAY 4 Pre-layout Timing analysis and CTS

# Timing Analysis and Clock Tree Synthesis (CTS)

## Standard Cell LEF generation

During Placement, entire mag information is not necessary. Only the PR boundary, I/O ports, Power and ground rails of the cell is required. This information is defined in LEF file.
The main objective is to extract lef from the mag file and plug into our design flow.

## Grid into Track info

 **Track** :A path or a line on which metal layers are drawn for routing. Track is used to define the height of the standard cell. 

To implement our own stdcell, few guidelines must be followed 
 - I/O ports must lie on the intersection on Horizontal and vertical tracks
 - Width and Height of standard cell are odd mutliples of Horizontal track pitch and Vertical track pitch

This information is defined in ``tracks.info``. The syntax is ``` metal_layer direction offset spacing ```

```
li1 X 0.23 0.46 
li1 Y 0.17 0.34
```
  
![4-1](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/0b1a5578-e3a4-41b4-b8c5-6ce8c4414228)

lets say the I/O ports are in ```li1 layer``` and check whether they are at intersection or not by using ```grid 0.46um 0.34um 0.23um 0.17um``` on tkcon window and to activate the grid, press G on the magic console.
 
Now, check whether the width and height of std cell met by measuring the tracks the cells has occupied.
 
![frustated](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/319aa4c7-6e1e-4273-b9cd-ba10adfc4c73)


## Create Port Definition: 

However, certain properties and definitions need to be set to the pins of the cell. For LEF files, a cell that contains ports is written as a macro cell, and the ports are the declared as PINs of the macro.

The way to define a port is through Magic console and following are the steps:
- In Magic Layout window, first source the .mag file for the design (here inverter). Then Edit >> Text which opens up a dialogue box.
- When you double press S at the I/O lables, the text automatically takes the string name and size. Ensure the Port enable checkbox is checked and default checkbox is unchecked as shown in the figure:

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/8aee236b-632c-41ae-8465-db34b3783501)

- In the above figure, The number in the textarea near enable checkbox defines the order in which the ports will be written in LEF file (0 being the first).

-  For power and ground layers, the definition could be same or different than the signal layer. Here, ground and power connectivity are taken from metal1 (Check by using
what command on tkcon once you make box on VPWR and VGND on magic window)

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/e14054c8-114e-46b2-a14d-7a6ed17763c5)

## Set port class and port use attributes for layout 

After defining ports, the next step is setting port class and port use attributes.

Select port A in magic:
```
port class input
port use signal
```
Select Y area
```
port class output
port class signal
```
Select VPWR area
```
port class inout
port use power
```
Select VGND area
```
port class inout
port use ground

```

## Custom cell naming and lef extraction.

Name the custom cell through tkcon window as ```sky130_vsdinv.mag```.

![4-8 our cell](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/68fc0578-dd29-45c5-a6ee-d64cbc02a60b)

this gets created in ```openlane/vsdstdcelldesign``` directory.

![4-7 custon name for cell](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/7c2aebaf-5e2a-4e26-800f-b2e04d340e3f)


LEF extraction can be carried out in tkcon as follows:
```
lef write

```
![lef write](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/9cc3b2a1-c0c1-4190-a220-fcfd13ad6723)

This generates ```sky130_vsdinv.lef``` file.

![4-9 lef file created](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/fabbb30e-fb9f-4a9a-bb98-c7de461cac54)

## Integrating custom cell in OpenLANE

In order to include our custom standard cell to ur design and run the first step synthesis, copy the``` sky130_vsdinv.lef``` file to the ``designs/picorv32a/src directory.``
Since abc maps the standard cell to a library, there must be a library that defines the CMOS inverter. The ```sky130_fd_sc_hd_typical.lib``` as well as fast and slow libs files from ```vsdstdcelldesign/libs``` directory needs to be copied to the ```designs/picorv32a/src``` directory. If you are interested to check our cell in lib and lef, go to lib and lef folders

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/77f8b542-d996-4060-956f-c02283e2f5b0)


![4-10 lef and lib file to picrov32a](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/685c25b1-c3f1-43f2-b7e0-1985cd341531)


Next, modifiy the ``config.tcl`` by adding few extra definitions like abc synthesis mapping to typical.lib, other three are used for sta analysis and to include extra lefs(our design lef) to flow

```
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__typical.lib"
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/d6414049-bbc3-4c80-b516-7b616afa973d)

Before this, lets change the env variable strategy for synth in `` configuration/synthesis.tcl ``  from `` AREA 0 to DELAY ``


In order to integrate the standard cell in the OpenLANE flow, invoke openLANE as usual and carry out following steps:
``` 
prep -design picorv32a -tag 03-06-08-35 -overwrite 
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
```
After synthesis, my timing is clean.

## Delay Tables

Basically, Delay is a parameter that has huge impact on our cells in the design. Delay decides each and every other factor in timing. 
For a cell with different size, threshold voltages, delay model table is created where we can it as timing table.
```Delay of a cell depends on input transition and out load```. 
Lets say two scenarios, 
we have long wire and the cell(X1) is sitting at the end of the wire : the delay of this cell will be different because of the bad transition that caused due to the resistance and capcitances on the long wire.
we have the same cell sitting at the end of the short wire: the delay of this will be different since the tarn is not that bad comapred to the earlier scenario.
Eventhough both are same cells, depending upon the input tran, the delay got chaned. Same goes with o/p load also.

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/d6a9ee55-39c4-46b1-a8c8-f4695758621e)


Just for better values for slack, I checked few synthesis environment variables like synthesis strategy, synthesis buffering and synthesis sizing , maximum fanout of cells and made changes if neccessary.

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/0296f08a-fdab-4c22-9ec4-d4b04ee78d2c)

run synthesis if you have violations for better slack

Next floorplan is run, followed by placement:
```
run_floorplan

```
Since we are getting an error shown in the below, 

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/9cd41112-43d3-46e7-98f3-5008a7afe8ab)

we gave commands for each stage manually step by step after synthesis.  
```
init_floorplan
place_io
global_placement_or
tap_decap_or
```
   
During floorplan, ``` tap cells - 7892, Endcap cells - 550 ``` got placed. Design has ``` 275 original rows ```.

Now Instead of run_placement, we get 
``
   detailed_placement
``

After placement, we check for legality. I even check how many cells got placed.

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/91b062d3-946c-4893-af99-ff826db25b45)

To check the layout invoke magic from the ```results/placement``` directory:

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

Since the custom standard cell has been plugged into the design,in the openlane flow, it would be visible in the layout.

![cell in our design](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/f36a0c82-cd99-4850-b1ec-3a4e9114b747)

# Post-synthesis timing analysis Using OpenSTA

Timing analysis is carried out outside the openLANE flow using OpenSTA tool. For this, ```pre_sta.conf``` is required to carry out the STA analysis. Invoke OpenSTA outside the openLANE flow as follows:
```
sta pre_sta.conf
```
![sta config](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/9beaddcf-52d3-4a60-bc58-09cc714734c1)

sdc file for OpenSTA is modified like this:

base.sdc is located in vsdstdcelldesigns/extras directory.
So, I copied it into our design folder using

``` cp my_base.sdc /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/ ```

![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/e2e1e152-d5b5-41ad-9670-ae8a88d04d28)

Since I have no Violations I skipped this, but have hands on experience on timing analysis using OpenSTA.

Since clock is propagated only once we do CTS, In placement stage, clock is considered to be ideal. So only setup slack is taken into consideration before CTS.

``` 
Setup time: minimum time required for the data to be stable before the active edge of the clock to get properly captured.

Setup slack : data required time - data arrival time 

```

clock is generated from PLL which has inbuilt circuit which cells and some logic. There might variations in the clock generation depending upon the ckt. These variations are collectivity known as clock uncertainity. In that jitter is one of the parameter. It is uncertain that clock might come at that exact time withought any deviation. That is why it is called clock_uncertainity
Skew, Jitter and Margin comes into clock_uncertainity

```  Clock Jitter : deviation of clock edge from its original position. ```

From the timing report, we can improve slack by upsizing the cells i.e., by replacing the cells with high drive strength and we can see significant changes in the slack.

#  Clock Tree Synthesis using Tritoncts

In this stage clock is propagated and make sure that clock reaches each and every clock pin from clock source with mininimum skew and insertion delay. Inorder to do this, we implement H-tree using mid point strategy. For balancing the skews, we use clock invteres or bufferes in the clock path. 
Before attempting to run CTS in TritonCTS tool, if the slack was attempted to be reduced in previous run, the netlist may have gotten modified by cell replacement techniques. Therefore, the verilog file needs to be modified using the ```write_verilog``` command. Then, the synthesis, floorplan and placement is run again. To run CTS use the below command:
```
run_cts
```

After CTS run, my slack values are

``setup : 4.33 , Hold : 0.25``

Since, clock is propagated, from this stage, we do timing analysis with real clocks. From now post cts analysis is performed by operoad within the openlane flow

```
openroad
write_db pico_cts.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/03-07_11-25/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock (all_clocks)
report_checks -path_delay min_max -format full_clock_expanded -digits 4
``` 

After performing Timing analysis, my slack values are

``` Setup : 4.0565 , Hold : -0.1673```.

![buff with more delay](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/396e9062-6818-4740-bbfa-b36efcc158c6)

After CTS, my hold slack is getting violated. In order to fix that, I checked the cell that has more delay from the report from the above picture and changed it to the cell that has more drive strength.
we exit from openROAD and give these commands

![buff list change](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/a91c21d5-f800-448d-8be9-39f5d0bb050b)

```
echo $::env(CTS_CLK_BUFFER_LIST)
set $::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]
echo $::env(CTS_CLK_BUFFER_LIST)
```
After changing the files, load the placement stage def file and run cts again. 
Now, again run OpenROAD and create another db and everything else is same.
Report after post_cts is

``` Setup slack - 4.2379 , Hold slack - 0.1169 ```

# Final steps in RTL2GDS

## Maze Routing and Lee's algorithm

Routing is a physical connection between two pins. Algorithm takes the pins as source and target and finds the best possible connection between them and guarantees to find a route between two points if the connection exists.

One such alogirthm is ```The Maze Routing algorithm``` and the ```Lee algorithm``` is one possible solution for Maze routing problems. 

Earlier we have created a grid during customising the cell. In the same way floorplan creates a grid that is used for routing. Lee algorithm  creates two points, source and target and using routing grid it searches for the shortest/best path between two them.

It creates labels to the adjacent grids around the source from 1 till it reaches the target(lets say 7 for ex). There will be so many paths from 1 to 7 like L shaped, zigzag shaped. Lee alogirthm prefers the best path with not zig zags i.e., L shaped. In case there are no L shaped paths, it has to take the zig zag path. This is used for global routing.

They are limitations to it. First it creates maze and then starts numbering from source moves to target. It is easy to to route from two pins, but it is timing consuming for millions of the pins. There are other alogorithm in the same manner. 

![lee route](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/57abf172-7ec2-4725-929b-eb48268c5d1f)

## Design Rule Check (DRC)

DRC verifies whether a design meets the predefined process technology rules given by the foundry for its manufacturing. DRC checking is an essential part of the physical design flow and ensures the design meets manufacturing requirements and will not result in a chip failure.
It defines the ``Quality of chip``. They are so many DRCs, let us see few of them

Design rules for physical wires
- Minimum width of the wire
- Minimum spacing between the wires
- Minimum pitch of the wire
To solve signal short violation, we take the metal layer and put it on to upper metal layer. we check via rules
- Via width
- via spacing

![drc](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/72016ae6-ef19-407a-8385-5f4883b45806)

## Power Distribution Network generation

Unlike the general ASIC flow, Power Distribution Network generation is not a part of floorplan run in OpenLANE. PDN must be generated after CTS and post-CTS STA analyses:
```
gen_pdn
```
we can check whether PDN has been created or no by check the current def environment variable: ``` echo S::env(CURRENT_DEF)```.
  - Once the command is given, power distribution netwrok is generated.
  - The power distribution network has to take the `design_cts.def` as the input def file.
  - Power rings,strapes and rails are created by PDN.
  - From VDD and VSS pads, power is drawn to power rings.
  - Next, the horizontal and vertical strapes connected to rings draw the power from strapes. 
  - Stapes are connected to rings and these rings are connected to std cells. So, standard cells get power from rails.
  -  The standard cells are designed such that it's height is multiples of the vertical tracks /track pitch. Here, the pitch is `2.72`. Only if the above conditions are adhered it is possible to power the standard cells.
  -  There are definitions for the straps and the rails. In this design, straps are at metal layer 4 and 5 and the standard cell rails are at the metal layer 1. Vias connect accross the layers as required.
 
![pdn](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/8f3bc2b5-6655-470a-a43d-7e5378a55e61)

## Routing

Basically in any of the routing EDA tools both OpenLANE and Commerical EDA tools, the entire routing process is very complex because of huge space. They divided the routing into two stages, Global routing and detailed routing.

The two types of engines that perform two stages of routing are

 - **Global routing** - routing region is divided into rectangular grid cells and represented as coarse 3D routing graph. The engine that does global routing is ``FASTE ROUTE``. 
 - **Detailed routing** - Finer grids and routing guides used to implement physical wiring ``tritonRoute`` 
Fast Route generates the routing guides, whereas Triton Route uses the Global Route and then completes the routing with some strategies and optimisations for finding the best possible path connect the pins. 

## Features of TritonRoute

- Performs Initial detail route
- Honouring **pre-processed route guides** - attempts to route within the route guides
   - Initial route guide - see the directions of the prefered route guides. If any non direction routing guides are found it divides it into unit widths.
   - Splitting - It splits non direction routing guides into unit widths
   - Merging - guides that are orthogonal(touching guides) to to the prefered guides are merged.
   - Bridging - gudies that are parallel to the perfered routing guides are bridged with an additional layer
   - preprocessed guides
   - 
![i will know too](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/9de7b0aa-dd3c-42de-a199-2b3ff474f0d6)

- Assumes route guide for each net satisfy **inter guide connectivity**
   - Same metal layer with touching guides or neighbouring metal layers with nonzero vertically overlapped area( via are placed ).
   - each unconnected termial i.e., pin of a standard cell instance should have its pin shape overlapped by a routing guide( a black dot(pin) with purple box(metal1 layer))
  
![i will know this too](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/5c9d2474-08d4-4d1a-a5fe-c678b92c6211)    ![i will know](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/b3d7a8b4-b7a3-4cae-b841-b4408dc9e39f)

- Works on MILP(Mixed Integer linear programming) based **panel routing** scheme with **Intra-layer** parallel and **Inter-layer sequential** routing framework
 
![inter and intra](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/e21dc4df-d2f4-4939-b711-6b9750e46174)

## TritonRoute problem statement

``` 
Inputs : LEF, DEF, Preprocessed route guides
Output : Detailed routing solution with optimized wire length and via count
Constraints : Route guide honoring, connectivity constraints and design rules.
```

The space where the detailed route  takes place has been defined. Now TritonRoute handles the connectivity in two ways.
- **Access Point(AP)**          : An on-grid point on the metal of the route guide, and is used to connect to lower-layer segments, pins or IO ports,upper-layer segments.
- **Access Point Cluster(APC)** : A union of all the Aps derived from same lower-layer segment, a pin or an IO port, upper-layer guide.

![ap apc](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/f1719cb7-468a-48f5-aabb-d340847a9685)

TritonRoute run for routing

Make sure the ``CURRENT_DEF`` is set to ``pdn.def``

Start routing by using 

``run_routing``

- The options for routing can be set in the `config.tcl` file. 
- The optimisations in routing can also be done by specifying the routing strategy to use different version of `TritonRoute Engine`. There is a trade0ff between the optimised route and the runtime for routing.
- For the default setting picorv32a takes approximately 30 minutes according to the current version of TritonRoute.
 
![drc clean](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/c418c0c3-bea0-401c-92e6-d46eeb3041bf)

To see the final layout with PDN and routing use magic engine.

``` magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.routing.def & ```

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY4/assets/135046169/6feefd73-f6fa-4911-9ab4-4df4e5d75fb7).

Our custimizes cell in the final layout i.e., after routing

![routed design of our cell](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/7630fb16-423a-4450-99b2-45d24b168aaf)

## SPEF Extraction

Parasitic extraction is done separately using spef extractor oustide the openlane.
Invoke the engine using the command in SPEF_EXTRACTOR directory

``python3 main.py /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/03-06_15-23/tmp/merged.lef /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/03-06_15-23/results/routing/picorv32a.def/`` 

One thing is observed is TritonRoute automatically generates spef file once it finishes its run. If we create a spef manually, this will replace the auto generated spef file.

Drc clean, Timing clean. My design RTL2GDS is successful.
