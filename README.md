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

## Overview

This repository deals with the workshop which signinfies the beginning of a new era of “Design at $0” as visualize and materialize by the continuous effort of the entire VSD team and [Lakshmi Sathi](https://github.com/lakshmi-sathi/avsdpll_1v8). The workshop is also another standard VSD IAT cloud based 2 days workshop with 24 hrs accessibility of the platform to each participants. The VSD IAT platform provides all the necessary material including remote access of their ubuntu server for carrying on the labs with the pre-loaded tools required for the particular workshop. Assessment is also done through the multiple choice questions, including both the theoritical understanding and lab exercise. However, the installation procedure of the required tools viz ngspice and magic for use in our local system is covered. The snippet of the platform and lab instance is as shown  below. 

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

###Frequency Divider 

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



Now, the necessary files required for the lab is to be collected in the same location and thereafter a file is to created with all the required libraries for used during execution of the ngspice programs. With respect to the specification given, the nfet and pfet configuration for 1.8v supply voltage, TT corner and room temperature, therequired libraries are collected in the same location for ease in the operation. The snapshot of the list of the libraries considered is as shown below.

![lab2](https://user-images.githubusercontent.com/63381455/127781879-1c7abdc5-b1eb-4389-b14f-933790fe4ac4.JPG)
 





