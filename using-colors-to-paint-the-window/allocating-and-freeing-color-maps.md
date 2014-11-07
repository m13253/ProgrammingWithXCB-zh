分配和釋放顏色映射
===

當你使用 XCB 繪圖時，你可以在你的窗口中選擇使用屏幕基礎的顏色映射來顯示或者分配一個新的顏色映射給你的窗口。在 latter 情況下，鼠標每次移動到你的窗口之內，屏幕顏色映射將會被你的窗口的顏色映射重寫，然後你將看到其他的窗口都會變色，一些還凍結了。事實上，這只會在你執行帶 `-install` 命令執行 X 應用時發生。

在 XCB 中，一個顏色映射是（通常和 X 中一樣）一個 Id ：

    typedef uint32_t xcb_colormap_t;

為了訪問屏幕的默認顏色映射，你需要設置 `default_colormap` 為 `xcb_screen_t` 結構：

    #include <stdio.h>

    #include <xcb/xcb.h>

    int
    main()
    {
      xcb_connection_t *c;
      xcb_screen_t     *screen;
      xcb_colormap_t    colormap;

      c = xcb_connect (NULL, NULL);
      screen = xcb_setup_roots_iterator (xcb_get_setup (c)).data;

      colormap = screen->default_colormap;

      return 0;
    }

這將返回第一個屏幕的默認顏色映射（重複，記住 X 服務器可能支持各種不同的屏幕）。

要分配一個新的顏色映射，就像下面那樣做。我們首先用這個函數請求 X 服務器給我們的顏色映射來個 Id ：

    xcb_colormap_t xcb_generate_id (xcb_connection_t *c);

然後，我們使用這個函數創建顏色映射：

    xcb_void_cookie_t xcb_create_colormap (xcb_connection_t *c,
                                           uint8_t           alloc,   /* 要分配的顏色映射記錄 （不分配或者全部分配）*/
                                           xcb_colormap_t    mid,     /* 顏色映射 Id */
                                           xcb_window_t      window,  /* 要被創建顏色映射的窗口 */
                                           xcb_visualid_t    visual); /* 屏幕支持的 visual Id */ 

示例：

    #include <xcb/xcb.h>

    int
    main ()
    {
      xcb_connection_t *c;
      xcb_screen_t     *screen;
      xcb_window_t      win;
      xcb_colormap_t    cmap;

      c = xcb_connect (NULL, NULL);
      screen = xcb_setup_roots_iterator (xcb_get_setup (c)).data;

      /* 在此創建窗口 */

      cmap = xcb_generate_id (c);
      xcb_create_colormap (c, XCB_COLORMAP_ALLOC_NONE, cmap, win, screen->root_visual);

      return 0;
    }

####Note
`window` 參數只是用來允許 X 服務器對給出的屏幕創建顏色映射。我們能用這個顏色映射傳入任意同屏幕的窗口。

要釋放顏色映射，使用這個函數：

    xcb_void_cookie_t xcb_free_colormap (xcb_connection_t *c,
                                         xcb_colormap_t cmap);  /* 顏色映射 */

####和 Xlib 的比較

    XCreateColormap ()          xcb_generate_id ()
                                xcb_create_colormap ()
    XFreeColormap ()            xcb_free_colormap ()
