# PLL IC design using Open Source PDK Google Skywater 130nm
![CP](https://user-images.githubusercontent.com/63381455/127760055-c856e48c-0e21-48de-a370-4aa641c4c0d2.png)

# Table of contents

- [Overview](#overview)
- [Day 1 : PLL Theory and Lab set-up](#Day1)
  - [Introduction to PLL](#PLL)
  - [Introduction to Phase frequency detector](#PFD)
  - [Introduction to charge pump](#CP)
  - [Voltage control oscillator and Frequency Divider](#VCO)
  - [Tool setup and design flow]
  - [Introduction to PDK, specifications and pre-layout circuits](#PDK)
- [Day 2 : PLL Labs and Post Layout simulations](#lay)
  - [PLL component circuit design](#des)
  - [PLL component circuit simulation](#sim)
  - [Steps to combine PLL sub circuits and PLL full design simulation](#sub)
  - [Troubleshooting](#trou)
  - [Layout design](#lay)
  - [Layout walkthrough](#walk)
  - [Tapeout theory](#tape)
  - [Tapeout labs](#labs)
- [Acknoledgement](#Ack)

## Overview

This repository deals with the workshop which signinfies the beginning of a new era of “Design at $0” as visualize and materialize by the continuous effort of the entire VSD team and [Lakshmi Sathi](https://github.com/lakshmi-sathi/avsdpll_1v8). This workshop is also another standard VSD IAT cloud based 2 days workshop with 24 hrs accessibility of the platform to each participants. The VSD IAT platform provides all the necessary material including remote access of their ubuntu server for carrying on the labs with the pre-loaded tools required for the particular workshop. Assessment is also done through the multiple choice questions, including both the theoritical understanding and lab exercise. However, the installation procedure of the required tools viz ngspice and magic for use in our local system is covered. The snippet of the platform and lab instance is as shown  below. 

![Platform](https://user-images.githubusercontent.com/63381455/127762673-1e95f130-8853-4e38-8531-486f8d1ba857.png)

![Lab_instance](https://user-images.githubusercontent.com/63381455/127762677-db01a615-e3eb-4517-a0c5-8cd68bd1204d.png)

## Day 1 : PLL Theory and Lab set-up

Day1 is divided into two parts - PLL Theory section and the Lab set-up section. The approach taken by the instructor [Lakshmi Sathi](https://github.com/lakshmi-sathi/avsdpll_1v8) the designer herself, in designing the Phase-Locked Loop IC on Open-Source Google-Skywater 130nm node is an intuitive one where a simple PLL with extraordinarily little math and without diving into complex frequency domain analysis or control system theory is considered. 

Now starting from the basic question:

Why PLL? 

To get precise clock signal without frequency and phase noise. Also to have flexibility to work on frequency of choice. Now as quartz crystal provides superior spectral purity, fixed frequency and phase noise performance, VCO on the other hand has good flexibility and implemantable on SOC, hence the amalgamation of the two for designing a simple PLL IC is good enough. The Phase lock loop intution is based on the abilitity of the output signial to mimic the input reference signal and maintaining a constant phase difference. The block diagram of the PLL is as shown in figure below with a more detailed one next to it.


![6](https://user-images.githubusercontent.com/63381455/127767306-796e61e9-e3b1-4617-b5fb-9925f649725e.JPG)

![7](https://user-images.githubusercontent.com/63381455/127767319-8d36f4ae-a3fc-4e80-9871-7e736b0474e9.JPG)

Phase frequency detector, works by comparing the reference frequency signal and the output frequency to bring about synchronization so that the phase difference is minimum, whose output is in digital form. Next the charge pump circuit converts the same into analog signal which the LPF smoothens and serves as input to the VCO. The Frequency divider circuit also known as clock muliiplier as it divides the entire circuit to a mulitiplier circuit. Say for example if the FD signal is divided by 8 then the VCO has to be adjusted so that the signal is 8 times that of the reference signal. 

## Introduction to phase frequecy detector

Two cases are considered - one when the output frequeny is leading the reference frequency and the other when it is lagging. The two signals are then XORed and their output compared. From the XORed signal it is concluded that it is difficult to distinguish between the lagging and leading signal baded on this method. The reference figure is included below.

![LL](https://user-images.githubusercontent.com/63381455/127772339-dcb515b4-4c5f-4c29-9060-448d3282f5a4.JPG)

Hence the concept of DOWN signal and UP signal is applied. Whenever the output signal is leading the DOWN signal is activated and vice versa incase of lagging signal and thereby the VCO signal is adjusted as per requirement to minimize the phase difference. 

![PFD](https://user-images.githubusercontent.com/63381455/127772653-4cf48e66-1654-4965-9e8a-e451da199ae8.jpg)

## Introduction to charge pump

It converts the digital measure of phase/frequency difference into an analog signal to control the oscillator. The circuit diagram of the charge pump circuit is as shown below. It consists of a current stirring circuit whose output depends on whether the capacitor is charging or dischahging based on the Up and Down signal. However, the charge pump has leakage issue.

![CP_comb](https://user-images.githubusercontent.com/63381455/127774109-4723c54a-67ae-429d-9dbc-1e5e58a90d15.JPG)

Now in order to stabilize the output a loop filter (LPF) is added along with the capacitor. The value of the capacitor is is change and its value is based on the thumb rule along with the loop filter bandwidth is as shown in the figure. 

![charge_pump](https://user-images.githubusercontent.com/63381455/127774970-0200c314-f426-440f-b6ef-33de727db623.jpg)

## Voltage control oscillator and Frequency Divider

The most common oscillator, Ring oscillator is used as a VCO. It consists of odd number of inverters connected in series, which has the same number of delay.  The output flips after the delay and hence driving half of a period which is given twice the delay of the inverter and the inverter count viz `period = 2*delay(inverter)*inverter count` which bascically signifies that it has a fixed frequency. To have control over the frequency, a current starving mechanism is used over the ring oscillator circuit, where two supply of currect sources are provided to the top and bottom of the ring oscillator, which are controlled by the control voltage. The current sources in turn control the frequencies.It is necesary to design the VCO circuit prooperly to ensure that the desired range of frequency for is obtained.


![VCO](https://user-images.githubusercontent.com/63381455/127775454-6c06558a-26b6-4efa-bccb-b8a03e0c93d1.JPG)

### Frequency Divider 

As the output of the toggle flip flop is half the frequency of the input sigal. Hence, if we had a D-flip we can make into a toggle flip flop by adding an inverter to the ouput and input pin of the D flip flop. Hence, if the a signal with 10 ns period is supplied at the input, the ouput will a signal of 20 ns period as shown in figure below. For obtaining a 8x multiplier 3 such toggling flip flop can be used.
 
![31](https://user-images.githubusercontent.com/63381455/127779491-de5072d8-41d7-405c-9e5d-d35125e60ade.JPG)

Some common terms used in PLL

 - Lock range : The frequency the range the PLL is able to follow input frequency variations once locked.

- Capture range: The frequency range the PLL is able to lock-in when starting from an unlocked condition and it depends on the bandwidth of the loop filter.

- Settling time : The time the PLL requires to be in lock step from an unlocked state. It depends on how the charge pump reaches its stable value

## Tool setup and design flow

This section deals with the tool setup and the development flow. For installaing the required tools into local machine the open source tools ngspice and magic is to be installed along the Google skywater PDK - a 130 nm process. The details of the same can be found [here](https://github.com/lakshmi-sathi/avsdpll_1v8/blob/main/README.md#-Instruction).

### Development flow

 The design/development flow for any IC includes the following steps
 
 - Specification 
 - Spice level circuit simulation
 - Pre-layout simulation
 - Layout development
 - Parasitic extraction
 - Post layout simualtion

## Introduction to PDK, specifications and pre-layout circuits

The Process design kit (PDK) used is the open source Google Skywater 130nm. PDK's are basically the foundry files which contains about the trnsistor, their configuarion, timing information, area occupied, layout, verilog. However, in building the PLL IC all these information is not necessary as it is designed from scratch i.e from the transistor level. Also the PDK has diffrent process corners and in this case the TT corner is considered. The specications in designng the IC are mentioned in the [repository](https://github.com/lakshmi-sathi/avsdpll_1v8). Also the PDK provides different sets of libraries and for this case the primitive library for spice simulation is used. 



The first step to start the lab exercise is to git clone the avsdpll_1v8 repository using the git command followed by the url of the repository viz `git clone https://github.com/lakshmi-sathi/avsdpll_1v8.git`. Thus, a folder will be created which contains all the necessary files and folders required for the smooth functioning of the workshop.

![LAB1](https://user-images.githubusercontent.com/63381455/127780740-15755297-a66c-4053-828e-0ec756b64db4.JPG)



Now, the necessary files required for the lab is to be collected in the same location and thereafter a file is to created with all the required libraries for used during execution of the ngspice programs. With respect to the specification given, the nfet and pfet configuration for 1.8v supply voltage, TT corner and room temperature, the required libraries are collected in the same location for ease in the operation. The snapshot of the list of the libraries considered is as shown below.

![lab2](https://user-images.githubusercontent.com/63381455/127781879-1c7abdc5-b1eb-4389-b14f-933790fe4ac4.JPG)

## Day 2 : PLL Labs and Post Layout simulations

During the Day 2, some of the labs of post layout simualtions are done for understanding of the procedure required and the changes to be done so that the pre-layout and post-layout simulations output matches.

## PLL component circuit design

Now the frequency divider spice file is checked and the proper location of the library file is mentioned for its execution. Also, the maximum allowable length for 130nm process is considered i.e 150nm. As far as the width of the transistors the minimum width for nmos is 360nm and for pmos is 420nm. Base on the requirement of the design the width of the transistor are adjusted. The snippet of the FD.cir is included below.

![FD](https://user-images.githubusercontent.com/63381455/127850477-6428146b-8d26-4b0d-88bc-96e4c03e63dd.png)


## PLL component circuit simulation

Here the circuit simulation of the charge pump circuit is done as a part of lab assessment. For that the library file path is checked and modified, and therafter slight modification in the transient response ending time is done from 1u to 20us, inorder to find the voltage at the output due to leakage, which is 800uv. The snapshot of the assessment along with the output is included below.

![ques](https://user-images.githubusercontent.com/63381455/127852722-1514448b-4aa5-4625-8379-479c02fb5a14.png)

![LAB4](https://user-images.githubusercontent.com/63381455/127853091-be9edcff-7be2-4000-a5cc-f17f898fab6d.JPG)


## Steps to combine PLL sub circuits and PLL full design simulation

 After individually designing the layout of the different components the next step is to combime the layouts into the final layout design. Thus a magic file is opened using the command `magic -T sky130A.tech`, where `sky130A.tech`, is the technology file provided by Google Skywater. Now the individual layouts are placed by clicking cells part and using the place instance as shown in the figure below. Initially blank box appears and to view the layout the  'i' key followed by the 'x' key is used. After properly placing the layouts, the next step is to connect them together using the input and the output metal layers and combining them together by appropriate via.
 
![PI1](https://user-images.githubusercontent.com/63381455/127850788-f5fd46b3-bd7a-408b-9519-421d75581119.png)

![PI2](https://user-images.githubusercontent.com/63381455/127851505-336a12c1-f9eb-4fde-9afe-461daa6087d0.png)

![PI3](https://user-images.githubusercontent.com/63381455/127851666-913dbf66-6aa4-40ca-bedc-b82ab76ff46e.png)

 
 ## Troubleshooting
 
 It is important to troubleshoot/debug a design at the individual level prior connecting them together which saves from unwanted hiccups at later stage. In case of PLL IC design some of the issues face during Pre-layout simulation is mentioned below along with its debugging procedure.
 
 - PreLayout-Sim: What to do if it doesn't work?
 
   - Observe what kind of issue is faced
   - Always debug individual component fully before moving to combined simulation 
   - Are some signals coming up flat? Or the siumalation is crashing? - Check connections
   - The signals are right but mimicking not happening - Check wrong net name, capatilization issue or wrong parameter value
   - For what range of frequencies, is the VCO working properly?
   - Is the phase frequency detector able to detect small difference
   - How is the response of Charge Pump?
   - Whether the loop filter values ane working out?
  
 ## Layout design
 
 Here some discussion on the magic design tool is done viz drawing a simple nmos transistor, using of the color pallete on the right hand side of the layout window which showcase the different layers required in layout design, selecting the entire design using the key `A`, moving the entire design to a different location using `M` key or simply copying it through `C` key. If drc error exists it can be viwed by using `?` in the tkcon window, and the error has to be rectified.  Now here a lab  execise is done to find the area of either charge pump/frequency detector. For that simply the a box is selected by left clicking say on the top left corner and right clicking on the bottom right corner thereafter simply th box command is used in tkcon window/editor of magic as shown in figure below.
 
![LAB8](https://user-images.githubusercontent.com/63381455/127853337-80bf838d-0f70-4dea-a3a7-56db55912e4e.JPG)

 
 ## Layout walkthrough
 
 The VCO layout design is considered, and the design is explained thoroughly and the figure of the VCO design is included below. The final layout of  the PLL IC is also included below.
 
 ![VCO_Layout](https://user-images.githubusercontent.com/63381455/127853544-aa3d1ce0-baae-4f58-88ba-3b4c8d139b52.jpg)

 ![PLL_Layout](https://user-images.githubusercontent.com/63381455/127853614-78b35d0e-f3ba-49e1-a6ca-5e20228bf081.jpg)
 
  
 ## Parasitic extraction
 
 For extracting the parasitic capacitance and resistance after layout design the following command are used in the tkcon window.
``` 
 extract all
 ```
 it extracts a .ext file
 
 ```
 ext2spice cthresh 0 rthresh 0
 ```
 extracts the parasitics from the 0'th value itself.
 ```
 ext2spice
 ```
 generates a spice file
 
 After extraction of the spice file, all the parasitics are included in it along with other parameters (ad,as,pd,ps) and some changes occurs in the spice program. The first thing which changes is the scale for the transistor sizing and hence it should be changed to the required scale. Now the parasitic extraction of the PFD file is done and the spice file is checked and it is found that a total of 43 parasitics capacitance is extracted nad the highest capacitive value is of capacitor C35 = 3.76fF, across the VDD and GND pin which is observe as a part of lab exercise. The snapshot of the same is included below.
 
![LAB9](https://user-images.githubusercontent.com/63381455/127853710-b8c54471-c87e-45b5-9d3a-83bbbfea4da2.JPG)


Next exercise is to instantiate the subckt to the newly generated PFD spice file and make a phase difference of 0.25ns between the reference clock signal and the feedback clock signal and to obsere the output generated. It is observed that the up signal is short and is capable of detecting the small phase difference between the two signal.

![quest](https://user-images.githubusercontent.com/63381455/127853902-c3a6a713-0cff-4bff-aabf-4f0a5d4b8a87.JPG)


Finally after removing the drc errors any verifing the proper functioning of the design, the next step is to create the gds file of the final PLL design, using magic. Go to file in the layout window and simply `write gds`, a gds file is created which is used for the final tapeout.

## Tapeout theory

Tapeout is he process of adding the final design to the FAB after we prepare it. The preparing stage consists of number of steps.

- Inclusion of I/O pads
- Peripherals for serial connection
- Memory 
- Testing mechanism
- Others

However, in the open source world this has been made easy by Efabless through its MPW open shuttle program which is sponsored by Google. Efabless provides a Caravel SOC where anyone can use it for their design fabrication. For this a user project area is defined where the designer places his/her design based on the pins placement and makes the necessary connection. The Caravel SOC along with its details is as shown in figure below. The details of the Efables MPW shuttle 2 program can be viewed [here](https://efabless.com/open_shuttle_program/2).

![CaravelSoc](https://user-images.githubusercontent.com/63381455/127854944-ae4b8d73-2038-420d-8c02-9c559b6aace8.png)



## Tapeout Labs

- The first step is to go to the efabless caravel github [link](https://github.com/efabless/caravel) and from the gds folder download `user_analog_project_wrapper_empty.gds.gz` and extract it.
- Next open the `user_analog_project_wrapper_empty.gds.gz` in magic using the command `magic -T sky130A.tech user_analog_project_wrapper_empty.gds.gz` and place the PLL design in the used define area. By zooming in the pins available in the SOC can be viewed. The PLL has 5 pins, 1 each for voltage and ground, 2 digital IO pins for Ref_Clk and out_clk pina and an analog pin for the output VCO.


## Acknowledgment

- [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd.
- [Lakshmi Sathi,8X PLL Clock Multipler](https://github.com/lakshmi-sathi/avsdpll_1v8)
