# HTTP 客户端

本练习的目标是编写一个能够连接网站的小型 HTTP 客户端。

## 配置

✅ 进入 `intro/http-client` 目录。

✅ 打开 `intro/http-client` 中已准备好的项目框架。

✅ 将你的[网络凭据](02_4_hello_board.md)加到 `cfg.toml` 中，就像在硬件测试中做的那样。

✅ 用下面的命令打开此项目的文档：

```console
cargo doc --open
```

`intro/http-client/examples/http_client.rs` 包含解答。你可以用下面的命令运行它：

```console
cargo run --example http_client
```
## 建立连接

默认只能使用未加密的 HTTP，这限制了我们能连接到的主机。因此我们将使用 `http://neverssl.com/`。

在 ESP-IDF 中，HTTP 客户端连接由 `esp-idf-svc` crate 中的 `http::client::EspHttpClient` 管理。它实现了 `embedded-svc` 中的 `http::client::Client` trait，定义了 [HTTP 请求方法](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods)（如 `GET` 或 `POST`）使用的函数。现在正是查看你用 `cargo doc --open` 打开的文档的好时机，查看其中 `esp_idf_svc::http::client::EspHttpConnection` 和 `embedded_svc::http::client::Client` 相关的内容，以及可以使用的实例化方法。

✅ 用默认配置创建一个 `EspHttpConnection`。到文档里找一个合适的构造方法。

✅ 从刚刚创建的 connection 里获取一个 client。

在 client 上调用 HTTP 函数（例如 `get(url)`）会返回一个 `embedded_svc::http::client::Request`。你需要提交（submit）它来表示 client 在发送请求附带的选项。

`get` 函数使用 [as_ref()](https://doc.rust-lang.org/std/convert/trait.AsRef.html)。这意味着该函数可以接受任何实现 `AsRef<str>` trait 的类型，即任何可以调用 `.as_ref()` 产生 `&str` 的类型，而不是仅限于某种特定类型，例如 `String` 或 `&str`。这适用于 `String` 和 `&str`，也适用于包含前两种类型的 `Cow<str>` 枚举类型。


```rust
{{#include ../../intro/http-client/examples/http_client.rs:request}}
```

成功的响应具有 [2xx 范围内的状态码](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)。紧随其后的是网站的原始 html。

✅ 检验连接是否成功。

✅ 如果状态码不在 2xx 范围内，返回一个错误。

```rust
match status {
        200..=299 => {
        }
        _ => bail!("Unexpected response code: {}", status),
    }
```
状态码错误可以用 [Anyhow](https://docs.rs/anyhow/latest/anyhow/index.html) crate 返回。`Anyhow` 常被用于简化应用程序中的错误处理，它提供了一个通用的 `anyhow::Result<T>`，将成功（`Ok`）情况包装在 T 中，而且无需指定 Err 类型，只要求你返回的每个错误都实现了 `std::error::Error`。


✅ 使用 `Read::read(&mut reader,&mut buf)` 将接收到的数据逐块地读取到 `u8` 缓冲区中。`Read::read` 会返回读取的字节数——当这个值为 `0` 时就完成了读取。

✅ 报告读取的总字节数。

✅ 把接收到的数据记录到控制台上。
💡 响应数据以字节的形式存储在缓冲区内，所以你可能需要[一个方法](https://doc.rust-lang.org/std/str/fn.from_utf8.html)来把字节转换为 `&str`。

## 额外的任务

✅ 在 match 分支里分别处理 3xx、4xx 和 5xx 状态码

✅ 编写一个自定义的 `Error` 枚举来表示这些错误。为这个错误实现 `std::error::Error` trait。


## Simulation

This project is available for simulation through two methods:
- Wokwi projects:
  - [Exercise](https://wokwi.com/projects/360722140931768321?build-cache=disable)
  - [Solution](https://wokwi.com/projects/333372159510446675?build-cache=disable)
- Wokwi files are also present in the project folder to simulate it with Wokwi VS Code extension:
   1. Press F1, select `Wokwi: Select Config File` and choose `intro/http-client/wokwi.toml`
      - Edit the `wokwi.toml` file to select between exercise and solution simulation
   2. Build you project
   3. Press F1 again and select `Wokwi: Start Simulator`

## Troubleshooting

- `missing WiFi name/password`：确保你已根据 `cfg.toml.example` 配置了 `cfg.toml`。一个常见的问题是包名和配置中的 section 名称不匹配。

```toml
# Cargo.toml
#...
[package]
name = "http-client"
#...

# cfg.toml
[http-client]
wifi_ssid = "..."
wifi_psk = "..."
```

- `Guru Meditation Error: Core 0 panic'ed (Load access fault). Exception was unhandled.`
    这可能是由你的代码中的 `.unwrap()` 引起的。试试用问号运算符替代它们。