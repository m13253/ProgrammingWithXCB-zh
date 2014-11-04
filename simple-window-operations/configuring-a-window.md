配置窗口
===

當我們創建了我們的第一個窗口時，在 X 事件 之章中，我們可以設置窗口的各種屬性（位置，大小，窗口可以接收的事件等）。如果我們想修改它們，但是窗口已經被創建了，我們也能夠改變它，只需使用這個函數：

    xcb_void_cookie_t xcb_configure_window (xcb_connection_t *c,
                                            xcb_window_t      window,
                                            uint16_t          value_mask,
                                            const uint32_t   *value_list);
