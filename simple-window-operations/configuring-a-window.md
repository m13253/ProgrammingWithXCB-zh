配置窗口
===

當我們創建了我們的第一個窗口時，在 X 事件 之章中，我們可以設置窗口的各種屬性（位置，大小，窗口可以接收的事件等）。如果我們想修改它們，但是窗口已經被創建了，我們也能夠改變它，只需使用這個函數：

    xcb_void_cookie_t xcb_configure_window (xcb_connection_t *c,
                                            xcb_window_t      window,
                                            uint16_t          value_mask,
                                            const uint32_t   *value_list);

可給 `value_mask` 設置一個或若干個掩碼，它們被定義在 `xproto.h` 頭文件的 `xcb_config_window_t` 枚舉中：

- XCB_CONFIG_WINDOW_X               窗口左上角新的 X 坐標
- XCB_CONFIG_WINDOW_Y               窗口左上角新的 Y 坐標
- XCB_CONFIG_WINDOW_WIDTH           窗口的新寬度
- XCB_CONFIG_WINDOW_HEIGHT          窗口的新高度
- XCB_CONFIG_WINDOW_BORDER_WIDTH    窗口的新邊框寬 
- XCB_CONFIG_WINDOW_SIBLING
- XCB_CONFIG_WINDOW_STACK_MODE      新堆疊命令

然後我們給 `value_mask` 一個新值。我們現在開始描述如何使用 `xcb_configure_window_t` 。
