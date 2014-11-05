取得窗口信息
===

就像我們能設置窗口的各種屬性一樣，我們同樣能詢問 X 服務器當前客戶端的屬性。例如，我們能檢查窗口的屏幕位置，當前大小，是否已被映射等等。這個結構包含了這些信息：

    typedef struct {
        uint8_t      response_type;
        uint8_t      depth;         /* 窗口深度 */
        uint16_t     sequence;
        uint32_t     length;
        xcb_window_t root;          /* 根窗口的 Id */
        int16_t      x;             /* 窗口 X 坐標 */
        int16_t      y;             /* 窗口 Y 坐標 */
        uint16_t     width;         /* 窗口寬 */
        uint16_t     height;        /* 窗口高 */
        uint16_t     border_width;  /* 窗口邊框寬 */
    } xcb_get_geometry_reply_t;

XCB 使用兩個函數填滿這個結構：

    xcb_get_geometry_cookie_t  xcb_get_geometry       (xcb_connection_t         *c,
                                                       xcb_drawable_t            drawable);
    xcb_get_geometry_reply_t  *xcb_get_geometry_reply (xcb_connection_t         *c,
                                                       xcb_get_geometry_cookie_t cookie,
                                                       xcb_generic_error_t     **e);

示例：

    xcb_connection_t         *c;
    xcb_drawable_t            win;
    xcb_get_geometry_reply_t *geom;

    /* 已經初始化 c 和 win */

    geom = xcb_get_geometry_reply (c, xcb_get_geometry (c, win), NULL);

    /* 做一些操作 */

    free (geom);

當 xcb_get_geometry_reply_t 重新分配了一個時，不要忘記釋放這個結構。

有個問題，這樣返回了窗口位置給有關的父窗口。這樣會導致獲取的坐標失效，例如移動窗口。為了避免這個問題，我們需要進行兩步操作。首先，我們找出我們窗口的父窗口的 Id 。然後轉換有關坐標到屏幕坐標。

要得到父窗口的 Id ，我們需要這個結構：

    typedef struct {
        uint8_t      response_type;
        uint8_t      pad0;
        uint16_t     sequence;
        uint32_t     length;
        xcb_window_t root;
        xcb_window_t parent;        /* 父窗口 Id */
        uint16_t     children_len;
        uint8_t      pad1[14];
    } xcb_query_tree_reply_t;

XCB 使用兩個函數填滿這個結構：

    xcb_query_tree_cookie_t xcb_query_tree       (xcb_connection_t        *c,
                                                  xcb_window_t             window);
    xcb_query_tree_reply_t *xcb_query_tree_reply (xcb_connection_t        *c,
                                                  xcb_query_tree_cookie_t  cookie,
                                                  xcb_generic_error_t    **e);

這個結構轉換坐標：

    typedef struct {
        uint8_t      response_type;
        uint8_t      same_screen;
        uint16_t     sequence;
        uint32_t     length;
        xcb_window_t child;
        uint16_t     dst_x;         /* 已轉換的 X 坐標 */
        uint16_t     dst_y;         /* 已轉換的 Y 坐標 */
    } xcb_translate_coordinates_reply_t;

XCB 使用兩個函數填滿這個結構：
    
    xcb_translate_coordinates_cookie_t xcb_translate_coordinates       (xcb_connection_t                    *c,
                                                                        xcb_window_t                        src_window,
                                                                        xcb_window_t                        dst_window,
                                                                        int16_t                             src_x,
                                                                        int16_t                             src_y);
    xcb_translate_coordinates_reply_t *xcb_translate_coordinates_reply (xcb_connection_t                    *c,
                                                                        xcb_translate_coordinates_cookie_t  cookie,
                                                                        xcb_generic_error_t                **e);

示例：

    xcb_connection_t                  *c;
    xcb_drawable_t                     win;
    xcb_get_geometry_reply_t          *geom;
    xcb_query_tree_reply_t            *tree;
    xcb_translate_coordinates_reply_t *trans;

    /* 初始化 c 和 win */

    geom = xcb_get_geometry_reply (c, xcb_get_geometry (c, win), NULL);
    if (!geom)
      return 0;

    tree = xcb_query_tree_reply (c, xcb_query_tree (c, win), NULL);
    if (!tree)
      return 0;

    trans = xcb_translate_coordinates_reply (c,
                                             xcb_translate_coordinates (c,
                                                                        win,
                                                                        tree->parent,
                                                                        geom->x, geom->y),
                                             NULL);
    if (!trans)
      return 0;

    /* 轉換坐標在 trans->dst_x 和 trans->dst_y */

    free (trans);
    free (tree);
    free (geom);
