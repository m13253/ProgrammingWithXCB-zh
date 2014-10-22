#繪圖primitives：點、線、矩形、圓……
在我們創建了一個圖形上下文後，我們就可以用這個圖形上下文來對窗口繪圖啦。在 XCB 函數中，這些函數叫做**繪圖例程**。讓我們看看它們是怎樣使用的吧。
想畫一個或若干個點，我們用：

    xcb_void_cookie_t xcb_poly_point (xcb_connection_t  *c,
                                      uint8_t            coordinate_mode,    /* coordinate mode 經常設置成 XCB_COORD_MODE_ORIGIN */
                                      xcb_drawable_t     drawable,
                                      xcb_gcontext_t     gc,
                                      uint32_t           points_len,         /* 點數 */
                                      const xcb_point_t *points);            /* 點的數組 */

`coordinate_mode` 參數指定了 coordinate mode. 有效的值是：

- XCB_COORD_MODE_ORIGIN
- XCB_COORD_MODE_PREVOIUS

如果使用了 XCB_COORD_MODE_PREVOIUS ，那麼所有的點
`xcb_point_t` 這個類型是一個只含有兩個成員（點坐標）的結構：

    typedef struct {
        int16_t x;
        int16_t y;
    } xcb_point_t

你將看到在 xponiter.c 中看到示例。TODO: 那麼幫我設置連接吧。（XCB 你看看你，十三年來都沒時間寫文檔嗎？）
用這個函數可以畫一條線：
    
    xcb_void_cookie_t xcb_poly_line (xcb_connection_t  *c,
                                     uint8_t            coordinate_mode,
                                     xcb_drawable_t     drawable,
                                     xcb_gcontext_t     gc,
                                     uint32_t           points_len,     /* 端點數 */
                                     const xcb_point_t *points);        /* 點的數組 */

這個函數將會在第一和第二個點之間畫一條線，同樣會第二和第三個點之間畫線，以此類推。
用這個函數來畫一條線段：

    xcb_void_cookie_t xcb_poly_segment (xcb_connection_t    *c,
                                        xcb_drawable_t       drawable,
                                        xcb_gcontext_t       gc,
                                        uint32_t             segments_len,  /* 線段數 */
                                        const xcb_segment_t *segments);     /* 線段的數組 */

`xcb_segment_t` 這個類型是一個只含有四個成員（點一和點二的坐標）的結構：

    typedef struct {
        int16_t x1;
        int16_t y1;
        int16_t x2;
        int16_t y2;
    } xcb_segment_t;

畫一個或多個矩形，使用這個函數：

    xcb_void_cookie_t xcb_poly_rectangle (xcb_connection_t      *c,
                                          xcb_drawable_t         drawable,
                                          xcb_gcontext_t         gc,
                                          uint32_t               rectangles_len, /* 矩形數量 */ 
                                          const xcb_rectangle_t *rectangles);    /* 矩形的數組 */

`xcb_rectangle_t` 這個類型是一個含有四個成員（左上角坐標，寬和高）的數組：

    typedef struct {
        int16_t  x;
        int16_t  y;
        uint16_t width;
        uint16_t height;
    } xcb_rectangle_t;

要畫一個或多個弧，用這個函數：

    xcb_void_cookie_t xcb_poly_arc (xcb_connection_t *c,
                                    xcb_drawable_t    drawable,
                                    xcb_gcontext_t    gc,
                                    uint32_t          arcs_len,  /* 弧數 */
                                    const xcb_arc_t  *arcs)      /* 弧的數組 */

`xcb_arc_t` 這個類型是一個含有六個成員的結構：

    typedef struct {
        int16_t  x;
        int16_t  y;
        uint16_t width;
        uint16_t height;
        int16_t  angle1;  /* 弧起始角度 */
        int16_t  angle2;  /* 弧終止角度 */
    } xcb_arc_t;

####Note
弧的角度以角度的 1/64 作為單位，那麼要生成一個從 0 度開始， 90 度的角，那麼參數的設置是這樣的： angle1 = 0 ， angle2 = 90 << 6。正向角是逆時針的，而反向角是順時針的。

這些幾何圖形都不會填充內部。那麼為了填充一個幾何圖形，就使用這個函數：

    xcb_void_cookie_t xcb_fill_poly (xcb_connection_t  *c,
                                     xcb_drawable_t     drawable,
                                     xcb_gcontext_t     gc,
                                     uint8_t            shape,
                                     uint8_t            coordinate_mode,
                                     uint32_t           points_len,
                                     const xcb_point_t *points)
`shape` 參數可以幫助服務器提高性能，可用的參數是：

- XCB_POLY_SHAPE_COMPLEX
- XCB_POLY_SHAPE_NONCONVEX
- XCB_POLY_SHAPE_CONVEX

填充一個或多個矩形，使用這個函數：

    xcb_void_cookie_t xcb_poly_fill_rectangle (xcb_connection_t      *c,
                                               xcb_drawable_t         drawable,
                                               xcb_gcontext_t         gc,
                                               uint32_t               rectangles_len,
                                               const xcb_rectangle_t *rectangles);

填充一條或多條弧，使用這個函數：
    
    xcb_void_cookie_t xcb_poly_fill_arc (xcb_connection_t *c,
                                         xcb_drawable_t    drawable,
                                         xcb_gcontext_t    gc,
                                         uint32_t          arcs_len,
                                         const xcb_arc_t  *arcs);
這裡有一個示例，它畫了四個點，一條線，兩條線段，兩個矩形和兩條弧。

TODO: 使用 screen->root_depth 作為 depth 的參數。

    #include <stdlib.h>
    #include <stdio.h>
    
    #include <xcb/xcb.h>

    int
    main()
    {
      xcb_connection_t    *c;
      xcb_screen_t        *screen;
      xcb_drawable_t       win;
      xcb_gcontext_t       foreground;
      xcb_generic_event_t *e;
      uint32_t             mask = 0;
      uint32_t             values[2];

      /* 幾何對象 */
      xcb_point_t          points[] = {
        {10, 10},
        {10, 20},
        {20, 10},
        {20, 20}};

      xcb_point_t          polyline[] = {
        {50, 10],
        { 5, 20},
        {25,-20},
        {10, 10}};

      xcb_segment_t        segments[] = {
        {100, 10, 140, 30},
        {110, 25, 130, 60}};

      xcb_rectangle_t      rectangles[] = {
        { 10, 50, 40, 20},
        { 80, 50, 10, 40}};

      xcb_arc_t            arcs[] = {
        {10, 100, 60, 40, 0, 90 << 6},
        {90, 100, 55, 40, 0, 270 << 6}};

      c = xcb_connect (NULL, NULL);

      screen = xcb_setup_roots_iterator (xcb_get_setup (c)).data;
      
      win = screen->root;

      foreground = xcb_generate_id (c);
      mask = XCB_GC_FOREGROUND | XCB_GC_GRAPHICS_EXPOSURES;
      values[0] = screen->black_pixel;
      values[1] = 0;
      xcb_create_gc (c, foreground, win, mask, values);

      /* 取得窗口的 Id */
      win = xcb_generate_id(c);

      /* 創建窗口 */
      mask = XCB_CW_BACK_PIXEL | XCB_CW_EVENT_MASK;
      values[0] = screen->white_pixel;
      values[1] = XCB_EVENT_MASK_EXPOSURE;
      xcb_create_window (c,
                        XCB_COPY_FORM_PARENT,           /* 深度 */
                        win,
                        screen->root,
                        0, 0,                           /* x, y */
                        150, 150,                       /* 寬和高 */
                        10,                             /* 邊框寬 */
                        XCB_WINDOW_CLASS_INPUT_OUTPUT,  /* class */
                        screen->root_visual,            /* visual */
                        mask, values);                  /* 掩碼 */

      /* 映射窗口 */
      xcb_map_window (c, win);

      /* 清空請求 */
      xcb_flush (c);

      while ((e = xcb_wait_for_event (c))) {
        switch (e->response_type & ~0x80) {
        case XCB_EXPOSE:
          /* 畫點 */
          xcb_poly_point (x, XCB_COORD_MODE_ORIGIN, win, foreground, 4, points);

          /* 畫線 */
          xcb_poly_line (c, XCB_COORD_MODE_PREVOIUS, win, foreground, 4, polyline);

          /* 畫線段 */
          xcb_poly_segment (c, win, foreground, 2, segments);

          /* 畫矩形 */
          xcb_poly_rectangle (c, win, foreground, 2, rectangles);

          /* 畫弧 */
          xcb_poly_arc (c, win, foreground, 2, arcs);

          /* 清空請求 */
          xcb_flush (c);
          break;
        }
        default: {
          /* 未知類型事件，忽視它 */
          break;
        }
        }
        /* 釋放通用事件 */
        free (e);
      }
