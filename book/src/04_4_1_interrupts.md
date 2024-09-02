# 编写中断处理程序

本练习的目标是处理按下 `BOOT` 按钮时触发的中断。

`advanced/button-interrupt/src/main.rs` 中包含本练习的代码框架。

`advanced/button-interrupt/examples/solution.rs` 包含本练习的解答。可以用以下命令运行：

```console
cargo run --example solution
```
## ✅ 任务

1. 用 `PinDriver` 结构体和以下设置来配置 [BOOT 按钮](https://github.com/esp-rs/esp-rust-board#ios)（GPIO9）：
    - 输入模式
    - 上拉
    - 上升沿触发中断
2. 实例化一个新的通知（notification）和 notifier
    - 查看 `hal::task::notification` 文档
3. 在 `unsafe` 块中，创建一个订阅（subscription）及其回调函数
    - 查看 `PinDriver::subscribe` 和 `task::notify_and_yield`
    - `unsafe` 的原因是：
      - 回调函数会运行在 [ISR（中断服务函数）](https://en.wikipedia.org/wiki/Interrupt_handler)中，所以我们需要避免调用任何可能阻塞的函数，包括 STD， `libc` 或 FreeRTOS API（少数允许的除外）。
      - 回调闭包会从环境中捕获东西，你可以使用其中的静态变量。捕获的变量需要具有比订阅（subscription）更长的生命周期。你也可以使用非静态变量，但这需要额外小心，更多细节请参阅 `esp_idf_hal::gpio::PinDriver::subscribe_nonstatic` 文档。
4. 在循环中，使能中断，并等待通知（notification）
    - 应在每次收到通知后，使能中断（在非 ISR 上下文中）
    - `esp_idf_svc::hal::delay::BLOCK` 可以用于等待
5.  运行程序，按下 `BOOT` 按钮，看看效果如何！

🔎 在本练习中，我们使用通知（notification），它只会提供最新的值。
因此如果在读取通知的值之前，中断被多次触发，你只能得到最新的值。
另一方面，队列允许接收多个值。更多详细信息请参阅 `esp_idf_hal::task::queue::Queue`。

## Simulation

This project is available for simulation through two methods:
- Wokwi projects
  - [Exercise](https://wokwi.com/projects/360623288920412161?build-cache=disable)
  - [Solution](https://wokwi.com/projects/333374799393849940?build-cache=disable)
    - The Solution project contains solution for [Random LED Color on pushinig a Button](./04_4_2_interrupts.md)
- Wokwi files are also present in the project folder to simulate it with Wokwi VS Code extension:
   1. Press F1, select `Wokwi: Select Config File` and choose `advanced/button-interrupt/wokwi.toml`
      - Edit the `wokwi.toml` file to select between exercise and solution simulation
   2. Build you project
   3. Press F1 again and select `Wokwi: Start Simulator`