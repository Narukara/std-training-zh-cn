<p style="text-align:center;"><img src="./assets/esp-logo-black.svg" width="50%"></p>

# 引言

## 本材料的内容

这里是 Ferrous Systems 的 *Embedded Rust on Espressif* 培训材料，分为两个部分：入门和进阶。入门部分将向你介绍嵌入式开发的基础知识，以及如何使开发板与外界交互——对命令做出反应，并发送传感器数据。

进阶部分将深入探讨中断处理、低级外设访问和编写你自己的驱动程序等主题。

你可以加入 Matrix 上的 [esp-rs 社区](https://matrix.to/#/#esp-rs:matrix.org) 来探讨任何技术问题！社区对所有人开放。

## 翻译

这里是本书的简体中文译本。原书（英文版）及其他译本如下：

- [English](https://esp-rs.github.io/std-training/) ([仓库](https://github.com/esp-rs/std-training/))

## 开发板

本书要求使用 [Rust ESP 开发板](https://github.com/esp-rs/esp-rust-board)[^note]——不支持像 QEMU 这样的模拟器。


电路板的设计、图片、引脚布局和原理图也可以在此仓库中找到。

如果你订阅了其中一项培训，乐鑫将直接为你提供一块开发板。

我们的重点主要在 [ESP32-C3](https://www.espressif.com/en/products/socs/esp32-c3) 平台，一个基于 [`RISC-V`](https://riscv.org/) 的，具有强大物联网功能的微控制器，集成 Wi-Fi 和 Bluetooth 5 (LE) 功能，以及适用于复杂应用的大容量 RAM 和 Flash。 本教程的大部分内容也适用于 Espressif 使用的其他架构（`Xtensa`），特别是 [ESP32-S3](https://www.espressif.com/en/products/socs/esp32-s3)。 对于底层访问，原理上是一样的，但实际的硬件访问会各有不同——请根据需要参阅技术参考手册（[C3](https://www.espressif.com/sites/default/files/documentation/esp32-c3_technical_reference_manual_en.pdf)，[S3](https://www.espressif.com/sites/default/files/documentation/esp32-s3_technical_reference_manual_en.pdf)）或[其他可用的技术文档](https://www.espressif.com/en/support/documents/technical-documents)。

## Rust 知识

- 基本的 Rust 知识：[The Rust Book](https://doc.rust-lang.org/book/) 第 1 到第 6 章，第4章“所有权”不需要完全理解。
- [The Rust on ESP Book](https://esp-rs.github.io/book/) 不是必须的，但是强烈推荐。因为它能帮助你理解 Rust on ESP 生态系统和许多将在本教程中讨论到的概念。

[^note]: 也可以使用 [ESP32-C3-DevKitC-02](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/hw-reference/esp32c3/user-guide-devkitc-02.html) 学习入门部分，但并不推荐。使用本书要求的硬件学起来更简单。

