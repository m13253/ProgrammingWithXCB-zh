釋放 XPM
===

最後，當我們使用完畢 XPM 後，我們應該釋放它，為了在 X 服務器上釋放資源。用這個函數完成：

    xcb_void_cookie_t xcb_free_pixmap (xcb_connection_t *c,
                                       xcb_pixmap_t      pixmap);

當然，我們釋放 XPM 之後，就不要作死去再次訪問它啦。

TODO: 給個實例，或者鏈接到 xpoints.c
