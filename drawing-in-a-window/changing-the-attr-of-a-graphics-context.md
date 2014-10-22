#改變圖形上下文的屬性

當我們分配好了一個圖形上下文，我們可能需要改變它的屬性（例如改變繪畫線條的前景色，或者改變描繪字符串的字體）。這個函數可以助你一臂之力：

    xcb_void_cookie_t xcb_change_gc (xcb_connection_t *c, 
                                     xcb_gcontext_t    gc,
                                     uint32_t          value_mask,
                                     const uint32_t   *value_list);

`value_mask` 參數可以設置 `xcb_gc_t` 中定義的掩碼：

- XCB_GC_FUNCTION
- XCB_GC_PLANE_MASK
- XCB_GC_FOREGROUND
- XCB_GC_LINE_WIDTH
- XCB_GC_LINE_STYLE
- XCB_GC_CAP_STYLE
- XCB_GC_JOIN_STYLE
- XCB_GC_FILL_STYLE
- XCB_GC_FILL_RULE
- XCB_GC_TILE
- XCB_GC_STIPPLE
- XCB_GC_TILE_STIPPLE_ORIGIN_X
- XCB_GC_TILE_STIPPLE_ORIGIN_Y
- XCB_GC_FONT
- XCB_GC_SUBWINDOW_MODE
- XCB_GC_GRAPHICS_EXPOSURES
- XCB_GC_CLIP_ORIGIN_X
- XCB_GC_CLIP_ORIGIN_Y
- XCB_GC_CLIP_MASK
- XCB_GC_DASH_OFFSET
- XCB_GC_DASH_LIST
- XCB_GC_ARC_MODE

