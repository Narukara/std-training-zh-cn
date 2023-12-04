# HTTPS 客户端

现在让我们更改 HTTP 客户端源代码，使它也适用于加密连接。

`intro/http-client/examples/http_client.rs` 包含解答。你可以用下面的命令运行它：

```console
cargo run --example https_client
```

创建一个自定义的客户端配置，由此使用启用了证书的`esp_idf_svc::http::client::EspHttpConnection`，而其他值保持默认。

```rust
{{#include ../../intro/http-client/examples/https_client.rs:connection}}
```

✅ 用新的配置初始化 HTTP 客户端，通过下载一些 `https` 资源来验证 HTTPS 是否正常工作，例如 `https://espressif.com/`。下载的内容会在控制台中以原始 HTML 的形式显示出来。

## Troubleshooting（与上一节相同）

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