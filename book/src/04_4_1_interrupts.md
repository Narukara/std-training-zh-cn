# 编写中断处理程序

本练习的目标是处理按下 `BOOT` 按钮时触发的中断。
此练习涉及使用 ESP-IDF 的 C 绑定和其他 unsafe 操作，以及非典型 Rust 文档。首先，我们将逐行构建这个中断处理程序。

`advanced/button-interrupt/src/main.rs` 中包含本练习的代码框架。

`advanced/button-interrupt/examples/solution.rs` 包含本练习的解答。可以用以下命令运行：

```console
cargo run --example solution
```
## ✅ 任务

1. 用一个 C 结构体 [`gpio_config_t`](https://esp-rs.github.io/esp-idf-sys/esp_idf_sys/struct.gpio_config_t.html) 和以下信息来配置 [BOOT 按钮](https://github.com/esp-rs/esp-rust-board#ios)（GPIO9）：
    - 输入模式
    - 上拉
    - 上升沿触发中断

该结构体有以下字段：

 * `pin_bit_mask`：表示引脚编号：把数字 1 左移引脚编号位，作为其值。
 * `mode`：设置引脚的模式，允许以下配置：
   * `gpio_mode_t_GPIO_MODE_INPUT`
   * `gpio_mode_t_GPIO_MODE_OUTPUT`
   * `gpio_mode_t_GPIO_MODE_DISABLE` // 失能 GPIO
   * `gpio_mode_t_GPIO_MODE_OUTPUT_OD` // 开漏输出
   * `gpio_mode_t_GPIO_MODE_INPUT_OUTPUT` // 输入 & 输出
   * `gpio_mode_t_GPIO_MODE_INPUT_OUTPUT_OD` // 开漏输入 & 输出
 * `pull_up_en`：如果上拉，取 `true.into()`
 * `pull_down_en`：如果下拉，取 `true.into()`
 * `intr_type`：设置中断类型，允许以下配置：
   * `gpio_int_type_t_GPIO_INTR_ANYEDGE` // 双边沿触发
   * `gpio_int_type_t_GPIO_INTR_DISABLE` // 失能
   * `gpio_int_type_t_GPIO_INTR_NEGEDGE` // 下降沿触发
   * `gpio_int_type_t_GPIO_INTR_POSEDGE` // 上升沿触发

它们都是常数，其值代表需要在相应寄存器中设置的位。

2. 用 [`unsafe extern "C" fn gpio_config`](https://esp-rs.github.io/esp-idf-sys/esp_idf_sys/fn.gpio_config.html) 将这些配置写入相应寄存器。这些操作需要在 unsafe 块中进行。为了进行这些 FFI 调用，可以使用宏 `esp!($Cfunktion)`。

3. 用 [`unsafe extern "C" fn gpio_install_isr_service`](https://esp-rs.github.io/esp-idf-sys/esp_idf_sys/fn.gpio_install_isr_service.html) 设置一个通用的 GPIO 中断处理程序。这个函数以 `ESP_INTR_FLAG_IRAM` 作为参数。

4. 创建一个 `static mut` 来保存我们将要从 `xQueueGenericCreate` 中获取的队列句柄。这是一个用于唯一标识一个队列的数字。而队列存储本身是由操作系统来管理的。

    ```rust
    static mut EVENT_QUEUE: Option<QueueHandle_t> = None;
    ```

5. 用 [`pub unsafe extern "C" fn xQueueGenericCreate`](https://esp-rs.github.io/esp-idf-sys/esp_idf_sys/fn.xQueueGenericCreate.html) 创建事件队列。这允许我们安全地从中断程序向主线程传递事件。

    ```rust
    EVENT_QUEUE = Some(xQueueGenericCreate(QUEUE_SIZE, ITEM_SIZE, QUEUE_TYPE_BASE));
    ```

6. 添加一个函数，每当按钮引脚上产生了 GPIO 中断时，就会调用该函数。我们将这个函数放在 RAM 的一个特殊块（`iram0`）中，如此一来，即使外部 flash 忙于其他操作（如文件系统操作）时，这个函数仍然可用。这个函数需要从 `EVENT_QUEUE` 获取队列句柄，并使用 `std::ptr::null_mut()` 调用 `xQueueGiveFromISR` 函数——队列中的对象大小为零，事实上，我们不需要放置什么东西在队列中。相反，向其中推一个空的东西，就足以唤醒另一端了！

    ```rust
    #[link_section = ".iram0.text"]
    unsafe extern "C" fn button_interrupt(_: *mut c_void) {
        xQueueGiveFromISR(EVENT_QUEUE.unwrap(), std::ptr::null_mut());
    }
    ```

每当中断被触发，就会添加一个事件到队列中。

7. 将我们刚刚编写的函数传递给之前注册的通用 GPIO 中断处理程序，并指定会触发它的 GPIO 引脚编号。

    ```rust
    esp!(gpio_isr_handler_add(
        GPIO_NUM,
        Some(button_interrupt),
        std::ptr::null_mut()
    ))?;
    ```

8. 在一个循环中，等待队列有项目进入。也就是说，等待 `button_interrupt` 函数将一些东西放入队列中。

    ```rust
    let res = xQueueReceive(EVENT_QUEUE.unwrap(), ptr::null_mut(), QUEUE_WAIT_TICKS);
    ```

9. 处理 `res` 的值，在按钮被按下时打印 “Button pushed!”。

10. 运行程序并按下 `BOOT` 按钮，看看效果如何！
