# 检查硬件

将 Espressif Rust 开发板连接到你的电脑。确认一个红色的小 LED 被点亮了。

此设备应当通过 USB 提供了一个 UART 串口：

**Windows**：一个 USB 串行设备（COM 端口），在设备管理器的“端口”部分。

**Linux**：`lsusb` 下的一个 USB 设备。
这个设备的 VID（vendor ID）为 `303a`，PID（product ID）为 `1001`——`lsusb` 的输出中会省略 `0x` 前缀：

``` console
$ lsusb | grep USB
Bus 006 Device 035: ID 303a:1001 Espressif USB JTAG/serial debug unit
```

另一个查看设备，以及相关权限和端口的方法是检查 `/by-id` 目录：

``` console
$ ls -l /dev/serial/by-id
lrwxrwxrwx 1 root root .... usb-Espressif_USB_JTAG_serial_debug_unit_60:55:F9:C0:27:18-if00 -> ../../ttyACM0
```
> 如果你在使用 ESP32-C3-DevKitC-02，使用 `$ ls /dev/ttyUSB*` 命令

**macOS**：此设备将显示为 `system_profiler` 中 USB 树的一部分：

```console
$ system_profiler SPUSBDataType | grep -A 11 "USB JTAG"

USB JTAG/serial debug unit:

  Product ID: 0x1001
  Vendor ID: 0x303a
  (...)
```

此设备还将作为 `tty.usbmodem` 设备显示在 `/dev` 目录中：

``` console
$ ls /dev/tty.usbmodem*
/dev/tty.usbmodem0
```
