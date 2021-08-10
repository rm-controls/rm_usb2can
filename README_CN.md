# rm_usb2can

![Version](https://img.shields.io/badge/Version-1.0.3-brightgreen.svg)&nbsp;&nbsp;![Build](https://img.shields.io/badge/Build-Passed-success.svg)&nbsp;&nbsp;![License](https://img.shields.io/badge/License-MIT-blue.svg)

[English](https://github.com/rm-controls/rm_usb2can/blob/main/README.md)/中文

***

### 简介

&nbsp;&nbsp;&nbsp;&nbsp;本项目的开发目的是为了给NUC提供一个CAN的外设接口。由于NUC不具有SPI等简单外设接口，故无法使用MCP2515等SPI转CAN芯片。为此，本项目基于github的开源方案[candleLight](https://github.com/candle-usb/candleLight_fw/tree/master)开发，采用STM32F072CBT6作为主控芯片，实现USB转CAN的功能。STM32F072CBT6具有能够同时工作的USB全速外设和CAN外设，封装为QFP64，是较为优秀的解决方案。

### 开发工具

+ EDA工具： Altium Designer 20.0.13
+ 编译工具： gcc-arm-none-eabi  8-2019-q3-update
+ 烧录工具： STM32CubeProgrammer v2.6.0

### 电路设计

&nbsp;&nbsp;&nbsp;&nbsp;USB HUB电路设计：由于整车需要至少2路CAN总线来保证电机回传数据包的完整性，所以我们采用了GL850G作为USB HUB芯片实现USB一拖四的方案。

![usb_hub](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/usb_hub.png)

&nbsp;&nbsp;&nbsp;&nbsp;USB转CAN电路设计：用STM32F072CBT6实现USB转CAN功能。CAN电平转换芯片采用MAX3051芯片，该芯片使用3.3V供电，且封装为SOT23-8，为PCB小型化提供了基础。

![stm32_can](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/stm32_can.png)