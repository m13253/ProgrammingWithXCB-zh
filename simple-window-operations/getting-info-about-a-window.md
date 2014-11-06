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

當然， geom, tree 和 trans 都需要被釋放。

這個工作有些困難，但 XCB 是一個非常低級的庫。

TODO: the utilization of these functions should be a prog, which displays the coordinates of the window.

這是另一個提供窗口信息的結構：

    typedef struct {
        uint8_t         response_type;
        uint8_t         backing_store;
        uint16_t        sequence;
        uint32_t        length;
        xcb_visualid_t  visual;                 /* visual */
        uint16_t        _class;
        uint8_t         bit_gravity;
        uint8_t         win_gravity;
        uint32_t        backing_planes;
        uint32_t        backing_pixel;
        uint8_t         save_under;
        uint8_t         map_is_installed;
        uint8_t         map_state;              /* 窗口映射狀態 */
        uint8_t         override_redirect;
        xcb_colormap_t  colormap;               /* 窗口的顏色映射 */
        uint32_t        all_event_masks;
        uint32_t        your_event_mask;
        uint16_t        do_not_propagate_mask;
    } xcb_get_window_attributes_reply_t;

XCB 使用兩個函數填滿這個結構：

    xcb_get_window_attributes_cookie_t xcb_get_window_attributes       (xcb_connection_t                    *c,
                                                                        xcb_window_t                         window);
    xcb_get_window_attributes_reply_t *xcb_get_window_attributes_reply (xcb_connection_t                    *c,
                                                                        xcb_get_window_attributes_cookie_t   cookie,
                                                                        xcb_generic_error_t                **e);

示例：

    xcb_connection_t                  *c;
    xcb_drawable_t                     win;
    xcb_get_window_attributes_reply_t *attr;

    /* 初始化 c 和 win */

    attr = xcb_get_window_attributes_reply (c, xcb_get_window_attributes (c, win), NULL);

    if (!attr)
      return 0;

    /* 對 attr 進行操作 */

    free (attr);

和 geom 一樣，attr 也要釋放。
