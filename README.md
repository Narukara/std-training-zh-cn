https://github.com/ferrous-systems/espressif-trainings 的中文翻译

---

# Embedded Rust Trainings for Espressif

这个仓库包含了在 Espressif ESP32-C3 上使用嵌入式 Rust 的学习材料。

我们推荐你通过阅读 [这本书](https://espressif-trainings.ferrous-systems.com) 来开始学习。

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/ferrous-systems/espressif-trainings)

## 内容

包括：

* 一本你可以通读的书 - ([Source](./book)) ([Published](https://espressif-trainings.ferrous-systems.com))
* 一些入门级的例子：
   * 基础的硬件检查 ([Source](./intro/hardware-check))
   * 一个 HTTP 客户端 ([Source](./intro/http-client))
   * 一个 HTTP 服务器 ([Source](./intro/http-server))
   * 一个 MQTT 客户端 ([Source](./intro/mqtt))
* 一些高级例子：
   * 底层 GPIO
   * 中断
   * I2c 驱动 ([Source](./advanced/i2c-driver))
   * I2c 传感器的读取 ([Source](./advanced/i2c-sensor-reading))
   * GPIO/按钮中断 ([Source](./advanced/button-interrupt))
   * 驱动一个 RGB LED
* 一些有用的常见 crates：
   * [`esp32-c3-dkc02-bsc`](./common/lib/esp32-c3-dkc02-bsc) - ESP32-C3-DKC02 的板级支持
   * [`get-uuid`](./common/lib/get-uuid) - 提供一个编译期生成的 UUID
   * [`mqtt-messages`](./common/lib/mqtt-messages) - MQTT 辅助函数
   * [`icm42670p`](./common/lib/icm42670p) - 基本的传感器驱动
* 一些额外的东西：
   * [`mqtt-python-client`](./extra/mqtt-python-client) 一个 Python MQTT 客户端，测试用

请注意，这些材料的大部分仍在编写中！

## 开发

这里提供的每一个 Rust 示例 crate 都可以以通常的方式构建。细节信息请参阅 [Embedded Rust Bookshelf](https://docs.rust-embedded.org) 或每个 crate 的 README.md。

这本书使用 Markdown 编写，使用 [mdbook](https://crates.io/crates/mdbook)。你可以通过运行以下命令来渲染一个本地副本：

```console
~ $ cargo install mdbook
~ $ git clone https://github.com/ferrous-systems/espressif-trainings.git
~ $ cd espressif-trainings/book
~/espressif-trainings/book $ mdbook serve
```

一个本地的 web 服务器会在 <http://127.0.0.1:3000> 上启动，你可以在这里阅读渲染得到的书。当你修改硬盘上的 Markdown 文件时，它会自动更新。

Note that you __must__ not push to the `main` branch. Instead undertake any
changes in a branch, either in this repository (if you have access) or in a
fork. Please do then feel free to open a Pull Request in Github to merge the
changes to our `main` branch.

This work is continually updated and as such there are no 'releases'. Every
commit to `main` gets published to
<https://espressif-trainings.ferrous-systems.com> automatically.

## Licence

The material in this repository is licensed
[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). All
material is Copyright 2022 Ferrous Systems GmbH, unless otherwise stated.

You are free to __Share__ and __Adapt__ but you must give __Attribution__ and
__Share Alike__.

In addition, the source code contained within this repository (either in the
book, or as separate examples) is made available under either the
[MIT](./LICENSE-MIT.txt) or [Apache-2.0](./LICENSE_APACHE.txt) licenses, at
your option.

## Contribution

Unless you explicitly state otherwise, any contribution intentionally
submitted for inclusion in the work by you, as defined in the Apache-2.0
license, shall be licensed as above, without any additional terms or
conditions.

## Code of Conduct

Contribution to this crate is organized under the terms of the [Rust Code of
Conduct][CoC], and the maintainer of this crate, Ferrous Systems GmbH, promises
to intervene to uphold that code of conduct.

[CoC]: CODE_OF_CONDUCT.md
