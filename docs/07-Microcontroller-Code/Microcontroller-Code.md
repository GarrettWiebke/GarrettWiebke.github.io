---
title: Microcontroller Code
---

## Overview
This code is programming the subsystem to move the gear motor by turning on the RB2 pin that is connected to the forward pin of the systems H-bridge, after 3 seconds RC1 will activate turning on the water pump, and then RB1 will turn on being connected to the reverse pin in the H-bridge.


```
#include "mcc_generated_files/system/pins.h"
#include "mcc_generated_files/system/system.h"


#define _XTAL_FREQ 64000000UL   // Adjust if needed for your clock

void main(void)
{
    SYSTEM_Initialize();
    PIN_MANAGER_Initialize();

    // --- Configure pins as requested ---
    IO_RB1_SetDigitalOutput();   // Reverse output
    IO_RB2_SetDigitalOutput();   // Forward output
    IO_RD0_SetDigitalOutput();   // ON LED
    IO_RD1_SetDigitalOutput();   // OFF LED
    IO_RC1_SetDigitalOutput();   // RC1 as output (added)
    IO_RC6_SetDigitalInput();    // Sensor input

    // Make sure outputs start LOW
    IO_RB1_SetLow();
    IO_RB2_SetLow();
    IO_RD0_SetLow();
    IO_RD1_SetLow();
    IO_RC1_SetLow();             // ensure RC1 starts LOW

    while (1)
    {
        //uint8_t sensor = IO_RC6_GetValue();  // Read sensor pin
        uint8_t sensor = 0;   // force OFF or high state

        if (sensor == 1)
        {
            // ============================
            // SENSOR HIGH ? MOTOR ACTION
            // ============================
            IO_RD0_SetHigh();   // ON LED
            IO_RD1_SetLow();    // OFF LED

            // Forward
            IO_RB2_SetHigh();
            __delay_ms(3000);
            IO_RB2_SetLow();

            // --- New RC1 activation sequence between forward and reverse ---
            __delay_ms(1000);   // small delay before RC1 turns on (adjustable)
            IO_RC1_SetHigh();   // RC1 turns ON
            __delay_ms(1000);   // RC1 stays on for this duration (adjustable)
            IO_RC1_SetLow();    // RC1 turns OFF
            __delay_ms(100);    // tiny gap before reverse (optional)

            // Reverse
            IO_RB1_SetHigh();
            __delay_ms(3000);
            IO_RB1_SetLow();
        }
        else
        {
            // ============================
            // SENSOR LOW ? MOTOR OFF
            // ============================
            IO_RB1_SetLow();
            IO_RB2_SetLow();

            IO_RD0_SetLow();    // OFF LED
            IO_RD1_SetHigh();   // ON LED
            IO_RC1_SetLow();    // ensure RC1 off while idle
        }
    }
}
```


## Resouces
The link to the updated subsystem code .zip file 
[Here](Sub.X.zip)