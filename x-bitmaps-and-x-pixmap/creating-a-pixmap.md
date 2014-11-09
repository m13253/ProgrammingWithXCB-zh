創建 XPM
===

有時候我們想創建一個未初始化的 XPM ，以稍後在上面繪圖。這對圖像繪圖程序是很有用的（創建一個新的空畫布將會創建一個新的 XPM ，儲存在上面畫的東西）。這對於讀取各種圖像格式也是很有用的：我們讀取圖像數據到內存，在服務器創建一個 XPM ，然後解碼圖像數據畫到 XPM 上。

要創建一個新的 XPM ，我們首先請求 X 服務器給我們的 XPM 一個 Id 。使用這個函數：

    xcb_pixmap_t xcb_generate_id (xcb_connection *c);

然後，XCB 提供了以下函數來創建新的 XPM ：

    xcb_void_cookie_t xcb_create_pixmap (xcb_connection *c,
                                         uint8_t         depth,
                                         xcb_pixmap_t    pid,       /* XPM 的 Id */
                                         xcb_drawable_t  drawable,
                                         uint16_t        width,     /* 窗口的寬，以像素為單位 */
                                         uint16_t        height);   /* 窗口的高，以像素為單位 */

TODO: 解釋 drawable 參數。
