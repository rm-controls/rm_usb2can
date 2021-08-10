# rm_usb2can

![Version](https://img.shields.io/badge/Version-1.0.3-brightgreen.svg)&nbsp;&nbsp;![Build](https://img.shields.io/badge/Build-Passed-success.svg)&nbsp;&nbsp;![License](https://img.shields.io/badge/License-MIT-blue.svg)

[English](https://github.com/rm-controls/rm_usb2can/blob/main/README.md)/中文

***

### 简介

&nbsp;&nbsp;&nbsp;&nbsp;本项目的开发目的是为了给[英特尔® NUC](https://www.intel.cn/content/www/cn/zh/products/details/nuc.html)提供一个CAN (Controller Area Network)的外设接口。由于NUC不具有SPI等简单外设接口，故无法使用MCP2515等SPI (Serial Peripheral Interface) 转CAN芯片。为此，本项目基于github的开源方案[candleLight](https://github.com/candle-usb/candleLight_fw/tree/master)开发，采用STM32F072CBT6作为主控芯片，实现USB (Universal Serial Bus) 转CAN的功能。STM32F072CBT6具有能够同时工作的USB全速外设和CAN外设，封装为QFP64 (Quad Flat Package)，是较为优秀的解决方案。

***

### 开发工具

+ EDA工具： Altium Designer 20.0.13
+ 编译工具： gcc-arm-none-eabi  8-2019-q3-update
+ 烧录工具： STM32CubeProgrammer v2.6.0

***

### 电路设计

#### 原理图设计要点

&nbsp;&nbsp;&nbsp;&nbsp;USB HUB电路设计：由于整车需要至少2路CAN总线来保证电机回传数据包的完整性，所以我们采用了GL850G作为USB HUB芯片实现USB一拖四的方案。另外，我们还通过使用施密特触发器来实现上电时序，保证USB设备枚举的顺序在每次上电后都是一致的。

![usb_hub](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/usb_hub.png)

&nbsp;&nbsp;&nbsp;&nbsp;USB转CAN电路设计：用STM32F072CBT6实现USB转CAN功能。CAN电平转换芯片采用MAX3051EKA芯片，该芯片使用3.3V供电，且封装为SOT23-8 (Small Outline Transistor)，为PCB小型化提供了基础。图中的R6、R7电阻用途为更改STM32的Boot模式，从而使STM32能够通过更改R6、R7的短接模式而在DFU (Device Firmware Upgrade) 烧录模式与Flash模式下切换，方便烧录固件。

![stm32_can](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/image/stm32_can.png)

#### PCB布局布线要点

1. 所有USB总线需要设置成差分对，在布线时严格遵循差分信号布线规则。
2. 所有CAN总线需要设置成差分对，在布线时严格遵循差分信号布线规则。
3. 晶振和谐振电容底部最好挖空不铺铜。
4. 电源线线宽应尽量大于0.3mm以保证电源轨道不塌陷。

***

### 电路制作

&nbsp;&nbsp;&nbsp;&nbsp;我们提供了三个不同版本的Gerber文件用于生产，分别是扩展：两路CAN+两路UART、四路CAN、两路UART。这三种版本的电路均采用两层PCB进行设计，面积约为20*30mm。你可以直接将打包好的gerber文件提交个生产厂家（如：[嘉立创](https://www.jlc.com/#)）进行生产。

&nbsp;&nbsp;&nbsp;&nbsp;你可以在此处找到该板对应的BOM (Bill Of Material) 表，并且依据这张表格将元器件焊接到已经制作好的PCB上。焊接完成后，请仔细检查是否有引脚之间有残余的焊锡，那将会导致电路板发生短路，同时你还要检查引脚是否有虚焊的现象发生。

&nbsp;&nbsp;&nbsp;&nbsp;焊接完成后，用万用表检查5V电源接口与GND之间是否短路、3.3V电源输出与GND之间是否短路。当确保上述情况没有出现时，再将板子连接电脑的USB口，此时应该可以看到板上的电源指示灯亮起，并且电脑能够识别到USB HUB芯片。

**注意：请勿同时焊接用于切换STM32下载模式的上/下拉电阻，那样有可能使电源与GND之间出现短路！**

***

### 固件烧录

&nbsp;&nbsp;&nbsp;&nbsp;你可以[点击此处](https://raw.githubusercontent.com/rm-controls/rm_usb2can/main/firmware.bin)下载已经编译好的固件，或者是跟随[candleLight文档](https://github.com/candle-usb/candleLight_fw/tree/master#building)自行编译固件。最终你会得到.bin文件，我们使用[STM32CubeProgrammer](https://www.st.com/zh/development-tools/stm32cubeprog.html)来将固件通过USB烧录到STM32中。下面展示了下载固件的步骤：

1. 将STM32上BOOT0引脚的下拉电阻取下，焊接上上拉电阻。
2. 将板子连接到电脑的USB口中，打开STM32CubeProgrammer。