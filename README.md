# Passive Acoustic Data Acquisition using National Instruments CRio chassis

This is code for data acquisition from a National Instruments CRio chassis as described
in our paper 

Gillespie D, Oswald M, Hastie G and Sparling C (2022) Marine Mammal HiCUP: A High Current Underwater Platform for the Long-Term Monitoring of Fine-Scale Marine Mammal Behavior Around Tidal Turbines. Front. Mar. Sci. 9:850446. [doi.org/10.3389/fmars.2022.850446](https://doi.org/10.3389/fmars.2022.850446)

The NI CRio Chassis has a FPGA front end, than can acquire data from multiple different types of CRio pugin board. The FPGA is read by a C programm running on the CRio's ARM processor. This then sends data to a PAMGuard sound acquisition plugin, where it's unpacked and instered into the PAMGuard processing chain. 

The code is therefore in three distinct parts:

## FPGA Code

In folder labview

This is a [National Instruments Labview](https://www.ni.com/en/shop/labview.html) project that will build bitfiles for the CRio FPGA. The only way you're going to build this is if you purchase Labview, but you'll find some bit files for some CRio chassis available in the repositor. Once activated from the C program, the FPGA acquired data and places it in a FIFI buffer that gets read by the C software below. 

## C / C++ code

In folder CPP

The C Code for the CRio on the ARM. This reads data from the FIFO buffer, compresses it using  a [lossless compression algorithm](https://doi.org/10.1121/1.4776206) then sends the data to the PAMGuard plugin using TCP network commands. 

Under normal cirumstances, the program is entirely operated from the PAMGuard plugin, though there are some command line options you can issue in the CRio temrinal during testing. Commands and status checks are all done using UDP, data transfer uses TCP. 

This was last built in about 2018 and since then NI have changed thier toolchain, so good luck if you want to rebuild this. 

## PAMGuard Plugin

In folder plugin

A PAMGuard sound acquisition plugin. A jar file buid is placed in the PAMGuard plugins folder and the system then becomes available as an option within the PAMGuard sound acquisition module. The module controls the above C program, which in turn controls the FPGA. Control is achevied by openeing an SSH shell within PAMGuard on the PC running PAMGuard, the C program runs within this shell, so that PAMGuard can pick up dbug output and also so that the C program terminates when PAMGuard exits. 
