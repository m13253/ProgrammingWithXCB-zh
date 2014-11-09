創建和銷毀鼠標指針
===

有兩個方法來創建鼠標指針。一個是使用已經 X 服務器預先定義了的鼠標指針，另一個是使用用戶定義的位圖。

第一個方法，我們使用一個名為 "cursor" 的特殊字體，並使用 `xcb_create_glyph_cursor` ：

    xcb_void_cookie_t xcb_create_glyph_cursor (xcb_connection_t *c,
                                               xcb_cursor_t      cid,
                                               xcb_font_t        source_font, /* 源字體 */
                                               xcb_font_t        mask_font,   /* 掩碼，或者 XCB_NONE */
                                               uint16_t          source_char, /* 源 character glyph */
                                               uint16_t          mask_char,   /* 掩碼 character glyph */
                                               uint16_t          fore_red,    /* 源前景紅色值 */
                                               uint16_t          fore_green,  /* 源前景綠色值 */
                                               uint16_t          fore_blue,   /* 源前景藍色值 */
                                               uint16_t          back_red,    /* 源背景紅色值 */
                                               uint16_t          back_green,  /* 源背景綠色值 */
                                               uint16_t          back_blue);  /* 源背景藍色值 */

TODO: 描述 source_char 和 mask_char 並給個如何獲取值的示例。

那麼我們首先打開一個字體並創建一個新的鼠標指針。對於所有的 X 資源，都首先需要用 `xcb_generate_id` 請求 X id 。

    xcb_font_t          font;
    xcb_cursor_t        cursor;

    /* 連接已經設置 */

    font = xcb_generate_id (conn);
    xcb_open_font (conn, font, strlen ("cursor"), "cursor");

    cursor = xcb_generate_id (conn);
    xcb_create_glyph_cursor (conn, cursor, font, font,
                             58, 58 + 1,
                             0, 0, 0,
                             0, 0, 0);

我們通過指定 `source_font` 參數為 58 ，並設置 `mask_font` 為 58 + 1 ，創建了一個樣式為右手的鼠標指針

鼠標指針通過這個函數來銷毀：

    xcb_void_cookie_t xcb_free_cursor (xcb_connection_t *c,
                                       xcb_cursor_t      cursor);

第二種方法，我們創建一對 XPM 來創建鼠標指針。使用同樣的深度，不同的顏色。一個 XPM 定義了鼠標指針樣式，並透過掩碼指定特定像素精確地描繪在鼠標指針上。然後那些像素將會透明。

TODO: 給個栗子。
