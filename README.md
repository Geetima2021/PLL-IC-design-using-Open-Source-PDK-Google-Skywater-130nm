# PLL IC design using Open Source PDK Google Skywater 130nm
![CP](https://user-images.githubusercontent.com/63381455/127760055-c856e48c-0e21-48de-a370-4aa641c4c0d2.png)

# Table of contents

- [Overview](#overview)
- [Day 1 : PLL Theory and Lab set-up](#Day1)
  - [Introduction to PLL](#GNU)

## Overview

This repository deals with the workshop which signinfies the beginning of a new era of “Design at $0” as visualize and materialize by the continuous effort of the entire VSD team and [Lakshmi Sathi](https://github.com/lakshmi-sathi/avsdpll_1v8). The workshop is also another standard VSD IAT cloud based 2 days workshop with 24 hrs accessibility of the platform to each participants. The VSD IAT platform provides all the necessary material including remote access of their ubuntu server for carrying on the labs with the pre-loaded tools required for the particular workshop. Assessment is also done through the multiple choice questions, including both the theoritical understanding and lab exercise. However, the installation procedure of the required tools viz ngspice and magic for use in our local system is covered. The snippet of the platform and lab instance is as shown  below. 

![Platform](https://user-images.githubusercontent.com/63381455/127762673-1e95f130-8853-4e38-8531-486f8d1ba857.png)

![Lab_instance](https://user-images.githubusercontent.com/63381455/127762677-db01a615-e3eb-4517-a0c5-8cd68bd1204d.png)

## Day 1 : PLL Theory and Lab set-up

Day1 is divided into two parts - PLL Theory section and the Lab set-up section. The approach taken by the instructor [Lakshmi Sathi](https://github.com/lakshmi-sathi/avsdpll_1v8) the designer herself, in designing the Phase-Locked Loop IC on Open-Source Google-Skywater 130nm node is an intuitive one where a simple PLL with extraordinarily little math and without diving into complex frequency domain analysis or control system theory is considered. 

Now starting from the basic question:

Why PLL? 

To get precise clock signal without frequency and phase noise. Also to have flexibility to work on frequency of choice. Now as quartz crystal provides superior spectral purity, fixed frequency and phase noise performance, VCO on the other hand has good flexibility and implemantable on SOC, hence the amalgamation of the two for designing a simple PLL IC is good enough.
 




