# Tesla Coil Dead-Time Driver V1 (TCDeadTimeDriver-V1)

Version 1 of the Dead-Time Controller add-on board for the [oneTesla v1 Musical Tesla Coil](http://onetesla.com/).  It is used to add a Dead-Time between the gate pulses to the IGBT driver transistors to minimize overlap and prevent premature death of the transistors.

This board can be ordered from [https://www.oshpark.com/shared_projects/0FoLInjA](https://www.oshpark.com/shared_projects/0FoLInjA).

##How to Calibrate (Requires Oscilloscope, Frequency Counter, and functioning oneTesla v1 Musical Tesla Coil):

1. Determine your free-running resonant frequency of the oneTesla Musical Tesla Coil without the dead-time circuit installed.

2. Install the dead-time board according to the notes in PWMDeadTimeDriver.pdf, making sure you replace the inverting UCC37321 driver chip on your oneTesla board with a non-inverting UCC37322 driver.

3. Run the Tesla Coil at a very low power setting (very small input pulses to the fiber optic receiver)

4. Set Dead-Time potentiometer (R7) until the desired dead-time delay gap is seen between Output #1 and Output #2 on an oscilloscope.

5. Set Free-Running Frequency potentiometer (R5) until the frequency on Freq Out (pin 4 of U2) is about 5% less than the primary Tesla Coil circuit resonant frequency.

6. Set the Sync Pulse Width adjust potentiometer (R3) until the SG3525 properly locks onto the current sense signal input connection from the 74HCT14 Pin 4 signal from the oneTesla main-board.  This will make the SG3525 act a like a PLL.

