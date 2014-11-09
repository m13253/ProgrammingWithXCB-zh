對窗口描繪 XPM
===

當我們可以操縱一個 XPM 時，我們就能在一些窗口上面繪圖了。使用以下函數：

    xcb_void_cookie_t xcb_copy_area (xcb_connection_t *c,
                                     xcb_drawable_t    src_drawable,    /* 要粘帖的 drawable */
                                     xcb_drawable_t    dst_drawable,    /* 複製的上一個 drawable */
                                     xcb_gcontext_t    gc,              /* 圖像上下文 */
                                     int16_t           src_x,           /* 想複製的左上角 X 坐標 */
                                     int16_t           src_y,           /* 想複製的左上角 Y 坐標 */
                                     int16_t           dst_x,           /* 要複製到的 X 坐標 */
                                     int16_t           dst_y,           /* 要複製到的 Y 坐標 */
                                     uint16_t          width,           /* 要複製的區域的寬度 */
                                     uint16_t          height);         /* 要複製的區域的高度 */

你看，我們能複製 XPM，並且只能給出一個 XPM 可繪圖的矩形。這樣可以提高繪圖速度：我們只需複製我們修改的那部分 XPM 。

####NOTE
可以在屏幕的不同深度創建 XPM 。當我們進行複製操作時（一個窗口上的 XPM 等），我們應該調整雙方的深度。如果雙方深度不同，操作會失敗。除了使用 `xcb_copy_plane_t` 函數來複製一部分源 XPM 的平面之外。在通常的事件中，我們能複製一些平面到目標窗口（事實上，設置一些特殊的位可以複製）。這能用來製造各種圖形特效，不過這超出了這個教程的範圍。
