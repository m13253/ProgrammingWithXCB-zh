#給一個圖形上下文分配內存
一個圖形上下文中定義的幾個屬性是用來傳遞給各種繪圖函數的。為此，我們就來定義一個圖形上下文吧。我們可以對單個窗口使用超過一個的圖形上下文，這樣能夠畫出多種風格（不同顏色，不同線寬等）。在 XCB 中，一個圖形上下文就像窗口那樣，是抽象成 Id 的：

    typedef uint32_t xcb_gcontext_t;

我們首先用這個函數請求 X 服務器，取得圖形上下文的 Id ：

    xcb_gcontext_t xcb_generate_id (xcb_connection_t *c);

然后，我们设置图形上下文的属性：

    xcb_void_cookie_t xcb_create_gc (xcb_connection_t *c,
                                     xcb_gcontext_t    cid,
                                     xcb_drawable_t    drawable,
                                     uint32_t          value_mask,
                                     const uint32_t   *value_list);

现在这里有个示例，示范如何为一个图形上下文分配内存，并用各种绘图函数将背景涂黑：

    #include <xcb/xcb.h>

    int
    main ()
    {
      xcb_connection_t *c;
      xcb_screen_t     *screen;
      xcb_drawable_t    win;
      xcb_gcontext_t    black;
      uint32_t          mask;
      uint32_t          value[1];

      /* Open the connection to the X server and get the first screen */
      c = xcb_connect (NULL, NULL);
      screen = xcb_setup_roots_iterator (xcb_get_setup (c)).data;

      /* Create a black graphic context for drawing in the foreground */
      win = screen->root;
      black = xcb_generate_id (c);
      mask = XCB_GC_FOREGROUND;
      value[0] = screen->black_pixel;
      xcb_create_gc (c, black, win, mask, value);

      return 0;
    }

####Note
應該把 `value_mask` 和 `value_list` 當作 xcb_create_gc 的原型。如果我們只想定義一個圖形上下文中的許多屬性之中的一小部分，就需要告訴 `xcb_create_gc` 什麼屬性是我們想要設置的。這就是 `value_mask` 所做的。然後我們根據 `value_mask` 來為 `value_list` 參數來設置特定的屬性值。這種情況下，我們就能用一個屬性來定義一個圖形上下文：什麼時候畫什麼。前景色默認為黑色。剩下的屬性會設置為它們的默認值。

####和 Xlib 的對比
    
    XCreateGC ()        xcb_generate_id ()
                        xcb_create_gc ()
