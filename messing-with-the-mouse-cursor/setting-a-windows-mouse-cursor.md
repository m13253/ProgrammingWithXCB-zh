設置窗口的鼠標指針
===

當鼠標指針創建了，我們就能通過使用 `xcb_change_window_attributes` 和 `XCB_CWCURSOR` 屬性來修改鼠標指針：

    uint32_t mask;
    uint32_t value_list;

    /* 連接和窗口已經設置 */
    /* 鼠標指針也已經創建 */

    mask = XCB_CWCURSOR;
    value_list = cursor;
    xcb_change_window_attributes (conn, window, mask, &value_list);

當然，鼠標指針和字體都一定要釋放。
