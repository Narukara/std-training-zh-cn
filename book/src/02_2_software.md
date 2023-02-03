# 软件

按照以下步骤完成 ESP32-C3 平台工具的默认安装。

🔎 如果想要自定义安装（例如，从源码构建组件，或者添加对 Xtensa/ESP32-S3 的支持），请参阅 *Rust on ESP* 一书的 [Installing Rust](https://esp-rs.github.io/book/dependencies/installing-rust.html) 章节。

## Rust 工具链

✅ 如果你的电脑上还没有安装 Rust，从 <https://rustup.rs/> 获取它

此外，对于 ESP32-C3，目前需要特定的 Rust 工具链 *nightly* 版本。

✅ 用以下命令安装 nightly Rust，并添加对目标架构的支持：

```console
$ rustup install nightly-2022-03-10
$ rustup component add rust-src --toolchain nightly-2022-03-10
```

🔎 Rust 能够交叉编译到任何支持的目标架构（参见 `rustup 目标列表`）。默认情况下，仅会安装本机的架构。从 2022 年 1 月起，如果要编译到 Xtensa 架构（不是本材料的一部分），需要一个 Rust 编译器的分支。

## Espressif 工具链

需要几个工具：
- `cargo-espflash` - 上传固件到微控制器
- `ldproxy` - Espressif 构建工具链的依赖

✅ 使用下面的指令安装他们：

```console
$ cargo install cargo-espflash ldproxy
```

## 工具链依赖

### Debian/Ubuntu

```console
$ sudo apt install llvm-dev libclang-dev clang
```
### macOS

（当使用 Homebrew 包管理器时，这也是我们推荐的方式）

```console 
$ brew install llvm
```

### Troubleshooting

- Python 3 是必需的依赖项，它预装在 macOS 和大部分桌面 Linux 发行版上。**Python 2** 和指向它的 `virtualenv` 附加组件可能导致构建出现问题。

- 报错 `failed to run custom build command for libudev-sys v0.1.4` 或 `esp-idf-sys v0.30.X`：

    在撰写本文时，可以通过以下方法解决
    1. 在 `esp-rs` 容器中运行 [这一行](https://github.com/esp-rs/rust-build/blob/f773036483333f3b4618d988f9a1eda051573cb2/support/esp-rs-rust/Containerfile#L13)：

    `apt-get update \
    && apt-get install -y vim nano git curl gcc ninja-build cmake libudev-dev python3 python3-pip libusb-1.0-0 libssl-dev \
    pkg-config libtinfo5`

    2. 重启终端

    3. 如果不起作用，尝试 `cargo clean`，删除 `~/.espressif` 目录并按照 [esp instructions](
    https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/linux-macos-setup.html) 重新安装。

    ⚠️ 在 [step 2](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/linux-macos-setup.html#step-2-get-esp-idf) 中，不要克隆 `https://github.com/espressif/esp-idf.git` 仓库。在本培训中，我们会使用一个 git tag。

    相反，执行以下操作：

    ```console
    git clone --recursive --depth 1 --shallow-submodules git@github.com:espressif/esp-idf.git --branch "v4.4.1" esp-idf-v4.4
    cd esp-idf-v4.4
    ./install.sh esp32c3
    . ./export.sh
    ```

    如果更换了终端，你需要 source `export.sh` 文件：

    ```console
    source ~/esp/esp-idf-v4.4/export.sh 
    ```

    4. 在 Ubuntu 上，可能需要将内核更改为 `5.19`。运行 `uname -r` 以获取你的内核版本。


## Docker

> ❗️ 请**注意**，Docker 容器提供了一个**编译** Rust 练习的替代选项。它适用于有虚拟化环境经验的用户。请注意，我们无法在培训期间为 Docker 特定的问题提供帮助。

**编译** Rust 练习的另一种环境是使用 Docker。本仓库里有一个 `Dockerfile`，其中包含安装 Rust 工具链和所有需要的包的指令。这个虚拟化环境仅用于为 espressif 目标编译二进制文件。其他命令，例如 `cargo-espflash`，仍然需要在主机系统上执行。

✅ 为你的操作系统安装 [`Docker`](https://docs.docker.com/get-docker/)。

要构建 Docker 镜像，请在根目录下运行以下命令：

```console
$ docker image build --tag esp --file .devcontainer/Dockerfile .
```

构建镜像需要一段时间，具体取决于操作系统和硬件（20-30 分钟）。

要启动新的 Docker 容器，运行：

```console
$ docker run --mount type=bind,source="$(pwd)",target=/workspace,consistency=cached -it esp /bin/bash
```

这将在 Docker 容器中启动一个交互式 shell。它还将本地存储库挂载到容器内名为 `/workspace` 的文件夹中。对主机系统上项目的更改会反映在容器内，反之亦然。

使用此 Docker 配置需要把某些命令放在容器内执行，而其他命令必须在主机系统上执行。建议保持开启两个终端，一个连接到 Docker 容器，一个在主机系统上。

* 容器内的终端：编译项目
* 主机系统的终端：使用 `cargo-espflash` 子命令来向嵌入式硬件烧写程序


## 附加软件

### VS Code

一个具有良好 Rust 支持的编辑器，在大多数平台上可用。使用 VS Code 时，我们推荐安装以下扩展：

* `Even Better TOML` 用于编辑基于 TOML 的配置文件
* [`Rust Analyzer`](https://rust-analyzer.github.io/) 提供代码补全和跳转等

还有一些适用于高级用法的扩展

* [`lldb`](https://github.com/vadimcn/vscode-lldb) 基于 LLDB 的本机调试器扩展
* [`crates`](https://github.com/serayuzgur/crates) 帮助管理 Rust 依赖项

### VS Code 和 Devcontainer

有助于在 Docker 容器内开发的一个 VS Code 扩展是 [`Remote Containers`](https://github.com/Microsoft/vscode-remote-release)。它使用与 Docker 配置相同的 Dockerfile，构建镜像并从 VS Code 中建立连接。安装扩展后，VS Code 会识别 `.devcontainer` 文件夹中的配置。使用 `Remote Containers - Reopen in Container` 命令将 VS Code 连接到容器。
