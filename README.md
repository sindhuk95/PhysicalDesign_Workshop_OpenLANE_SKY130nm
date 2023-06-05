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

![Screenshot (25)](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/6fedcc32-0988-4f61-821c-47e15b5fed06)

### Foundary  IPS and Macros
**FOUNDARY** is a factory where chip gets manufatcured. Every information related to to chip depends on this. It is a set of machines where engineers can communicate with factory. **IPS** is **intellectual property** because these blocks need some intelligence to be built and are manufactured using foundry. Hence together called Foundary IPs.

**Macros** are reusable pieces of logic blocks (Intellectual Properties), that can be used in a design without the necessity of building them from scratch. These are like block box. 

![image](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/14c98f72-737e-491f-85dd-d5974aaba198)

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

![Screenshot (26)](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/cf1c401d-a955-40a7-ba3c-e2ac25506698)

# SOC DESIGN USING OPENLANE

### Components of Opensource Digital ASIC Design

Digital ASIC Design in a automated way requires several elements. The elements are
- RTL (Hardware descrption language) Models
- EDA Tools
- PDK Data

![Screenshot (28)](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/b081c2e3-3b7e-496c-bad7-5fe9be196f75)

we know about RTL and EDA tools. lets talk about PDK data.

** PDK data (Process Design Kit):** In order to model a fabrication process for the EDA tools we need collection of files. These are included in that kit. It is the interface between designers and fabrication team.
PDK has Design rules, Device models, Digital Standard cell Libraries,I/O lib etc. 

Google worked on an aggreement with skywater to opensoure PDK for the 130nm process by skywater. Google released the first ever opensoure pdk. The pdk has only data information for successful ASIC implementation by openroad or ocla tools


![Screenshot (27)](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/52c25efe-5966-4e69-a4e3-176bb3263f28)

Few Opensources for these three components are 
 
 ```
 RTL Designs :
 -librecores.org
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

![Screenshot (29)](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/707d282d-df46-4457-967b-9cf5dd58303a)

**Synthesis                     :**  converting RTL into gate level netlist using standard cell libraries.
**FloorPlanning & PowerPlanning :**  Size and shape of the die, Macro placement, I/O pins are done in FP
power Grid is build and through rings,stripes and rails power is evenly distributed. Upper metal layers are used for power routing because of  more width, indeed less resistance so less IR drop and Electron migration
**Placement                     :** According to the netlist through coarse placement, cells are placed in their resepctive locations and cells might overlap.
so we do detailed placement to avoid the overlapping and flipped to save site row area.
**Clock Tree Synthesis          :** Building a clock tree such that clock reached every clock pin of a sequential block with min skew and insertion delay. 
**Routing                       :** After clock, Signal routing takes place. For each metal layer PDK defines thickness, pitch,width, tracks etc.
**SKY130 has 6 layers. The lowest layer is used for interconnects and is made of titanium nitride and all 5 layers are aluminium.**
Most routers are grid routers and metal tracks form a routing grid. since the grid is huge, we use divide and conquer approach. 
Global Routing is performed using coarse grained grids generating routing guides. Fine grained grids uses these routing gudies and impplement the actual routing between wires.

Once Routing is done, we do verifictaion during signoff

1. Physical verification :   
      Design rule checks (DRC) : verifies whether our design meets design rules. (performed by MAGIC)
      Layout vs schematic (LVS) : verifies whether our layout matches with the netlist schematic.(MAGIC AND NETGEN)
2. Timing Verification : 
      Static Timing Analysis : Checks whether our design meets all teh timing constraints and is running with the designated frequency.

![Capture](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/baf3cdb1-79e8-4a9b-b229-2e05622a4b7c)

INTRODUCTION TO OPENLANE 

OpenLANE has a design flow starting from Design RTL to GDSII. The function needs PDK. It is based on several opensource projects such as openROAD, YOSYS, ABC, FAULT, MAGIC and may other.

OpenLANE ASIC FLOW : The main goal of OpenLANE Asic flow is to have clean GDSII with out any human intervention i.e., no DRS and LVS violations. OpenLANe can be used to harden Macros and chips.
Two modes of operation:

1. autonomus -  its like a push button flow, does everything automatically
2. Interactive -  runs through commands 

![Capture](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/21752035-2a3e-43b9-89da-4395e33de3e1)

The flow starts with RTL synthesis. RTL is fed to YOSYS with the design constraints and translates into netlist then optimised and mapped into the synthesizable cells from SCL using ABC. ABC is used during optimization. When it comes to OpenLANE we have stratagies where some tragets least area and the other on best timing, this is called SYNTHESIS EXPLORATION.

DESIGN EXPLORATION :  When you have more no of configurations in our design, the Design exploration does sweeping job and gives an design matrix which has report on violations, cell count, runtime, utilization etc and from that we can get the best set of configurations and clean layout.

![Capture](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/0efa00ea-fa0f-44f9-b0ec-1f14b0ff1bd6)


OpenLANE REGRESSION Testing : The design exploration utility is used for regression testing. OpenLANE already does this so that we can run the experssions on serveal configurations and compare them with the best one. It is something like Iteration.
![Capture](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/e6b81838-b16a-4483-bf4b-0e6650049838)


DFT (DESIGN FOR TEST) : Its an additional testing that is done for correctness of the design by generating test vectors or stimulus. 

Physical Implentation : we use automated PnR 

1. Floor/power planning
2. End decoupling capcitors and Tap cells insertion
3. PLacement : global and detailed
5. Post placement optimization
6. Clock Tree Synthesis
7. Routing : Global and Detailed

LEC : Since, we do optomization in Physical Implementation, the output netlist from this might be different from the synthesis netlist. Inorder to avoid any further violations functionally, we need to logically check the design before and after PnR. This is known as Logic Equivalence check

Antenna : When a metal wire segment is fabricated and its long enought, it act as antenna and collect charges which can damage the transistor  gate that is connected to that wire.
so length of the wire must be limited. Usually this job is done by router, if he fails, there are 2 solutions.

1.  Bridging : In this we bridge using top layer so we go upto the top layer and drop back to to the metal layer that has long wire segment.
2. Antenna diode : Insert the diode next to the transistor that is getting effected by this long wire.

Using OpenLANE we took a preventive approach. we created a fake antenna diode and placed next to every cell during placement. when Magic runs for antenna checkers on the routed layout and reports a violation on a cell input pin, then replace the fake with the real one.

Timing signoff involves interconnect rc extraction from the routed layout followed by STA performed by openSTA and reports timing.

Open Source EDA tools : 

In this LAB1 we had to synthesis a design picorv32a by using OpenLANE -interactive mode. If your using the lab session from https://www.vlsisystemdesign.com/ then you dont need to configure Docker but if your using in your PC you need to configure docker. So i used my PC for this practical session , i configured docker and then opened flow.tcl file in intercative mode . Its script which will invoke the Openlane.

./flow.tcl -interactive

after this we need to include package by using below command

package require openlane 0.9

after that  we need to prepare design files  by using below command


   prep -design picorv32a

when the design setup is completed, we need run our design for synthesis by using below command
 
   run_synthesis

After the results, we need to calculate the flop ratio.
flop ratio = (no of flops)/(total no of cells).

dfxtp2 = 1613
no of cells = 14876
so flop ratio = 0.10842 or 10.842%
![Capture](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/2a5f59a8-ad3a-432b-80cd-8cf5cbbbbf3c)
![Capture](https://github.com/sindhuk95/openLANE_sky130_PD_workshop_day1/assets/135046169/4119673d-ccdf-490a-8dd6-bcded97f35b7)








 









