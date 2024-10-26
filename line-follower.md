---
layout: default
---

# Line Following Robot

### Overview

The final of my 400-level mechatronics class was to create a wheeled robot capable of following a line, guided by a camera and an image recognition program.

### Electrical and Software Design

I used two wheels and two actuating motors to control the robot. A PIC32MX170F256B microcontroller, programmed in and compiled in C, held command over the robot's motion. The PIC32 and all of my other electrical components were soldered onto a custom PCB I designed in KiCAD and ordered through PCBWay.

[image of PCB layout]

Most of the circuit design was just based on the datasheet for the PIC32, which specifies what pins are used for power, for data input/output, where LEDs should go to signal power/data upload, etc. To interface with my computer, I used an Adafruit UART Friend in conjuction with a 3.3V regulator. The actual board is made up of two conducting layers. I used "Manhattan style" connections, where one layer is used to create horizontal connections and the other layer is used for vertical connections. This helps to alleviate possible shorts and makes board design easier.

The program to run the motors is fairly simple, using a PWM signal:

```C
//motor 1
    RPA0Rbits.RPA0R = 0b0101; //Makes A0 OC1
    // Make an output pin for B2
    TRISBbits.TRISB2 = 0;
    LATBbits.LATB2 = 0;
    T2CONbits.TCKPS = 0;     // Timer2 prescaler N=16 (1:16)
    PR2 = 2400;              // period = (PR2+1) * N * (1/48000000) = 50Hz, has to be less than 65000
    TMR2 = 0;                // initial TMR2 count is 0
    OC1CONbits.OCM = 0b110;  // PWM mode without fault pin; other OC1CON bits are defaults
    OC1CONbits.OCTSEL = 0;   // Use timer2
    OC1RS = 600-1;             // duty cycle = OC1RS/(PR2+1) = 25%
    OC1R = 600-1;              // initialize before turning OC1 on; afterward it is read-only
```

To actual turn on the motor you would run OC1RS = _____. To tell when each motor to actuate and by how much, I had a very simple program that essentially said "if the line is to the right of the camera, actuate the left motor, and vice versa."



### Mechanical Design

### Result

Picture

[back](./)
