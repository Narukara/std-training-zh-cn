# 创建新项目

现在让我们用 [`cargo-generate`](https://github.com/cargo-generate/cargo-generate) （一个通用的项目生成向导）来配置我们的第一个项目。

关于生成项目的更多信息，请参阅 The Rust on ESP Book 的[编写自己的应用](https://narukara.github.io/rust-on-esp-book-zh-cn/writing-your-own-application/index.html)章节。

> 本教程中的其他大多数练习都已经提供了项目框架，不需要使用 `cargo-generate`。
>
✅ 安装 `cargo-generate`：

```console
cargo install cargo-generate
```

✅ 进入 `intro` 目录并运行 `cargo generate`，使用 [`esp-idf` 模板](https://github.com/esp-rs/esp-idf-template)：

```console
cd intro
cargo generate esp-rs/esp-idf-template cargo
```

[`cargo-generate` 将提示有关新项目的详细信息](https://github.com/esp-rs/esp-idf-template#generate-the-project)。当在多个选项中进行选择时，可以使用光标向上/向下，并使用回车键确定。

你看到的第一条消息会是：
`⚠️Unable to load config file: /home/$USER/.cargo/cargo-generate.toml`。出现这个错误是因为没有偏好的配置文件。但这不是必须的，你可以忽略这个警告。

🔎 你可以创建一个 [偏好的配置文件](https://cargo-generate.github.io/cargo-generate/favorites.html)，放在 `$CARGO_HOME/cargo-generate`。可以使用 `-c, --config <config-file>` 覆盖它。

> 如果误操作了，按下 `Ctrl+C` 然后重新开始。

✅ 配置你的项目：

（这些选项可能以不同的顺序出现）

* Project Name: `hello-world`
* MCU: `esp32c3`
* Configure advanced template options?: `false`

🔎 `.cargo/config.toml` 包含你的 package 的本地设置（[全部设置列表](https://doc.rust-lang.org/cargo/reference/config.html)）。
`Cargo.toml` 包含依赖项，`Cargo.lock` 会[导入所有依赖项](https://doc.rust-lang.org/cargo/guide/cargo-toml-vs-cargo-lock.html)。

可选，但是推荐：为了节省硬盘空间和下载时间，把[工具链路径设置为全局（global）](https://github.com/esp-rs/esp-idf-sys#esp_idf_tools_install_dir-esp_idf_tools_install_dir)。否则每一个新项目/工作空间都会安装一个自己的工具链实例：


✅ 打开 `hello-world/.cargo/config.toml` 并添加下面几行到 `[env]` section 的底部。保持其他内容不变。

```toml
[env]
# ...
ESP_IDF_TOOLS_INSTALL_DIR = { value = "global" } # 添加这一行
```

✅ 打开 `hello-world/rust-toolchain.toml` 并将文件修改为如下所示：

```toml
[toolchain]
channel = "nightly-2025-01-01" # 修改这一行
```

✅ 在 `hello-world` 目录中用下面的命令来运行项目：

```console
cd hello-world
cargo run
```

✅ 输出的最后几行应当如下所示：

```console
(...)
I (268) cpu_start: Starting scheduler.
Hello, world!
```

## 额外的任务
- 如果 `main` 函数退出了，你只能通过复位微控制器来再次启动它。如果在其末尾放置一个死循环会怎么样？下载一个死循环程序来验证你的猜想。
- 你能想出一种办法来避免你看到的现象吗？（提示[^hint]）

## Troubleshooting
- 如果 `cargo run` 卡在了 `Connecting...` 上，可能是因为有另一个监视进程在运行（例如，在刚刚的 `hardware-check` 中打开的）。尝试找到并终止它。如果还是不行，尝试重新连接板子的 USB 线缆。
- `⛔ Git Error: authentication required`：你的 git 可能配置为将 `https` Github URL 替换成 `ssh`。检查全局 `~/.git/config` 中的 `insteadOf` 部分并禁用它们。

[^hint]: 通过在循环中`休眠`而不是忙等待，将控制权交还给底层操作系统。（使用 `std::thread::sleep`）