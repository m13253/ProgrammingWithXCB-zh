調整窗口大小
===

我們同樣能調整窗口大小。可以使用以下的代碼完成：

    const static uint32_t values[] = { 200, 300 };

    /* 連接 c 和窗口 win 都已經被定義了 */
    
    /* 調整窗口大小為 width = 200, height = 300 */
    xcb_configure_window (c, win, XCB_CONFIG_WINDOW_WIDTH | XCB_CONFIG_WINDOW_HEIGHT, values);

我們也能在調整窗口大小的同時移動窗口位置：

    const static uint32_t values[] = { 10, 20, 200, 300 };

    /* 連接 c 和窗口 win 都已經被定義了 */
    /* 移動窗口到 x = 10, y = 20 */
    /* 調整窗口大小為 width = 200, height = 300 */
    xcb_configure_window (c, win, XCB_CONFIG_WINDOW_X | XCB_CONFIG_WINDOW_Y | XCB_CONFIG_WINDOW_WIDTH | XCB_CONFIG_WINDOW_HEIGHT, values);
