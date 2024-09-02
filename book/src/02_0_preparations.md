# 准备工作

本章包含了有关教程材料、所需硬件的信息，以及一个安装指南。

## 本书使用的图标和格式

本书使用图标来标记书中不同种类的信息：
* ✅ 需要动手尝试。
* ⚠️ 警告和需要特别关注的细节。
* 🔎 深入某个主题的知识，但不需要了解这些知识即可继续阅读。
* 💡 在练习中可能对你有帮助的提示

> 注释示例：像这样的注释包含了有用的信息

## 代码注释

在某些 Rust 源文件里，有类似这样的 anchor 注释：
```rust,ignore
// ANCHOR: test
let foo = 1;
...
// ANCHOR_END: test
```
你可以忽略 Anchor 注释，它们只是用于把包含的代码导入到本书中。详见 [`mdBook` 文档](https://rust-lang.github.io/mdBook/format/mdbook.html#including-portions-of-a-file)

## 需要的硬件

- [Rust ESP 开发板](https://github.com/esp-rs/esp-rust-board)：可以在 Mouser、Aliexpress 上购买。[完整的供应商名单](https://github.com/esp-rs/esp-rust-board#where-to-buy)。
- 用于连接开发板和 PC 的 USB-C 线缆。
- 联入互联网的 Wi-Fi 接入点。

> 不需要额外的调试器硬件。

## 项目仿真

某些项目可以用 [Wokwi][wokwi] 仿真。寻找书中的指示来确认可以仿真的项目。仿真有两种方法实现：
- 使用 wokwi.com：直接在浏览器中执行构建、编辑代码。
- 使用 [Wokwi VS Code 扩展][wokwi-vscode]：用 VS Code 来编辑项目、执行构建。用 Wokwi VS Code 扩展对生成的二进制文件进行仿真。
    - 这种方法需要一些[安装][wokwi-installation]
    - 这种方法假定项目是在 debug 模式下构建的
    - 这种方法允许[调试项目][wokwi-debug]

[wokwi]: https://wokwi.com/
[wokwi-vscode]: https://docs.wokwi.com/vscode/getting-started
[wokwi-installation]: https://docs.wokwi.com/vscode/getting-started#installation
[wokwi-debug]: https://docs.wokwi.com/vscode/debugging

## 确保有一个可用的开发环境
<!-- TODO: Update this comments -->

⚠️ 如果你正在参加由 Ferrous Systems 开展的培训，我们强烈建议你至少提前一个工作日按照本章中的说明为培训做好准备。如果你遇到任何问题或需要任何类型的支持，请[联系我们](https://ferrous-systems.com/contact/)。

⚠️ 如果你正在使用 [ESP32-C3-DevKitC-02](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/hw-reference/esp32c3/user-guide-devkitc-02.html)，一些引脚和从机地址会有所不同，因为两块板子不完全相同。这与 [advanced/i2c-sensor-reading/](/advanced/i2c-sensor-reading/examples) 和 [advanced/i2c-driver/](/advanced/i2c-driver/src/) 中的解答有关，其中用于 ESP32-C3-DevKitC-02 的引脚和从机地址已被注释。

## 配套材料

- [官方 esp-rs book](https://esp-rs.github.io/book/introduction.html) 