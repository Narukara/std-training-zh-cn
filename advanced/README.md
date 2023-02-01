# Advanced 文件夹代码组织

文件夹按字母顺序列出，而非课程中教授的顺序。

## 按键中断

`button-interrupt/exercise/src/main.rs` 包含项目代码框架。
`button-interrupt/solution/src/main.rs` 包含练习第一步的解答。
`button-interrupt/solution/src/main_led.rs` 包含练习第二步的解答。

## I2C 驱动练习 (WIP)

`i2c-driver/exercise/src/icm42670p.rs` 将会是一个非常基础的 i2c IMU 传感器驱动的填空版本。任务是补全这个文件，使得运行 `main.rs` 可以记录驱动的设备 ID。这个驱动基于 common 中的同名版本，但是提供了更多功能。

`i2c-driver/exercise/src/main.rs` 将包含可以运行的代码。在不修改驱动文件的情况下运行它会产生错误。

`i2c-driver/solution/src/icm42670p.rs` 提供任务的一个解答。

## I2C 传感器读取练习 (WIP)

 `i2c-sensor-reading/exercise/src/main.rs` 是一个将要建立的练习框架。任务是使用一个已存在于 crates.io 中的驱动，通过 i2c 读取温度和湿度传感器。之后，通过 `shared-bus` 在同一条 i2c 总线上读取第二个传感器。第二个传感器的驱动在 `common/` 中。

`i2c-sensor-reading/solution/src/main.rs` 包含一个可以运行的解答。

