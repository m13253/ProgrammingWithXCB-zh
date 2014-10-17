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

最後，這個示例創建了一個 150x150 像素大小的窗口，并放置在屏幕左上角：

    #include <unistd.h>      /* pause() */

    #include <xcb/xcb.h>

    int
    main ()
    {
      xcb_connection_t *c;
      xcb_screen_t     *screen;
      xcb_window_t      win;

      /* Open the connection to the X server */
      c = xcb_connect (NULL, NULL);

      /* 取得第一屏幕 */
      screen = xcb_setup_roots_iterator (xcb_get_setup (c)).data;

      /* 查詢窗口的 Id */
      win = xcb_generate_id(c);

      /* 創建窗口 */
      xcb_create_window (c,                             /* Connection */
                         XCB_COPY_FROM_PARENT,          /* 深度（和根窗口一樣）*/
                         win,                           /* window Id */
                         screen->root,                  /* parent window */
                         0, 0,                          /* x, y */
                         150, 150,                      /* width, height */
                         10,                            /* border_width */
                         XCB_WINDOW_CLASS_INPUT_OUTPUT, /* class */
                         screen->root_visual,           /* visual */
                         0, NULL);                      /* 掩碼，尚未使用 */

      /* 在屏幕上映射窗口 */
      xcb_map_window (c, win);

      /* 確信命令會在停止之後發送，所以顯示窗口 */
      xcb_flush (c);

      pause ();    /* 保持客戶端狀態直到按下 Ctrl-C */

      return 0;
    }

在示例中，你一定看見了一個我們尚未解說的函數—— `xcb_flush()` 。它其實是用來清空待定的請求的。更精確地說，這也可以用兩個函數來完成同樣的事。一個是 `xcb_flush()` ：
    
    int xcb_flush(xcb_connection_t *c);

這個函數清空了所有待定的對 X 服務器的請求（就像 fflush() 函數一樣用來清空標準輸出）。另一個函數是 xcb_aux_sync() ：

    int xcb_aux_sync(xcb_connection_t *c);

這個函數也是清空了所有待定的對 X 服務器的請求，並且它會等待 X 服務器直到它處理完畢這些請求。在普通的程序中，這個函數不是必須的。
沒有定義背景的窗口就這樣以上面的代碼創造了。它其實能夠指定顏色，因為還有 `xcb_create_window()` 中的最後兩個參數，雖然他們還尚未解說。啊，忘記說件事，你不得不按下 Ctrl-C 來中斷這個程序。

TODO: 我們需要一個苦力來解說這些函數的返回值和通常會發生的錯誤。 XCB UCCU，你大概是找不到這個苦力啦！

####和 Xlib 的比較
    XCreateWindow ()        xcb_generate_id ()
                            xcb_create_window ()
