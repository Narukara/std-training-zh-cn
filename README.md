这里是 https://github.com/esp-rs/std-training 的简体中文翻译

目前进度：已经翻译完成（部分更新的内容需要重新翻译），跟踪到 3ce0971

---

# Embedded Rust Trainings for Espressif



这个仓库包含了在 Espressif ESP32-C3 上使用嵌入式 Rust 的学习材料。

我们推荐你通过阅读 [这本书](https://narukara.github.io/std-training-zh-cn/) 来开始学习。

## 内容

包括：

* 一本你可以通读的书 - ([源代码](./book)) ([网页](https://narukara.github.io/std-training-zh-cn/))
* 一些入门级的例子：
   * 基础的硬件检查 ([源代码](./intro/hardware-check))
   * 一个 HTTP 客户端 ([源代码](./intro/http-client))
   * 一个 HTTP 服务器 ([源代码](./intro/http-server))
   * 一个 MQTT 客户端 ([源代码](./intro/mqtt))
* 一些高级例子：
   * 底层 GPIO
   * 中断
   * I2c 驱动 ([源代码](./advanced/i2c-driver))
   * I2c 传感器的读取 ([源代码](./advanced/i2c-sensor-reading))
   * GPIO/按钮中断 ([源代码](./advanced/button-interrupt))
   * 驱动一个 RGB LED
* 一些有用且常见的 crates：
   * [`get-uuid`](./common/lib/get-uuid) - 提供一个编译期生成的 UUID
   * [`mqtt-messages`](./common/lib/mqtt-messages) - MQTT 辅助函数
   * [`rgb-led`](./common/lib/rgb-led) - 提供对 RGB LED（WS2812）的支持
   * [`wifi`](./common/lib/wifi) - Wifi 辅助函数

## 开发

这里提供的每一个 Rust 示例 crate 都可以以通常的方式构建。
细节信息请参阅 [Embedded Rust Bookshelf](https://docs.rust-embedded.org) 
或每个 crate 的 README.md。

这本书使用 Markdown 编写，
使用 [mdbook](https://crates.io/crates/mdbook)。
你可以通过运行以下命令来渲染一个本地副本：

```console
~ $ cargo install mdbook
~ $ git clone https://github.com/Narukara/std-training-zh-cn.git
~ $ cd std-training-zh-cn/book
~/std-training-zh-cn/book $ mdbook serve
```

一个本地的 web 服务器会在 <http://127.0.0.1:3000> 上启动，
你可以在这里阅读渲染得到的书。
当你修改硬盘上的 Markdown 文件时，它会自动更新。

Note that you __must__ not push to the `main` branch. Instead undertake any
changes in a branch, either in this repository (if you have access) or in a
fork. Please do then feel free to open a Pull Request in Github to merge the
changes to our `main` branch.

This work is continually updated and as such there are no 'releases'. Every
commit to `main` gets published to
<https://esp-rs.github.io/std-training> automatically.

## Licence

The material in this repository is licensed
[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). All
material is Copyright 2022 Ferrous Systems GmbH, unless otherwise stated.

You are free to __Share__ and __Adapt__ but you must give __Attribution__ and
__Share Alike__.

In addition, the source code contained within this repository (either in the
book, or as separate examples) is made available under either the
[MIT](./LICENSE-MIT.txt) or [Apache-2.0](./LICENSE-APACHE.txt) licenses, at
your option.

## Contribution

Unless you explicitly state otherwise, any contribution intentionally
submitted for inclusion in the work by you, as defined in the Apache-2.0
license, shall be licensed as above, without any additional terms or
conditions.

## Authors

The content of this training was created by Ferrous Systems GmbH and Espressif Systems.

## Code of Conduct

Contribution to this crate is organized under the terms of the [Rust Code of
Conduct](https://www.rust-lang.org/policies/code-of-conduct), and the maintainers of this crate promises to intervene to
uphold that code of conduct.
