# 中断

中断就是请求处理器中断当前执行的代码，以便及时处理某些事件。如果中断请求被接受，处理器就会暂停当前的活动，保存其状态，然后执行一个称作中断处理程序（interrupt handler）的函数，来处理某事件。中断常被硬件设备用于指示需要及时关注的电气或物理状态，例如，按钮被按下。

中断处理程序可能随时被调用，这为嵌入式 Rust 带来了一些挑战：需要有静态分配的、可变的内存，中断处理程序和主程序都可以引用它，而且这段内存必须随时是可用的。

## 挑战

### Flash 存储器

Flash 存储器不满足上面的要求，因为它在写入操作时是不可用的。在此期间发生的中断不会得到响应。在我们的例子中，这会导致按下按钮没有任何反应。我们通过将中断处理程序移动到 RAM 中来解决这个问题。
### 静态可变内存

在 Rust 中，这种内存可以用 `static mut` 来声明。但是读写此类变量总是 unsafe 的，如果不采取保护措施，可能会产生竞态条件。

如何处理这种问题？

在我们的例子中，ESP-IDF 框架提供了一个 `Queue` 类型，用于处理共享-可变的状态。我们只需获取一个 `QueueHandle` 来唯一标识一个正在使用的 `Queue`。然而，主线程是在运行时得到这个 `QueueHandle_t` 的，因此我们仍然需要少量的共享-可变状态，以便与中断处理程序共享 `QueueHandle_t`。我们使用 `Option<QueueHandle_t>`，将其静态初始化为 `None`，然后在 ESP-IDF 把队列创建出来时，再将它替换成 `Some(queue_handle)`。

在中断处理程序中，Rust 强制我们处理 `static mut` 仍然为 `None` 的情形。如果发生了这种情况，我们可以提前返回，或者直接 `unwrap()` 该值，如果它没有被提前设置为 `Some(queue_handle)`，就会产生错误，使程序退出。

仍然存在这样的风险：在中断处理程序触发时，`main()` 可能正在更改这个变量的值（即更改 `QueueHandle_t` 的值），从而使其处于不一致或无效状态。我们通过确保只设置一次该值来缓解这种情况，并且仅在使能中断之前设置。编译器无法检查这是否安全，因此我们在读取或写入该值时必须使用 `unsafe` 关键字。

<!-- An alternative to the `static mut` variable is to convert the `QueueHandle_t` to an integer, and store it in an `AtomicU32` or similar. These atomic types guarantee they can never be read in an intermediate or invalid state. However, they require special hardware support which is not available on the ESP32-C3. You would also still need to distinguish between a valid `QueueHandle_t` and some value that indicates the queue has not yet been created (perhaps `0xFFFF_FFFF`).
Yet another option is to use a special data structure which disables interrupts automatically when the value is being access. This guarantees that no code can interrupt you when reading or writing the value. This does however increase interrupt latency and in this case, because the `QueueHandle_t` is only written once, this is not necessary. -->

更多的信息可以参考 [Embedded Rust Book](https://docs.rust-embedded.org/book/concurrency/index.html)

## `unsafe {}` 块：

此代码包含许多的 `unsafe {}` 块。一般来说，`unsafe` 并不意味着所包含的代码不是内存安全的，而是意味着 Rust 无法在这个地方做出安全保证，并且程序员有责任确保内存安全。例如，调用 C 绑定本身就是不安全的，因为 Rust 无法为底层的 C 代码提供任何安全保证。


