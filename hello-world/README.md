#創建一個窗口——泥嚎，世界
在我們取得了屏幕的基本信息後，我們就能創建我們的第一個窗口。在 X 窗口系統中，一個窗口被當作是 Id。所以，在 XCB 中，一個窗口是這樣的類型：

    typedef uint32_t xcb_window_t;

我們首先要用這個函數為我們的窗口請求一個新的 Id ：

    xcb_window_t xcb_generate_id(xcb_connection_t *c);

然後， XCB 提供了以下函數來創建新窗口：

    xcb_void_cookie_t xcb_create_window (xcb_connection *c,             /* 指向連接的指針 */
                                         uint8_t         depth,         /* 屏幕的深度 */
                                         xcb_window_t    wid,           /* 窗口的 Id */
                                         xcb_window_t    parent,        /* 屏幕上已存在的要作為父窗口的 Id */
                                         int16_t         x,             /* 左上角的 X 坐標 */
                                         int16_t         y,             /* 左上角的 Y 坐標 */
                                         uint16_t        width,         /* 窗口的寬度，單位為像素 */
                                         uint16_t        height,        /* 窗口的高度，單位為像素 */
                                         uint16_t        border_width,  /* 窗口邊框寬 */
                                         uint16_t        _class,
                                         xcb_visualid_t  visual,
                                         uint32_t        value_mask,
                                         const uint32_t *value_list);

事實上，我們創建了窗口並不意味著窗口會馬上在屏幕上描繪。默認地，新創建的窗口是不會被映射到屏幕上的（它們不可見）。為了讓窗口可見，我們會使用 `xcb_map_window()` ，它的原型就是：

    xcb_void_cookie_t xcb_map_window (xcb_connection_t *c,
                                      xcb_window_t      window);

