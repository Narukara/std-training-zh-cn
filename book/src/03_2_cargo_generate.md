# 创建新项目

现在让我们用 [`cargo-generate`](https://github.com/cargo-generate/cargo-generate) （一个通用的项目生成向导）来配置我们的第一个项目。

> 本课程中的其他大多数练习都已经提供了项目框架，不需要使用 `cargo-generate`。

✅ 进入 `intro` 目录并运行 `cargo generate`，使用 `esp-idf` 模板：

```shell
$ cd intro
$ cargo generate --git https://github.com/esp-rs/esp-idf-template cargo
```

`cargo-generate` 将提示有关新项目的详细信息。当在多个选项中进行选择时，可以使用光标向上/向下，并使用回车键确定。

你看到的第一条消息会是：`⚠️Unable to load config file: /home/$USER/.cargo/cargo-generate.toml`。出现这个错误是因为没有偏好的配置文件。但这不是必须的，你可以忽略这个警告。

🔎 你可以创建一个 [偏好的配置文件](https://cargo-generate.github.io/cargo-generate/favorites.html)，放在 `$CARGO_HOME/cargo-generate`。可以使用 `-c, --config <config-file>` 覆盖它。 


> 如果误操作了，按下 `Ctrl+C` 然后重新开始。

✅ 配置你的项目：

（这些项可能以不同的顺序出现）

* Project Name: `hello-world`
* Rust toolchain: `nightly`
* MCU: `esp32c3`
* ESP-IDF native build version: `4.4`
* STD support: `true`

我们将使用 Espressif 构建系统的 `native` 变体进行构建。

✅ Enable the native build system by opening `Cargo.toml` in your new `hello-world` project and adding `"native"` as default feature:

```toml
[features]
default = ["native"] # add this line
native = ["esp-idf-sys/native"]
```

🔎 `.cargo/config.toml` contains local settings ([list of all settings](https://doc.rust-lang.org/cargo/reference/config.html)) for your package. 
`Cargo.toml` contains dependencies [import all your dependencies](https://doc.rust-lang.org/cargo/guide/cargo-toml-vs-cargo-lock.html).


Optional, but recommended: To save disk space and download time, set the toolchain directory to global - otherwise each new project/workspace will have its own instance of the toolchain installed on your computer.


✅ Open `hello-world/.cargo/config.toml` and add the following line to the bottom of the `[env]` section. Leave everything else unchanged.

```toml
[env]
# ... 
ESP_IDF_TOOLS_INSTALL_DIR = { value = "global" } # add this line
```

✅ Open `hello-world/rust-toolchain.toml` and change the file to look like this:

```toml
[toolchain]

channel = "nightly-2022-03-10" # change this line
```

✅ Run your project by using the following command out of the `hello_world` directory.

```shell
$ cd hello-world
$ cargo espflash --release --monitor /dev/SERIAL_DEVICE
```

✅ The last lines of your output should look like this:

```shell
(...)
I (268) cpu_start: Starting scheduler.
Hello, world!
```

## Extra tasks
- If your main function exits, you have to reset the microcontroller to start it again. What happens when you put an infinite loop at the end instead? Test your theory by flashing a looping program.
- Can you think of a way to prevent what you're now seeing? (click for hint:[^hint])

## Troubleshooting
- `⛔ Git Error: authentication required`: your git configuration is probably set to override `https` github URLs to `ssh`. Check your global `~/.git/config` for `insteadOf` sections and disable them.
- `Error: Failed to generate bindings`: add `default = ["native"]` to `Cargo.toml`
- if you're using the deprecated `pio` build system, an [initial git commit of your project](https://github.com/espressif/esp-idf/issues/3920) will be required for a successful build.
- if `cargo espflash` is stuck on `Connecting...`, you might have another monitor process still running (e.g. from the initial `hardware-check` test). Try finding and terminating it. If this doesn't help, disconnect and reconnect the board's USB cable.

[^hint]: yield control back to the underlying operating system by `sleep`ing in a loop instead of busy waiting. (use `use std::thread::sleep`)