# openLANE_sky130_PD_workshop_day1

## Inception of open-source EDA, OpenLANE and SKY130PDK**

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

### HOW TO TALK TO COMPUTERS.

 QFN-48  Package : Quad flat No Lead 48 package is an IC component best used for proagrammable module and microcomputers with 7 x 7mm body width and 48pins and these pins are driven by the board which is being seated. The Chip sits in the center of the Package. Package is like a case that surrounds the chip to protect it from corrosion or physical damage.

The Chip has various components.

1. Die  : Size of the entire chip and area where I/O pads sit.
2. Pads : Signals travel through pads in and out of the chip.
3. Core : Area in which Macros, standard cells sit and routing happens.

### Foundary  IPS and Macros

**FOUNDARY** is a factory where chip gets manufatcured. Every information related to to chip depends on this. It is a set of machines where engineers can communicate with factory. **IPS** is **intellectual property** because these blocks need some intelligence to be built and are manufactured using foundry. Hence together called Foundary IPs.

**Macros** are reusable pieces of logic blocks (Intellectual Properties), that can be used in a design without the necessity of building them from scratch. These are like block box. 

###  RISC-V Instruction set Architecture (ISA)  

Bascially, it is the language through which we communicate with the computers. If u want a c program to run on particular layout, we need to follow a particular flow to pass the information from c program to layout.

 ```Compilation from software to Hardware : C Program - Assembly language(RISC-V) - Machine language format(binary Format) - Layout (executed as per requirement)```
 For this to be executed, we need a separate interface between the architecture and the layout called Hardware description language. We need to implement the architecture using RTL and PnR converts RTL to Layout(GDS) and we get this layout as output.

lets us take Apps as examples.
how apps runs on the hardware? Between the apps and hardware, system software is where the implementation takes place. The Major components in system software are **OS,Complier,Assembler.**

**OS        :**  Handles IO operations, allocation of memory. It converts the App into its corresponding assmebly language program and then to binary language. The o/p of OS ais low level system functions i.,e C,C++,JAVA.

**Complier  :** The system functions is then given to the complier which converts into set of instructions depending upon the hardware.

**Assembler :** The instruction sets are then converted to binary language and fed to hardware.

This set of instructions is`` INSTRUCTION SET ARCHITECTURE or ARCHITECTURE OF COMPUTER `` and acts as interface between compliers input and hardware. RTL is implemented using this instruction set and synthesize to gate level netlist and then to layout.

# SOC DESIGN USING OPENLANE

### Components of Opensource Digital ASIC Design

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
### Simplified RTL2GDS Flow

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

### INTRODUCTION TO OPENLANE and STRIVE Chipsets

When using opensource EDA tools, the problem becomes tougher. We need to worry about 
- Tools qualification 
- Tools calibration 
- Missing tools

By releasing OpenLANE PDK, **efabless** decided On creating reference **open source ASIC implementation methodology and flow called **OpenLANE FLOW**. It comes with **APACHE 20. version license**. 

**OpenLANE** has started for true **open-source Tape-out experiment.**
At efabless, family of **open everything SOC's** is called **striVe.**
The members of striVe SOC family 
  - striVe
  - striVe 2, striVe 2a
  - striVe 3, striVe3a
  - striVe 5
  - striVe 6.
Main goal of OpenLANE Asic flow is to have clean GDSII with out any human intervention(no-human-in-loop)
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

### OpenLANE DIrectory Structure 

- OpenLANE is a flow that comprises of OpenSource tools
- This flow goal is to implement RTL 2GDS without human in loop.

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

In pdks directory .we have one of such variant file  ``sky130A``. In this we have libs folder, which has information specific to process technology and tools.

``sky130_fd_sc_hd`` variant is used for our design ``picorv32a``

Its abbreviation is:
 - sky130 : the process name, 130nm
 - fd     : foundry name of skywater. For example,  OSU is for oklahoma state university
 - sc     : standard cell
 - hd     : high density, variant of PDK

In this folder, we have lib files that has Timing related information like Process corners, PVT corners and lef and tech lef has physical information of cells.

### DOCKER
Before invoking the tool, lets know about **DOCKER**

When a developer develops a machine,its works fine in his system. But when its given to serves or someone else computers even with same performance or optimization, it wont work properly like images are not loaded properly or path is a little bit different. So, the classic problem between developer and production team is `` it works on my machine Why not in yours? , you might not have included the dependencies ``and this debt goes on. For this compatible issues, DOCKER was implemeneted. Docker is compatible with any programming language or project. Our code is wrapped in airtight containers which are portable.

**Docker is a tool which is used to automate the deployement applications in a light weight containers so the application can work effeciently in different enviroments.
 Container is a package that has all the dependencies required to run an application.**
 
 run `` docker`` 
 Once you run this, bash shell opens and we continue our OpenLANE Flow in that

Now, its time to use OpenLANE Flow. Since **OpenLANE is automatic, every stage runs automatically.** To know manually what is happening in each and every stage, we use **OpenLANE in interactive mode** and the flow.tcl has all the information about openlane flow.  

``./flow.tcl -interactive ``

**package** typically contains related programs that can perform different functions. So, input all the packages that are required for the openlane.

``package require openlane 0.9``

Before synthesis, we need to prepare the setup the data structure for our design ``picorv32a``.
We have only three files in our design. Now, we create a file system to flow where each amd every step of flow will be fetching data from a particular created location (``runs``)

``prep -design picorv32a``

A sub folder ``run`` has been created in picorv32a.

As you can clearly see the lefs are being merged from sky130A lef files.

Now, ``run_synthesis``

After synthesis, we need to calculate the flop ratio.
flop ratio = (no of flops)/(total no of cells).

dfxtp2 = 1613
no of cells = 14876
so flop ratio = 0.10842 or 10.842%







 









