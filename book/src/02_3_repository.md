# 课程仓库

完整的材料可以在 <https://github.com/ferrous-systems/espressif-trainings> 找到。

✅ 克隆并进入课程仓库：

```console
$ git clone "https://github.com/ferrous-systems/espressif-trainings.git"
$ cd espressif-trainings
```

❗ Windows 用户可能会遇到长路径名的问题。请按照以下步骤替换路径：

```console
git clone https://github.com/ferrous-systems/espressif-trainings.git
subst r:\ espressif-trainings
cd r:\
```

## 仓库内容

- `advanced/` - 高级课程的代码示例和练习
- `book/` - 本书的 markdown 源码
- `common/` - 入门和高级课程共用的代码
- `common/lib/` - 基础 crates
- `common/lib/esp32-c3-dkc02-bsc` - `ESP32-C3-DevKitC-02` 的板级支持 crate（bsc）
- `common/vendor/` - 第三方 crates，已被 fork 以添加必要的支持，等待上游合并
- `extra/` - 并非本课程必须的工具，但可能很有用
- `intro/` - 入门课程的代码示例和练习


## 关于配置的说明

比起将证书或其他敏感信息直接放在源代码中，在本课程中，我们会使用 [toml-cfg](https://github.com/jamesmunns/toml-cfg) 作为一种更方便、更安全的替代方法。配置信息会存储在相应包的根目录中名为 `cfg.toml` 的文件中

该配置中只包含一个与包同名的 section header（`Cargo.toml` 中的 `name = "your-package"`），具体设置因项目而异：

```toml
[your-package]
user = "example"
password = "h4ckm3"
```

如果你把 `cfg.toml` 复制到了另一个项目，记得将 header 改为 `[另一个包的 name]`。

