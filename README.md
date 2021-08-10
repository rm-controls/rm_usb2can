# rm_usb2can

![Version](https://img.shields.io/badge/Version-1.0.3-brightgreen.svg)&nbsp;&nbsp;![Build](https://img.shields.io/badge/Build-Passed-success.svg)&nbsp;&nbsp;![License](https://img.shields.io/badge/License-MIT-blue.svg)

English/[中文](https://github.com/rm-controls/rm_usb2can/blob/main/README_CN.md)

***

### Brief Introduction

&nbsp;&nbsp;&nbsp;&nbsp;The purpose of this project is to provide NUC with a can peripheral interface. Since NUC does not have simple peripheral interfaces such as SPI, it is impossible to use SPI to can chips such as MCP2515. Therefore, the project is developed based on the open source solution [candleLight](https://github.com/candle-usb/candleLight_fw/tree/master) of GitHub, and STM32F072CBT6 is used as the main control chip to realize the function of USB to can. STM32F072CBT6 has USB full speed peripherals and can peripherals that can work at the same time. It is packaged as QFP64 and is an excellent solution.

### Develop Tools

+ EDA Tool: Altium Designer 20.0.13
+ Compiler: gcc-arm-none-eabi  8-2019-q3-update
+ programming Tool: STM32CubeProgrammer v2.6.0

### Circuit Design
