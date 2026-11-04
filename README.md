# Bad, bad version of the analog synthesizer to Open Sound Control (OSC) module

## Project description

See branch main for the real project description.

On this branch, we modify the working design, to make a test project for automatic review tools.

Do not use it for reference. Do not copy it. Absolutely never send it to production.
This is a design full of mistakes for the sake of finding mistakes.

## Error list


## Bringup
### V0.1 known issues
* PHY: Error in LDO routing: VDDCR pin 6 is an internally regulated LDO 1.2V output, not to be connected to 3V3 supply.
Add 1 μF and 470 pF decoupling capacitors to ground.
* PHY: nINTSEL should be 0, change LED2 connection to pull-down.
    * REF_CLK_OUT is used to generate the 50MHz clock for RMII
    * This special feature allows to use a 25 MHz crystal with the LAN8742
    * As a consequence, the INT/CLKO pin must be set to clock out, not int
    * This means nINTSEL/LED2 pulled down
* Cannot setup different interrupts for GPIO (EXTI register) having the same numbers but on different ports.
    * See [this issue](https://github.com/zephyrproject-rtos/zephyr/issues/45234).
    * Issue with A8 rotative encoder pushbutton VS C8 rotary input B
    * Issue with D14 rotary input A and E14 trig input
* Shield REFCLK0: it is a 50MHz line!
* Missing pin 1 dot on diode D2 for manufacturing
* Dot around ethernet PHY: crystal dot too close
* Put poka-yoke or marking to mount USB connector on right side
* Put poka-yoke or marking for buttons
* Reduce jack connectors holes to mount more precisely
* Add testpoints for MDIO?
* Maybe disconnect PHY reset from main reset
Reset is pulled low by STLink probe, it has 1V2 value when probe is connected even if not plugged in.

