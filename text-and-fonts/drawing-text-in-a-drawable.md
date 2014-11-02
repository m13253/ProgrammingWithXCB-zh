在 drawable 描繪字體
===

在 drawable 中描繪字體，我們使用以下函數：

    xcb_void_cookie_t xcb_image_text_8 (xcb_connection *c,
                                        uint8_t         string_len,
                                        xcb_drawable_t  drawable,
                                        xcb_gcontext_t  gc,
                                        int16_t         x,
                                        int16_t         y,
                                        const char     *string);

`string` 參數就是要用來描繪的文字。繪圖的坐標由參數 `x` 和 `y` 給出。字體的基線是參數 `y` 。
