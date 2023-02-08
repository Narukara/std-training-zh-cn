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

（这些选项可能以不同的顺序出现）

* Project Name: `hello-world`
* Rust toolchain: `nightly`
* MCU: `esp32c3`
* ESP-IDF native build version: `4.4`
* STD support: `true`

我们将使用 Espressif 构建系统的 `native` 变体进行构建。

✅ 在新创建的 `hello-world` 项目中打开 `Cargo.toml`，添加 `"native"` 作为默认 feature 来启用 native 构建系统：

```toml
[features]
default = ["native"] # 添加这一行
native = ["esp-idf-sys/native"]
```

🔎 `.cargo/config.toml` 包含你的 package 的本地设置（[全部设置列表](https://doc.rust-lang.org/cargo/reference/config.html)）。
`Cargo.toml` 包含依赖项 [导入所有依赖项](https://doc.rust-lang.org/cargo/guide/cargo-toml-vs-cargo-lock.html)。

可选，但是推荐：为了节省硬盘空间和下载时间，把工具链路径设置为全局（global）——否则每一个新项目/工作空间（workspace）都会安装一个自己的工具链实例。


✅ 打开 `hello-world/.cargo/config.toml` 并添加下面几行到 `[env]` section 的底部。保持其他内容不变。

```toml
[env]
# ... 
ESP_IDF_TOOLS_INSTALL_DIR = { value = "global" } # 添加这一行
```

✅ 打开 `hello-world/rust-toolchain.toml` 并将文件修改为如下所示：

```toml
[toolchain]

channel = "nightly-2022-03-10" # 修改这一行
```

✅ 在 `hello_world` 目录中用下面的命令来运行项目：

```shell
$ cd hello-world
$ cargo espflash --release --monitor /dev/SERIAL_DEVICE
```

✅ 输出的最后几行应当如下所示：

```shell
(...)
I (268) cpu_start: Starting scheduler.
Hello, world!
```

## 额外的任务
- 如果 `main` 函数退出了，你只能通过复位微控制器来再次启动它。如果在其末尾放置一个死循环会怎么样？下载一个死循环程序来验证你的猜想。
- 你能想出一种办法来避免你看到的现象吗？（提示[^hint]）

## Troubleshooting
- `⛔ Git Error: authentication required`：你的 git 可能被配置为将 `https` github URL 替换成 `ssh`。检查全局 `~/.git/config` 中的 `insteadOf` 部分并禁用它们。
- `Error: Failed to generate bindings`：添加 `default = ["native"]` 到 `Cargo.toml`
- 如果要使用 `pio` 构建系统（已弃用），为了成功构建，需要 [创建一个初始 git 提交](https://github.com/espressif/esp-idf/issues/3920)。
- 如果 `cargo espflash` 卡在了 `Connecting...`，可能是有另一个 monitor 进程仍在运行（例如，`hardware-check` 的那个）。尝试找到并终止它。如果不起作用，断开并重新连接 USB 线缆。

[^hint]: 通过在循环中`休眠`而不是忙等待，将控制权交还给底层操作系统。（使用 `std::thread::sleep`）