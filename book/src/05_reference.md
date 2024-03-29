# 参考资料

## GPIO

GPIO 是通用输入输出（General Purpose Input Output）的缩写。 GPIO 是数字（有时也是模拟）信号引脚，可用作其他系统或设备的接口。每个引脚可以处于多种状态，且在上电或系统复位时进入默认状态（通常是无害的状态，例如数字输入）。然后我们可以编写软件，将它们更改为我们需要的状态。

下面将介绍几个与 GPIO 相关的概念：

### 引脚配置

GPIO 可以通过多种方式进行配置。可用的选项可能会根据芯片的设计而有所不同，但通常包括：

浮空：浮空引脚既不连接 VCC，也不连接地。它的电平只取决于外部施加的电压。需要注意的是，引脚应从外部拉低或拉高，因为如果引脚电平高于“低电压阈值”（`Vtl`），但低于“高电压阈值”（`Vth`），持续超过几微秒，可能会导致 CMOS 硅器件（例如微控制器）无法正常工作。

推挽输出：配置为推挽输出的引脚，可以将其驱动为高电平（即将其连接到 VCC），或将其驱动为低电平（即将其接地）。这对于 LED、蜂鸣器或其他耗电量较小的设备很有用。

开漏输出：开漏输出的引脚可以在“断路”和“接地”之间切换。通常会外接电阻将线路弱上拉至 VCC。这种类型的输出旨在允许多个设备连接在一起——如果连接到这条线路的任一设备将其驱动为低电平，则整条线路为低电平。如果两个或多个设备同时将其驱动为低电平，也不会发生损坏（地与地连接是安全的）。如果所有设备都没有将其驱动为低电平，则默认情况下电阻会将其拉高。

浮空输入：引脚上施加外部电压，可以在软件中读取为 `1`（如果电压高于某个阈值）或 `0`（如果低于阈值）。前述“浮空”状态的注意事项也适用于这个状态。

上拉输入：与浮空输入类似，不同之处在于存在一个内部的上拉电阻，它会在没有外部驱动器将线路下拉至地时，将线路弱上拉至 VCC。这对于读取按钮和其他开关的状态很有用，可以节省一个外部电阻。

### 高有效/低有效

数字信号有两种状态：“高”和“低”。这通常由信号与地之间的电压差来表示。哪种电平代表哪种状态是可以任意选定的，因此“高”和“低”都可以被定义为有效状态。

例如：一个高有效的引脚，在逻辑有效时应当是高电平。一个低有效的引脚，在逻辑无效时才是高电平。

在嵌入式 Rust 的抽象中，我们看到的是逻辑状态，而不是电平。所以如果有一个连接 LED 的低有效的引脚，你需要将其设置为无效状态才能点亮 LED。

### 片选

片选是发送给一个设备的二进制信号，可以部分或全部地，打开或关闭该设备。它通常是连接到 GPIO 的一条信号线，常用于允许多个设备连接到同一 SPI 总线上 —— 每个设备仅在其片选线处于有效状态时监听总线。

### Bit Banging

对于 I2C 或 SPI 等协议，我们通常使用 MCU 内的外设将我们想要传输的数据转换为信号。在某些情况下，例如，如果 MCU 不支持该协议，或者想要使用非标准形式的协议，则需要编写一个程序来手动将数据转换为信号。这称为 Bit Banging。


