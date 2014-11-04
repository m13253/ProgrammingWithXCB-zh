在屏幕中移動窗口
===

我們也許想移動窗口到不同的位置。這件事可以像這樣做：

    const static uint32_t values[] = { 10, 20 };

    /* 連接 c 和窗口 win 都已經被定義了 */

    /* 移動窗口到 x = 10, y = 20 */
    xcb_configure_window (c, win, XCB_CONFIG_WINDOW_X, XCB_CONFIG_WINDOW_Y, values);

####Note
當窗口被移動時，可能在被其他窗口遮蓋時會接收到暴露事件。
