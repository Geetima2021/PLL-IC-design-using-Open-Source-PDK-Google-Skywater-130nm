# PLL IC design using Open Source PDK Google Skywater 130nm
![CP](https://user-images.githubusercontent.com/63381455/127760055-c856e48c-0e21-48de-a370-4aa641c4c0d2.png)

# Table of contents

- [Overview](#overview)
- [Day 1 : PLL Theory and Lab set-up](#Day1)
  - [Introduction to PLL](#PLL)
  - - [Introduction to Phase frequency detector](#PFD)

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

## Voltage control oscillator

The most common oscillator, Ring oscillator is used as a VCO. It consists of odd number of inverters connected in series, which has the same number of delay.  The output flips after the delay and hence driving half of a period which is given twice the delay of the inverter and the inverter count viz `period = 2*delay(inverter)*inverter count` which bascically signifies that it has a fixed frequency. To have control over the frequency, a current starving mechanism is used over the ring oscillator circuit, where two supply of currect sources are provided to the top and bottom of the ring oscillator, which are controlled by the control voltage. The current sources in turn control the frequencies.It is necesary to design the VCO circuit prooperly to ensure that the desired range of frequency for is obtained.


![VCO](https://user-images.githubusercontent.com/63381455/127775454-6c06558a-26b6-4efa-bccb-b8a03e0c93d1.JPG)

Frequency Divider 





