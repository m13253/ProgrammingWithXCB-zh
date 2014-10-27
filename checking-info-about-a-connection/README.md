#檢查連接的基本信息
當我們已經打開了一個對 X 服務器的連接，我們應該檢查一些關於它的基本的信息：有多少屏幕，屏幕分辨率有多大，它支持多少種顏色（黑白？灰度？256 色？還是更多？）等等。我們可以從 `xcb_screen_t` 中獲得這些信息：

    typedef struct {
        xcb_window_t    root;
        xcb_colormap_t  default_colormap;
        uint32_t        white_pixel;
        uint32_t        black_pixel;
        uint32_t        current_input_masks;
        uint16_t        width_in_pixels;
        uint16_t        height_in_pixels;
        uint16_t        width_in_millimeters;
        uint16_t        height_in_millimeters;
        uint16_t        min_installed_maps;
        uint16_t        max_installed_maps;
        xcb_visualid_t  root_visual;
        uint8_t         backing_stores;
        uint8_t         save_unders;
        uint8_t         root_depth;
        uint8_t         allowed_depths_len;
    } xcb_screen_t;

我們能夠用接下來的這個函數檢索連接的第一屏幕：

    xcb_screen_iterator_t xcb_setup_roots_iterator(xcb_setup_t *R);

這裡有一個小程序來顯示了這個函數的用法：
    
    #include <stdio.h>
    #include <xcb/xcb.h>

    int
    main()
    {
      xcb_connection_t      *c;
      xcb_screen_t          *screen;
      int                   screen_nbr;
      xcb_screen_iterator_t iter;

      /* 開啟對 X 服務器的連接，使用環境變量 DISPLAY 。 */
      c = xcb_connect(NULL, &screen_nbr);

      /* 取得屏幕 #screen_nbr*/
      iter = xcb_setup_roots_iterator(xcb_get_setup(c));
      for (; iter.rem; --screen_nbr, xcb_screen_next(&iter))
        if (screen_nbr == 0){
          screen = iter.data;
          break;
        }

      printf ("\n");
      printf ("Informations of screen %ld:\n", screen->root);
      printf ("  width.........: %d\n", screen->width_in_pixels);
      printf ("  height........: %d\n", screen->height_in_pixels);
      printf ("  white pixel...: %d\n", screen->white_pixel);
      printf ("  black pixel...: %d\n", screen->black_pixel);
      printf ("\n");

      return 0;
    }
