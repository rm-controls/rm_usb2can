# rm_usb2can

![Version](https://img.shields.io/badge/Version-1.0.3-brightgreen.svg)&nbsp;&nbsp;![Build](https://img.shields.io/badge/Build-Passed-success.svg)&nbsp;&nbsp;![License](https://img.shields.io/badge/License-MIT-blue.svg)

[English](https://github.com/rm-controls/rm_usb2can/blob/main/README.md)/中文

***

### 简介

&nbsp;&nbsp;&nbsp;&nbsp;本项目的开发目的是为了给NUC提供一个CAN的外设接口。由于NUC不具有SPI等简单外设接口，故无法使用MCP2515等SPI转CAN芯片。为此，本项目基于github的开源方案[candleLight](https://github.com/candle-usb/candleLight_fw/tree/master)开发，采用STM32F072CBT6作为主控芯片，实现USB转CAN的功能。STM32F072CBT6具有能够同时工作的USB全速外设和CAN外设，封装为QFP64，是较为优秀的解决方案。

