# Hello, board!

现在我们已准备好进行一致性检查了！

✅ 将开发板的 USB-C 口连接到电脑，进入项目仓库中的 `hardware-check` 目录：

```console
cd intro/hardware-check
```

为了测试 Wi-Fi 连接，你需要提供你的网络名称（SSID）和密码（PSK）。这些凭据存储在专用的 `cfg.toml` 文件中（已被 `.gitignore` 忽略），以防因共享源代码或执行 pull request 而意外泄露。项目里已经提供了一个例子。

✅ 将 `cfg.toml.example` 复制到 `cfg.toml`（在同一目录中），将实际的 SSID 和 PSK 写入其中：

> ⚠️ [ESP32-C3 不支持 5GHz 频段](https://www.espressif.com/en/news/ESP32-C3_Wi-Fi_Certified#:~:text=ESP32%2DC3%20is%20a%20safe,wide%20range%20of%20IoT%20applications)，你需要确保你使用的 WiFi 具有可用的 2.4GHz 频段。

```console
$ cp cfg.toml.example cfg.toml
$ $EDITOR cfg.toml
$ cat cfg.toml

[hardware-check]
wifi_ssid = "Your Wifi name"
wifi_psk = "Your Wifi password"
```


✅ 构建、烧写并监视（monitor）这个项目:

```console
$ cargo run

Serial port: /dev/SERIAL_DEVICE
Connecting...

Chip type:         ESP32-C3 (revision 3)
(...)
Compiling hardware-check v0.1.0
Finished release [optimized] target(s) in 1.78s

[00:00:45] ########################################     418/418     segment 0x10000

Flashing has completed!
(...)
rst:0x1 (POWERON),boot:0xc (SPI_FAST_FLASH_BOOT)
(...)
(...)
(...)
I (4427) wifi::wifi: Wifi connected!
```

>🔎 如果成功运行了 `cargo run`，你可以通过 `ctrl+C` 退出。

> 🔎 `cargo run` 被[配置为使用 `espflash`](https://github.com/esp-rs/std-training/blob/main/intro/hardware-check/.cargo/config.toml#L6) 作为[自定义 runner](https://doc.rust-lang.org/cargo/reference/config.html#target)。以下方法也会得到相同的输出：
> - 使用 `cargo-espflash`：`cargo espflash flash --release --monitor`
> - 用 `espflash` 构建项目并烧写：`cargo build --release && espflash target/riscv32imc-esp-espidf/release/hardware-check`
> 为方便起见，这个改动已经应用于本教程的所有项目。

板上的 LED 应在启动时变为黄色，然后根据是否成功建立 Wifi 连接，变为红色（错误），或交替闪烁绿色和蓝色（成功）。如果出现 Wifi 错误，诊断消息也会显示在下面，例如：

```console
Error: could not connect to Wi-Fi network: ESP_ERR_TIMEOUT
```
> ⚠️ 如果你的网络名或密码不正确，也会得到 `ESP_ERR_TIMEOUT`。所以请仔细检查它们。

## 关于构建、烧写和监视的额外信息

如果想尝试在不烧写的情况下构建，可以运行：

 ```console
 cargo build
 ```
也可以使用以下命令监视设备而不重新烧写程序：

```console
espflash monitor
```


# Troubleshooting

## 构建错误

```console
error[E0463]: can't find crate for `core`
= note: the `riscv32imc-esp-espidf` target may not be installed
```

这说明你在尝试用 `stable` Rust 构建——你需要使用 `nightly`。
这个错误信息有一些误导性——这个目标无法安装。它需要使用 `build-std` 从源码构建，这是一个仅在 nightly 版本可用的特性。

---

```console
error: cannot find macro `llvm_asm` in this scope
```

你使用的 nightly 版本不兼容——用 `rust-toolchain.toml` 或 `cargo override` 配置一个合适的。

---

```console
CMake Error at .../Modules/CMakeDetermineSystem.cmake:129 (message):
```

你的 Espressif 工具链可能被损坏了。删除它，然后重新构建来触发新的下载：

```console
rm -rf ~/.espressif
```
在 Windows 上，删除 `%USERPROFILE%\.espressif` 文件夹。

---

 ```console
Serial port: /dev/tty.usbserial-110
Connecting...

Unable to connect, retrying with extra delay...
Unable to connect, retrying with default delay...
Unable to connect, retrying with extra delay...
Error: espflash::connection_failed

× Error while connecting to device
╰─▶ Failed to connect to the device
help: Ensure that the device is connected and the reset and boot pins are not being held down
 ```

无法通过 USB-C 线缆连接到开发板。典型的连接错误如上面所示。


解决方法：
1. 按住板子上的 boot 按钮，启动烧写命令，开始烧写后松开按钮
2. 使用集线器（hub）

[来源](https://georgik.rocks/unable-to-flash-esp32-with-these-usb-c-cables/)。