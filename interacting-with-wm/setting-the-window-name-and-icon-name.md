設置窗口和圖標的名稱
===

我們首先想設置的應該是我們的窗口的名稱了。這個可以用 `xcb_change_property()` 函數完成。名稱是用來給窗口管理器使用的（在標題欄裡），和任務欄等等。這個屬性名為 `WM_NAME` （`WM_ICON_NAME` 是最小化窗口使用的），並且它的類型為 `STRING` 。這裡有一個示例：

    #include <string.h>
    
    #include <xcb/xcb.h>
    #include <xcb/xcb_atom.h>

    int
    main ()
    {
      xcb_connection_t *c;
      xcb_screen_t     *screen;
      xcb_window_t      win;
      char             *title = "Hello World !";
      char             *title_icon = "Hello World ! (iconified)";



      c = xcb_connection_t (NULL, NULL);

      screen = xcb_setup_roots_iterator (xcb_get_setup (c)).data;

      win = xcb_generate_id (c);

      xcb_create_window (c,
                         0,                             /* 深度 */
                         win,                           /* 窗口 Id */
                         screen->root,                  /* 父窗口 */
                         0, 0,                          /* x, y */
                         250, 250,                      /* 寬和高 */
                         10,                            /* 邊框寬 */
                         XCB_WINDOW_CLASS_INPUT_OUTPUT, /* class */
                         screen->root_visual,           /* visual */
                         0, NULL);                      /* 掩碼，不使用 */

      /* 設置窗口標題 */
      xcb_change_property (c, XCB_PROP_MODE_REPLACE, win,
                           WM_NAME, STRING, 8,
                           strlen (title), title);

      /* 設置窗口圖標標題 */
      xcb_change_property (c, XCB_PROP_MODE_REPLACE, win,
                           WM_ICON_NAME, STRING, 8,
                           strlen(title_icon), title_icon);

      xcb_map_window (c, win);

      xcb_fluch (c);

      while (1) {}

      return 0;
    }

####Note
使用 `atom` 需要我們的程序在編譯的時候連接 `xcb_atom` ，那麼我們用這條命令：

    gcc prog.c -o prog `pkg-config --cflags --libs xcb_atom`
