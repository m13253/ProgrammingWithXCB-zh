Assigning 一個字體到圖像上下文
===

當一個字體被打開了，你就需要創建一個圖像上下文來包含要描繪的文字的前景色和背景色的信息。這裡有一個示例：

    /*
     * c        連接
     * screen   窗口要顯示的屏幕
     * window   要描繪文字的窗口
     * font     要打開的字體
     */
    
    uint32_t            value_list[3];
    xcb_gcontext_t      gc;
    uint32_t            mask;

    gc = xcb_generate_id (c);
    mask = XCB_GC_FOREGROUND | XCB_GC_BACKGROUND | XCB_GC_FONT;
    value_list[0] = screen->black_pixel;
    value_list[1] = screen->white_pixel;
    value_list[2] = font;
    xcb_create_gc (c, gc, window, mask, value_list);

    /* 這個字體不再需要了，所以我們關閉它 */
    xcb_close_font (c, font);
