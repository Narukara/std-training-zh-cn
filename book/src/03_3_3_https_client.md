# HTTPS 客户端

现在让我们更改 http 客户端源代码，使它也适用于加密连接。

`intro/http-client/examples/http_client.rs` 包含解答。你可以用下面的命令运行它（必须完成练习的第一步才能通过编译）：

```
cargo espflash --release --example https_client --monitor $SERIALDEVICE
```

为了建立安全、加密的 HTTPS 连接，我们首先需要添加一些证书，以便验证服务器的身份。

✅ 为了启用基本的 TLS 证书支持，在项目的 `sdkconfig.defaults` 里删除已存在的 `CONFIG_MBEDTLS...` 行并添加：

```cfg
CONFIG_MBEDTLS_CERTIFICATE_BUNDLE=y
CONFIG_MBEDTLS_CERTIFICATE_BUNDLE_DEFAULT_CMN=y
```

现在，让我们创建一个自定义客户端配置。在 `http::client::EspHttpClientConfiguration` 里启用证书，其他字段保持默认值：

```rust
let mut client = EspHttpClient::new(&EspHttpClientConfiguration {
        use_global_ca_store: true,
        crt_bundle_attach: Some(esp_idf_sys::esp_crt_bundle_attach),

        ..Default::default()
    }
```

✅ 用新的配置初始化 HTTP 客户端，通过下载一些 `https` 资源来验证 HTTPS 是否正常工作，例如 `https://espressif.com/`。下载的内容会在控制台中以原始 html 的形式显示出来。

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