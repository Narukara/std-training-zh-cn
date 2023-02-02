<p style="text-align:center;"><img src="./assets/esp-logo-black.svg" width="50%"></p>

# 介绍

## 本材料的内容

这是 Ferrous Systems 的 *Embedded Rust on Espressif* 培训材料。它分为两个部分：入门和高级。入门部分将向您介绍嵌入式开发的基础知识以及如何使开发板与外界交互——对命令做出反应并发送传感器数据。

高级课程将深入探讨中断处理、低级外设访问和编写您自己的驱动程序等主题。

您可以加入 Matrix 上的 [esp-rs 社区](https://matrix.to/#/#esp-rs:matrix.org) 来探讨任何技术问题！社区对所有人开放。

## 开发板

本书必须使用 [Espressif Rust Board](https://github.com/esp-rs/esp-rust-board)[^note]——不支持像 QEMU 这样的模拟器。

电路板设计、图片、引脚布局和原理图也可以在此仓库中找到。

如果您订阅了其中一项培训，乐鑫将直接为您提供一块开发板。一些练习还需要接入无线网。



我们的重点主要在 [ESP32-C3](https://www.espressif.com/en/products/socs/esp32-c3) 平台，一个基于 [RISC-V](https://riscv.org/) 的具有强大物联网功能的微控制器，集成 Wi-Fi 和 Bluetooth 5 (LE) 功能以及适用于复杂应用的大容量 RAM 和 Flash。 本课程的大部分内容也适用于 Espressif 使用的其他架构（Xtensa），特别是 [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3)。 对于底层访问，一般原则也适用，但实际的硬件访问会各有不同——请根据需要参阅技术参考手册（[C3](https://www.espressif.com/sites/default/files/documentation/esp32-c3_technical_reference_manual_en.pdf)，[S3](https://www.espressif.com/sites/default/files/documentation/esp32-s3_technical_reference_manual_en.pdf)）或[其他可用的技术文档](https://www.espressif.com/en/support/documents/technical-documents)。


## Rust 知识

基本的 Rust 知识：[The Rust Book](https://doc.rust-lang.org/book/) 第 1 到第 6 章，第4章“所有权”不需要完全理解。


[^note]: 可以使用 [ESP32-C3-DevKitC-02](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/hw-reference/esp32c3/user-guide-devkitc-02.html) 学习入门部分，但我们不推荐它。使用与本书要求一致的硬件会更易学。

