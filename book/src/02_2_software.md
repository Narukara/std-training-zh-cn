# 软件

按照以下步骤完成 ESP32-C3 平台工具的默认安装。

🔎 如果想要自定义安装（例如，从源码构建组件，或者添加对 `Xtensa` 目标的支持），请参阅 *Rust on ESP* 一书的 [Rust on ESP targets](https://esp-rs.github.io/book/installation/index.html) 章节。

## Rust 工具链

✅ 如果你的电脑上还没有安装 Rust，从 <https://rustup.rs/> 获取它

此外，对于 ESP32-C3，目前需要 Rust 工具链的 [*nightly* 版本](https://rust-lang.github.io/rustup/concepts/channels.html#working-with-nightly-rust)。本教程中我们将使用 `nightly-2023-11-14` 版本。

✅ 用以下命令安装 *nightly* Rust，并添加对目标架构的支持：

```console
rustup toolchain install nightly-2023-11-14 --component rust-src
```

🔎 Rust 能够交叉编译到任何支持的目标架构（参见 `rustup 目标列表`）。默认情况下，仅会安装本机的架构。
从 2022 年 1 月起，如果要编译到 `Xtensa` 架构（不是本材料的一部分），需要一个 Rust 编译器的分支。

## Espressif 工具链

需要几个工具：
- [`cargo-espflash`](https://github.com/esp-rs/espflash/tree/main/cargo-espflash) - 上传固件到微控制器，打开串口监视器，Cargo 集成
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
sudo apt install llvm-dev libclang-dev clang libuv-dev libuv1-dev pkgconf python3-venv python-is-python3
```
### `macOS`

当使用 Homebrew 包管理器时，这也是我们推荐的方式：
```console
brew install llvm libuv
```

### Troubleshooting

- Python 3 是必需的依赖项，它预装在 macOS 和大部分桌面 Linux 发行版上。**Python 2** 和指向它的 `virtualenv` 附加组件可能导致构建出现问题。

- 报错 `failed to run custom build command for libudev-sys vX.X.X` 或 `esp-idf-sys vX.X.X`：

    在撰写本文时，可以通过以下方法解决：
    1. 运行这一行命令：

    `apt-get update \
    && apt-get install -y vim nano git curl gcc ninja-build cmake libudev-dev python3 python3-pip libusb-1.0-0 libssl-dev \
    pkg-config libtinfo5`

    2. 重启终端。

    3. 如果不起作用，尝试 `cargo clean`，删除 `~/.espressif` 目录（对于 Windows，是 `%USERPROFILE%\.espressif`），然后重新构建你的项目。

    4. 在 Ubuntu 上，可能需要将内核更改为 `5.19`。运行 `uname -r` 以获取你的内核版本。


## Docker

另一种可选的环境是使用 Docker。本仓库包含一个 `Dockerfile`，
其中包含用于安装 Rust 工具链（和所有依赖的包）的指令。**此虚拟化环境旨在
为 Espressif 目标编译二进制文件，在容器内烧录二进制文件是不可行的**。因此，有两种选择：
- 在主机系统上执行烧写命令，例如 `cargo-espflash`。 如果采用这个选项，建议开启两个终端：
    - 一个在容器内，用于编译项目
    - 一个在主机上，用 `cargo-espflash` 子命令来烧写程序
- 在容器内使用 [`web-flash`](https://github.com/esp-rs/esp-web-flash-server) crate 来烧写程序。容器已经包含了 `web-flash`。烧写 [`hardware-check` 项目](./02_4_hello_board.md) 的命令是：
   ```console
   web-flash --chip esp32c3 target/riscv32imc-esp-espidf/debug/hardware-check
   ```

✅ 为你的操作系统安装 [`Docker`](https://docs.docker.com/get-docker/)。

✅ 获取 Docker 镜像： 有两种方法来获取 Docker 镜像：
- 用 `Dockerfile` 构建镜像：
    ```console
    docker image build --tag rust-std-training --file .devcontainer/Dockerfile .
    ```
    构建镜像需要一段时间，具体取决于操作系统和硬件（20-30 分钟）。
- 从 [Dockerhub](https://hub.docker.com/r/espressif/rust-std-training) 下载：
    ```console
    docker pull espressif/rust-std-training
    ```
✅ 启动新的 Docker 容器：

- 对于本地 Docker 镜像：
  ```console
  docker run --mount type=bind,source="$(pwd)",target=/workspace,consistency=cached -it rust-std-training /bin/bash
  ```
- 对于从 Docker Hub 下载的：
  ```console
  docker run --mount type=bind,source="$(pwd)",target=/workspace,consistency=cached -it espressif/rust-std-training:latest /bin/bash
  ```

这将在 Docker 容器中启动一个交互式 shell。
它还将本地存储库挂载到容器内名为 `/workspace` 的文件夹中。对主机系统上项目的更改会反映在容器内，反之亦然。

## 附加软件

### VS Code

[VS Code](https://code.visualstudio.com/) 是一个具有良好 Rust 支持的编辑器，在大多数平台上可用。
使用 VS Code 时，我们推荐安装以下扩展：

* [`Rust Analyzer`](https://rust-analyzer.github.io/) 提供代码补全和跳转等
* `Even Better TOML` 用于编辑基于 TOML 的配置文件

还有一些适用于高级用法的扩展

* [`lldb`](https://github.com/vadimcn/vscode-lldb) 基于 LLDB 的本机调试器扩展
* [`crates`](https://github.com/serayuzgur/crates) 帮助管理 Rust 依赖项

### VS Code 和 Dev container

有助于在 Docker 容器内开发的一个 VS Code 扩展是 [`Remote Containers`](https://github.com/Microsoft/vscode-remote-release)。
它使用与 [Docker 配置](#docker)相同的 Dockerfile，构建镜像并从 VS Code 中建立连接。
安装扩展后，VS Code 会识别 `.devcontainer` 文件夹中的配置。使用 `Remote Containers - Reopen in Container` 命令将 VS Code 连接到容器。
