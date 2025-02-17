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

某些项目可以使用 [Wokwi][wokwi] 进行模拟，或者至少部分模拟。以下是支持 Wokwi 模拟的项目列表：
- `intro/hello-world`
- `intro/http-client`
- `advanced/i2c-sensor-reading`
- `advanced/i2c-driver`
- `advanced/button-interrupt`

在开始任何模拟项目之前，你需要[安装扩展][wokwi-installation]。要模拟一个项目：

1. 按下 F1，选择 `Wokwi: Select Config File`，然后选择你要模拟的项目的 `wokwi.toml` 文件。
   1. 编辑对应的 `wokwi.toml` 文件，以模拟练习部分或解答部分。
2. 以 `debug` 模式构建你的项目。
3. 再次按下 F1，然后选择 `Wokwi: Start Simulator`。

你也可以[调试项目][wokwi-debug]。

[wokwi]: https://wokwi.com/
[wokwi-installation]: https://docs.wokwi.com/vscode/getting-started#installation
[wokwi-debug]: https://docs.wokwi.com/vscode/debugging

## 确保有一个可用的开发环境
<!-- TODO: Update this comments -->

⚠️ 如果你正在参加由 Ferrous Systems 开展的培训，我们强烈建议你至少提前一个工作日按照本章中的说明为培训做好准备。如果你遇到任何问题或需要任何类型的支持，请[联系我们](https://ferrous-systems.com/contact/)。

⚠️ 如果你正在使用 [ESP32-C3-DevKitC-02](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/hw-reference/esp32c3/user-guide-devkitc-02.html)，一些引脚和从机地址会有所不同，因为两块板子不完全相同。这与 [advanced/i2c-sensor-reading/](/advanced/i2c-sensor-reading/examples) 和 [advanced/i2c-driver/](/advanced/i2c-driver/src/) 中的解答有关，其中用于 ESP32-C3-DevKitC-02 的引脚和从机地址已被注释。

## 配套材料

- [官方 esp-rs book](https://esp-rs.github.io/book/introduction.html) 