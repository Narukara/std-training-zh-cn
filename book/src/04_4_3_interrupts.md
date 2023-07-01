# 分步解答

1. 初始化 LED 外设并以任意颜色值启动它，看看它是否正常工作。
   ```rust
    let mut led = WS2812RMT::new(peripherals.pins.gpio2, peripherals.rmt.channel0)?;

    led.set_pixel(RGB8::new(20, 0, 20)).unwrap(); // Remove this line after you tried it once
   ```
2. 只在按钮按下后点亮 LED。可以把打印语句改成下面的代码来实现：
   ```rust
   1 => {
        led.set_pixel(arbitrary_color)?;

        },
    _ => {},
   ```
3. 调用 `esp_random()` 来生成随机 RGB 颜色值。
   * 这个函数是 `unsafe` 的。
   * 它会生成 `u32`，因此需要将它转换成 `u8`。

    ```rust
    unsafe {
    //...
    1 => {
        let r = esp_random() as u8;
        let g = esp_random() as u8;
        let b = esp_random() as u8;

        let color = RGB8::new(r, g, b);
        led.set_pixel(color)?;

        },
    _ => {},
   ```

4. **可选**：如果你想在其他地方重用这些代码，可以考虑将其放入一个函数中。这也允许我们确认具体哪些代码需要用 `unsafe` 块包裹。

    ```rust
    // ...

        unsafe {
            // ...
            match res {
                    1 => {
                        // 生成随机 RGB 值
                        random_light(&mut led);

                    },
                    _ => {},
                };
            }
        }
    // ...
    fn random_light(led: &mut WS2812RMT) {
        let mut color = RGB8::new(0, 0, 0);
        unsafe {
            let r = esp_random() as u8;
            let g = esp_random() as u8;
            let b = esp_random() as u8;

            color = RGB8::new(r, g, b);
        }

        led.set_pixel(color).unwrap();
    }
    ```

