# 简单的 HTTP 服务器

现在让我们把开发板变成一个微型网络服务器，在收到 `GET` 请求后，它会提供来自内部温度传感器的数据。

## 配置

`intro/http-server/` 中有已准备好的项目框架。它会建立 WiFi 连接，但你需要将其配置为使用 `cfg.toml` 中的网络凭据。

`intro/http-server/examples/https-server.rs` 包含一个解答。你可以用下面的命令运行它：

```console
cargo run --example http_server
```

## 处理请求

为了用浏览器访问开发板，你需要知道板子的 IP 地址。


✅ 运行 `intro/http-server` 中的框架代码。输出应该包含板子的 IP 地址，类似这样：

```console
I (3862) esp_netif_handlers: sta ip: 192.168.178.54, mask: ...
...
Server awaiting connection
```

`sta ip` 指的是 WiFi 术语站点（station），代表连接到接入点（access point）的接口。这就是你需要输入浏览器的地址（或其他 HTTP 客户端，如 `curl`）。

> 🔎 ESP-IDF 会尝试在本地网络中注册主机名 `espressif`，因此通常使用 `http://espressif/` 代替 `http://<sta ip>/` 也可以。
>
> 你可以通过设置 `sdkconfig.defaults` 中的 `CONFIG_LWIP_LOCAL_HOSTNAME` 来更改主机名，例如 `CONFIG_LWIP_LOCAL_HOSTNAME="esp32c3"`。

向客户端发送 HTTP 数据包括：
- 创建一个 `EspHttpServer` 实例
- 在主函数中循环，这样它就不会终止——终止会导致服务器离开作用域然后关闭
- 为需要提供内容的每个路径设置单独的请求处理（`handler`）函数。任何未配置的路径都会导致 `404` 错误。这些处理函数以 Rust 闭包的形式内联实现，如下所示：

```rust
server.fn_handler(path, Method::Get, |request| {
    // ...
    // 构造一个响应
    let mut response = request.into_ok_response()?;
    // 写入期望的数据
    response.write_all(&some_buf)?;
    // 如果完成了处理，处理函数期望一个 `Completion` 作为结果
    // 这是通过它实现的：
    Ok(())
});

```


✅ 使用默认的 `esp_idf_svc::http::server::Configuration` 创建一个 `EspHttpServer` 实例。默认配置将使它自动监听 80 端口。

✅ 验证与 `http://<sta ip>/` 的连接是否会产生 `404`（not found）错误，表明 `This URI does not exist`。

✅ 为根路径（`"/"`）编写请求处理函数。处理函数会在 `http://<sta ip>/` 上发送问候消息，使用已提供的 `index_html()` 函数来生成 HTML 字符串。

## 动态数据

我们还可以向客户端发送动态信息。该框架包含一个已配置好的 `temp_sensor`，用于测量开发板的内部温度。

✅ 在 `http://<sta ip>/temperature` 上编写第二个请求处理函数，用于报告芯片的温度。使用已提供的 `temperature(val: f32)` 函数来生成 HTML 字符串。
💡 如果要发送响应字符串，需要通过 `a_string.as_bytes()` 将其转换为 `&[u8]` slice。
💡 温度传感器需要独占（可变）访问。将它作为有所有权的值传递给请求处理函数是行不通的（因为它会在第一次调用后被丢弃）——你可以通过把处理函数变成 `move ||` 闭包来解决这个问题。将传感器包裹在 `Arc<Mutex<_>>` 中，将此 `Arc` 的一个 `clone()` 保留在主函数中，并将另一个移动到闭包中。

## Troubleshooting

- `httpd_txrx: httpd_resp_send_err` 可以通过重启解决。如果不起作用，可以使用 `cargo clean`。
- 确保你的电脑和开发板使用的是相同的 Wifi 网络。