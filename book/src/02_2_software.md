# 软件

按照以下步骤完成 ESP32-C3 平台工具的默认安装。

🔎 如果想要自定义安装（例如，从源码构建组件，或者添加对 Xtensa 目标的支持），请参阅 *Rust on ESP* 一书的 [Rust on ESP targets](https://esp-rs.github.io/book/installation/index.html) 章节。

## Rust 工具链

✅ 如果你的电脑上还没有安装 Rust，从 <https://rustup.rs/> 获取它

此外，对于 ESP32-C3，目前需要 Rust 工具链的 [*nightly* 版本](https://rust-lang.github.io/rustup/concepts/channels.html#working-with-nightly-rust)。本课程中我们将使用 `nightly-2023-02-28` 版本。

✅ 用以下命令安装 *nightly* Rust，并添加对目标架构的支持：

```console
rustup toolchain install nightly-2023-02-28 --component rust-src
```

🔎 Rust 能够交叉编译到任何支持的目标架构（参见 `rustup 目标列表`）。默认情况下，仅会安装本机的架构。
从 2022 年 1 月起，如果要编译到 Xtensa 架构（不是本材料的一部分），需要一个 Rust 编译器的分支。

## Espressif 工具链

需要几个工具：
- [`cargo-espflash`](https://github.com/esp-rs/espflash/tree/main/cargo-espflash) - 上传固件到微控制器，打开串口监视器，cargo 集成
- [`espflash`](https://github.com/esp-rs/espflash/tree/main/espflash) - 上传固件到微控制器，打开串口监视器
- [`ldproxy`](https://github.com/esp-rs/embuild/tree/master/ldproxy) - Espressif 构建工具链的依赖

✅ 使用下面的指令安装他们：

```console
cargo install cargo-espflash espflash ldproxy
```

⚠️ 本书中列出的 `espflash` 和 `cargo-espflash` 命令假定版本 >= 2

## 工具链依赖项

### Debian/Ubuntu

```console
sudo apt install llvm-dev libclang-dev clang
```
### macOS

当使用 Homebrew 包管理器时，这也是我们推荐的方式：
```console
brew install llvm
```

### Troubleshooting

- Python 3 是必需的依赖项，它预装在 macOS 和大部分桌面 Linux 发行版上。**Python 2** 和指向它的 `virtualenv` 附加组件可能导致构建出现问题。

- 报错 `failed to run custom build command for libudev-sys vX.X.X` 或 `esp-idf-sys vX.X.X`：

    在撰写本文时，可以通过以下方法解决：
    1. 运行这一行：

    `apt-get update \
    && apt-get install -y vim nano git curl gcc ninja-build cmake libudev-dev python3 python3-pip libusb-1.0-0 libssl-dev \
    pkg-config libtinfo5`

    2. 重启终端。

    3. 如果不起作用，尝试 `cargo clean`，删除 `~/.espressif` 目录（对于 Windows，是 `%USERPROFILE%\.espressif`），然后重新构建你的项目。

    4. 在 Ubuntu 上，可能需要将内核更改为 `5.19`。运行 `uname -r` 以获取你的内核版本。


## Docker

An alternative environment, is to use Docker. The repository contains a `Dockerfile`
with instructions to install the Rust toolchain, and all required packages. **This virtualized environment is designed
to compile the binaries for the Espressif target. Flashing binaries from containers is not possible**, hence there are two options:
- Execute flashing commands, e.g., `cargo-espflash`, on the host system.
- Use [`web-flash`](https://github.com/esp-rs/esp-web-flash-server) crate to flash the resulting binaries from the container. The container already includes `web-flash`. Here is how you would flash the build output of [`hardware-check` project](./02_4_hello_board.md):
   ```console
   web-flash --chip esp32c3 target/riscv32imc-esp-espidf/debug/hardware-check
   ```

✅ 为你的操作系统安装 [`Docker`](https://docs.docker.com/get-docker/)。

要构建 Docker 镜像，请在根目录下运行以下命令：

```console
docker image build --tag esp --file .devcontainer/Dockerfile .
```

构建镜像需要一段时间，具体取决于操作系统和硬件（20-30 分钟）。

要启动新的 Docker 容器，运行：

```console
docker run --mount type=bind,source="$(pwd)",target=/workspace,consistency=cached -it esp /bin/bash
```

这将在 Docker 容器中启动一个交互式 shell。
它还将本地存储库挂载到容器内名为 `/workspace` 的文件夹中。对主机系统上项目的更改会反映在容器内，反之亦然。

使用此 Docker 配置需要把某些命令放在容器内执行，而其他命令必须在主机系统上执行。
建议保持开启两个终端，一个连接到 Docker 容器，一个在主机系统上。

* 容器内的终端：编译项目
* 主机系统的终端：使用 `cargo-espflash` 子命令来向嵌入式硬件烧写程序

## 附加软件

### VS Code

[VS Code](https://code.visualstudio.com/) 是一个具有良好 Rust 支持的编辑器，在大多数平台上可用。
使用 VS Code 时，我们推荐安装以下扩展：

* [`Rust Analyzer`](https://rust-analyzer.github.io/) 提供代码补全和跳转等
* `Even Better TOML` 用于编辑基于 TOML 的配置文件

还有一些适用于高级用法的扩展

* [`lldb`](https://github.com/vadimcn/vscode-lldb) 基于 LLDB 的本机调试器扩展
* [`crates`](https://github.com/serayuzgur/crates) 帮助管理 Rust 依赖项

### VS Code 和 Devcontainer

有助于在 Docker 容器内开发的一个 VS Code 扩展是 [`Remote Containers`](https://github.com/Microsoft/vscode-remote-release)。
它使用与 [Docker 配置](#docker)相同的 Dockerfile，构建镜像并从 VS Code 中建立连接。
安装扩展后，VS Code 会识别 `.devcontainer` 文件夹中的配置。使用 `Remote Containers - Reopen in Container` 命令将 VS Code 连接到容器。

## Gitpod

[Gitpod](https://www.gitpod.io) can provide fully initialized, perfectly set-up developer environments for this training with no installation required
on the host system, other than a [Gitpod-compatible browser](https://www.gitpod.io/docs/configure/user-settings/browser-settings).

✅ Open a Gitpod Workspace:
[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/esp-rs/espressif-trainings)

> It may take a few minutes to build the container and setup the environment.

> Note that flashing from the Gitpod workspace is only available using [`web-flash`](https://github.com/esp-rs/esp-web-flash-server). The Gitpod workspace already includes `web-flash`. Here is how you would flash the build output of [`hardware-check` project](./02_4_hello_board.md):
>   ```console
>   web-flash --chip esp32c3 target/riscv32imc-esp-espidf/debug/hardware-check
>   ```