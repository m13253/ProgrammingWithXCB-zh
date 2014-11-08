分配和釋放顏色記錄
===

當我們可訪問一些顏色映射時，我們就能分配顏色了。顏色信息被存儲在這樣的結構中：

    typedef struct {
        uint8_t  response_type;
        uint8_t  pad0;
        uint16_t sequence;
        uint32_t length;
        uint16_t red;           /* 紅色值 */
        uint16_t green;         /* 綠色值 */
        uint16_t blue;          /* 藍色值 */
        uint8_t pad1[2];
        uint32_t pixel;         /* 顏色映射記錄 */
    } xcb_alloc_color_reply_t;

XCB 使用這兩個函數填寫這個結構：

    xcb_alloc_color_cookie_t xcb_alloc_color       (xcb_connection_t        *c,
                                                    xcb_colormap_t           cmap,
                                                    uint16_t                 red,
                                                    uint16_t                 green,
                                                    uint16_t                 blue);
    xcb_alloc_color_reply_t *xcb_alloc_color_reply (xcb_connection_t        *c,
                                                    xcb_alloc_color_cookie_t cookie,
                                                    xcb_generic_error_t    **e);

`xcb_alloc_color()` 需要傳遞 RGB 的值，這裡有個示例：

    #include <malloc.h>

    #include <xcb/xcb.h>

    int
    main ()
    {
      xcb_connection_t        *c;
      xcb_screen_t            *screen;
      xcb_window_t             win;
      xcb_colormap_t           cmap;
      xcb_alloc_color_reply_t *rep;

      c = xcb_connect (NULL, NULL);
      screen = xcb_setup_roots_iterator (xcb_get_setup (c)).data;

      /* 在此創建窗口 */

      cmap = xcb_generate_id (c);
      xcb_create_colormap (c, XCB_COLORMAP_ALLOC_NONE, cmap, win, screen->root_visual);

      rep = xcb_alloc_color_reply (c, xcb_alloc_color (c, cmap, 65535, 0, 0), NULL);

      if (!rep)
        return 0;

      free (rep);

      return 0;
    }

當 `xcb_alloc_color_reply()` ，你就需要釋放它。

TODO: 討論如何釋放顏色。
