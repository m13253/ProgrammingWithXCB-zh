完全示例
===

這個示例在窗口底部的 10 個像素內描繪文字（以基線）。按 Esc 鍵即可退出這個程序。

    #include <stdlib.h>
    #include <stdio.h>
    #include <string.h>

    #include <xcb/xcb.h>

    #define WIDTH 300
    #define HEIGHT 100


    
    static xcb_gc_t gc_font_get (xcb_connection_t *c,
                                 xcb_screen_t     *screen,
                                 xcb_window_t      window,
                                 const char       *font_name);

    static void text_draw (xcb_connection_t *c,
                           xcb_screen_t     *screen,
                           xcb_window_t      window,
                           int16_t           x1,
                           int16_t           y1,
                           const char       *label);

    static void
    text_draw (xcb_connection_t *c,
               xcb_screen_t     *screen,
               xcb_window_t      window,
               int16_t           x1,
               int16_t           y1,
               const char       *label)
    {
      xcb_void_cookie_t    cookie_gc;
      xcb_void_cookie_t    cookie_text;
      xcb_generic_error_t *error;
      xcb_gcontext_t       gc;
      uint8_t              length;

      length = strlen (label);

      gc = gc_font_get(c, screen, window, "7x13");

      cookie_text = xcb_image_text_8_checked (c, length, window, gc,
                                              x1,
                                              y1, label);
      error = xcb_request_check (c, cookie_text);
      if (error) {
        fprintf (stderr, "ERROR: can't paste text : %d\n", error->error_code);
        xcb_disconnect (c);
        exit (-1);
      }

      cookie_gc = xcb_free_gc (c, gc);
      error = xcb_request_check (c, cookie_gc);
      if (error) {
        fprintf (stderr, "ERROR: can't free gc : %d\n", error->error_code);
        xcb_disconnect (c);
        exit (-1);
      }
    }

    static xcb_gc_t
    gc_font_get (xcb_connection_t *c,
                 xcb_screen_t     *screen,
                 xcb_window_t      window,
                 const char       *font_name)
    {
      uint32_t             value_list[3];
      xcb_void_cookie_t    cookie_font;
      xcb_void_cookie_t    cookie_gc;
      xcb_generic_error_t *error;
      xcb_font_t           font;
      xcb_gcontext_t       gc;
      uint32_t             mask;

      font = xcb_generate_id (c);
      cookie_font = xcb_open_font_checked (c, font,
                                           strlen (font_name),
                                           font_name);

      error = xcb_request_check (c, cookie_font);
      if (error) {
        fprintf (stderr, "ERROR: can't open text : %d\n", error->error_code);
        xcb_disconnect (c);
        exit (-1);
      }

      gc = xcb_generate_id (c);
      mask = XCB_GC_FOREGROUND ｜XCB_GC_BACKGROUND | XCB_GC_FONT;
      value_list[0] = screen->black_pixel;
      value_list[1] = screen->white_pixel;
      value_list[2] = font;
      cookie_gc = xcb_create_gc_checked (c, gc, window, mask, value_list);
      error = xcb_request_check (c, cookie_gc);
      if (error) {
        fprintf (stderr, "ERROR: can't create gc : %d\n", error->error_code);
        xcb_disconnect (c);
        exit (-1);
      }

      cookie_font = xcb_close_font_checked (c, font);
      error = xcb_request_check (c, cookie_font);
      if (error) {
        fprintf (stderr, "ERROR: can't close gc : %d\n", error->error_code);
        xcb_disconnect (c);
        exit (-1);
      }

      return gc;
    }

    int main ()
    {
      xcb_screen_iterator_t screen_iter;
      xcb_connection_t     *c;
      const xcb_setup_t    *setup;
      xcb_screen_t         *screen;
      xcb_generic_event_t  *e;
      xcb_generic_error_t  *error;
      xcb_void_cookie_t     cookie_window;
      xcb_void_cookie_t     cookie_map;
      xcb_window_t          window;
      uint32_t              mask;
      uint32_t              values[2];
      int                   screen_number;

      /* 連接 */
      c = xcb_connect (NULL, &screen_number);
      if (!c) {
        fprintf (stderr, "ERROR: can't connect to an X server\n");
        return -1;
      }

      /* 取得當前屏幕 */
      setup = xcb_get_setup (c);

      screen = NULL;
      screen_iter = xcb_setup_roots_iterator (setup);
      for (; screen_iter.rem != 0; --screen_number, xcb_screen_next (&screen_iter))
        if (screen_number == 0)
          {
            screen = screen_iter.data;
            break;
          }
      if (!screen) {
        fprintf (stderr, "ERROR: can't get the current screen\n");
        xcb_disconnect (c);
        return -1;
      }

      /* 創建窗口 */
      window = xcb_generate_id (c);
      mask = XCB_CW_BACK_PIXEL | XCB_CW_EVENT_MASK;
      values[0] = screen>white_pixel;
      values[1] =
        XCB_EVENT_MASK_KEY_RELEASE |
        XCB_EVENT_MASK_BUTTON_PRESS |
        XCB_EVENT_MASK_EXPOSURE |
        XCB_EVENT_MASK_POINTER_MOTION;
      cookie_window = xcb_create_window_checked (c,
                                                 screen->root_depth,
                                                 window, screen->root,
                                                 20, 200, WIDTH, HEIGHT,
                                                 0, XCB_WINDOW_CLASS_INPUT_OUTPUT,
                                                 screen->root_visual,
                                                 mask, values);
      cookie_map = xcb_map_window_checked (c, window);

      /* 錯誤管理 */
      error = xcb_request_check (c, cookie_window);
      if (error) {
        fprintf (stderr, "ERROR: can't create window : %d\n", error->error_code);
        xcb_disconnect (c);
        return -1;
      }
      error = xcb_request_check (c, cookie_map);
      if (error) {
        fprintf (stderr, "ERROR: can't map window : %d\n", error->error_code);
        xcb_disconnect (c);
        return -1;
      }

      xcb_flush(c);

      while (1) {
        e = xcb_poll_for_event(c);
        if (e) {
          switch (e->response_type & ~0x80) {
          case XCB_EXPOSE: {
            chat *text;

            text = "Press ESC key to exit...";;
            text_draw (c, screen, window, 10, HEIGHT - 10, text);
            break;
          }
          case XCB_KEY_RELEASE: {
            xcb_key_release_event_t *ev;

            ev = (xcb_key_release_event_t *)e;

            switch (ev->detail) {
              /* ESC */
            case 9:
              free (e);
              xcb_disconnect (c);
              return 0;
            }
          }
          }
          free (e);
        }
      }

      return 0;
    }
