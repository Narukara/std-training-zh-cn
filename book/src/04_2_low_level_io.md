# 底层 I/O：如何操作寄存器

ESP32-C3 的开发方式一般有两种。一种是仅使用 `[no_std]` Rust 的裸机编程，另一种是使用 `[std]` Rust 和 esp-idf 的 C 绑定。`[no_std]` Rust 是指不使用标准库，只使用核心库（core library），它是标准库的一个子集，不依赖于操作系统。

## 生态系统是什么样的？

### `[std]` Rust 和 esp-idf

在 ESP32-C3 上使用 Rust 的最成熟的方法是使用 esp-idf 的 C 绑定。走这条路时，我们可以使用 Rust 的标准库，因为我们可以使用操作系统：FreeRTOS。能够使用标准库带来了很多好处：我们可以使用所有类型，无论它们是在栈上分配的还是在堆上分配的。我们可以使用线程、互斥量和其他同步原语。

esp-idf 主要是用 C 编写的，因此将它以规范的、分离的 crate 的形式提供给 Rust：

- 一个 `sys` crate 提供了实际的 `unsafe` 绑定（[esp-idf-sys](https://github.com/esp-rs/esp-idf-sys)）
- 一个高级的 crate 提供了安全易用的 Rust 抽象（[esp-idf-svc](https://github.com/esp-rs/esp-idf-svc/)）

最后一部分是底层硬件访问，仍以分离的形式提供：

- [esp-idf-hal](https://github.com/esp-rs/esp-idf-hal) 实现了硬件无关的 [embedded-hal](https://github.com/rust-embedded/embedded-hal) traits，例如模数转换、数字 I/O 引脚、SPI 通信。正如它的名字所暗示的，它依赖于 `esp-idf`。

`esp-rs` book 的 [ecosystem 章节](https://esp-rs.github.io/book/overview/using-the-standard-library.html) 提供了更多信息。

如果你想使用 Rust，这就是目前在 Espressif 芯片上提供了最大可能性的开发方式。本课程中的所有内容都基于这种方法。

我们将在中断练习中研究，在此生态系统中如何直接将值写入寄存器。

### `[no_std]` 的 Rust 裸机编程

顾名思义，裸机就是不使用操作系统。正因为如此，我们无法使用依赖于操作系统的语言特性。核心库是标准库的一个子集，它不包括堆分配类型和线程等功能。仅使用核心库的代码标有 `#[no_std]`。`#[no_std]` 代码总能在 `std` 环境下运行，反之则不然。在 Rust 中，从寄存器到代码的映射是这样工作的：

设备上的寄存器及其字段由系统视图描述（System View Description，SVD）文件说明。`svd2rust` 用于从这些 SVD 文件生成外设访问 crate（Peripheral Access Crate，PAC）。PAC 为特定型号微控制器中的各个内存映射寄存器提供了一个很薄的封装。

虽然可以单独使用 PAC 编写固件，但这可能不安全或不太方便，因为它只提供了对微控制器外设的最基本的访问。所以还有另一层封装，即硬件抽象层（Hardware Abstraction Layer，HAL）。HAL 为芯片提供了更加用户友好的 API，并且通常实现了 [`embedded-hal`](https://github.com/rust-embedded/embedded-hal) 中定义的通用 trait。

微控制器通常焊接到一些 PCB 板上，这决定了每个引脚的连接情况。因此可以为给定的电路板编写板级支持 crate（Board Support Crate，BSC，也称为板级支持包或 BSP）。这提供了另一个抽象层，例如，可以为板上的各种传感器和 LED 提供 API——用户无需知道微控制器上的哪些引脚连接到这些传感器或 LED。

尽管存在用于 ESP32-C3 的 [PAC](https://github.com/esp-rs/esp-pacs/tree/main/esp32c3)，但裸机 Rust 在 ESP32-C3 芯片上的实验性很强，所以目前我们不会直接在微控制器上使用它。我们将用这种方法编写部分传感器的驱动程序，因为驱动程序应该与平台无关。



