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
                         250, 250,                      /* 寬和高 */)
