調整窗口堆疊的命令：提升和下降
===

直到現在，我們對一個窗口改變了各種屬性。我們將看到如何去改變其他窗口的屬性。其中一個方法是**堆疊命令**。窗口被抽象成一個棧，最前面的窗口位於棧頂，最後面的窗口位於棧底。這裡有一個示例：

    const static uint32_t values[] = { XCB_STACK_MODE_ABOVE };

    /* 連接 c 和窗口 window 已經被定義了 */

    /* 移動窗口到棧頂 */
    xcb_configure_window (c, win, XCB_CONFIG_WINDOW_STACK_MODE, values);
    

    const static uint32_t values[] = { XCB_STACK_MODE_BELOW };

    /* 連接 c 和窗口 window 已經被定義了 */

    /* 移動窗口到棧底 */
    xcb_configure_window (c, win, XCB_CONFIG_WINDOW_STACK_MODE, values);
