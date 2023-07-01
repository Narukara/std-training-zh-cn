# 按钮随机设置 LED 颜色

✅ 修改代码，使 RGB LED 灯在每次按下按钮时变为随机颜色。如果一段时间内未按下按钮，LED 不应熄灭或改变颜色。

你可以在先前的代码上继续修改，或者从 `advanced/button-interrupt/src/main.rs` 开始着手。

`advanced/button-interrupt/examples/solution.rs` 包含本练习的解答。可以用以下命令运行：

```console
cargo run --example solution_led
```

## 💡 帮助信息

* 必要的 crate 都已经导入，你可以用 `cargo --doc --open` 查看 LED 的帮助文档。
* LED 的型号是 WS2812RMT。
* 这是一个可编程的 RGB LED。这意味着不存在单独的，用于设置红、绿、蓝的引脚。我们需要实例化它，然后才能发送 `RGB8` 类型的值给它。
* 这个板子有硬件随机数生成器，可以用 `esp_random()` 调用它。
* 从 Rust 的角度来看，调用 `esp-idf-sys` 中的一些函数是 unsafe 的，并且需要 `unsafe()` 块。不过你可以假设这些功能可以安全使用，不需要其他保护措施。

