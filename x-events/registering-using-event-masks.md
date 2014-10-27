使用事件掩碼寄存事件類型
===

在創建一個窗口的過程中，你應該給出它所接收的事件種類。那麼，你可能會寄存各種鼠標（也稱作指針）事件，鍵盤事件，暴露事件，等等。這會在 optimizing 服務器到客戶端的連接中完成。（i.e. why send a program (that might even be running at the other side of globe) an event it is not interested in?)


在 XCB 中，你使用 `value_mask` 和 `value_list` 在 `xcb_create_window()` 函數中寄存事件。這裡是一個如何在創建窗口時寄存暴露事件的例子：

    mask = XCB_CW_EVENT_MASK
    valwin[0] = XCB_EVENT_MASK_EXPOSURE;
    win = xcb_generate_id (c);
    xcb_create_window (c, depth, win, root->root,
                       0, 0, 150, 150, 10,
                       XCB_WINDOW_CLASS_INPUT_OUTPUT, root->root_visual,
                       mask, valwin);

XCB_EVENT_MASK_EXPOSURE 定義在 `xproto.h` 頭文件的一個叫 `xcb_event_mask_t` 的枚舉中。如果我們想寄存若干事件類型，那麼我們可以用位或。就像這樣：

    mask = XCB_CW_EVENT_MASK
    valwin[0] = XCB_EVENT_MASK_EXPOSURE | XCB_EVENT_MASK_BUTTON_PRESS;
    win = xcb_generate_id (c);
    xcb_create_window (c, depth, win, root->root,
                       0, 0, 150, 150, 10,
                       XCB_WINDOW_CLASS_INPUT_OUTPUT, root->root_visual,
                       mask, valwin);

這樣就寄存了暴露事件。鼠標一旦在窗口中點擊，窗口就會暴露。You should note that a mask may represent several event sub-types.

`xcb_cw_t` 給出了掩碼值：

    typedef enum {
        XCB_CW_BACK_PIXMAP       = 1L<<0,
        XCB_CW_BACK_PIXEL        = 1L<<1,
        XCB_CW_BORDER_PIXMAP     = 1L<<2,
        XCB_CW_BORDER_PIXEL      = 1L<<3,
        XCB_CW_BIT_GRAVITY       = 1L<<4,
        XCB_CW_WIN_GRAVITY       = 1L<<5,
        XCB_CW_BACKING_STORE     = 1L<<6,
        XCB_CW_BACKING_PLANES    = 1L<<7,
        XCB_CW_BACKING_PIXEL     = 1L<<8,
        XCB_CW_OVERRIDE_REDIRECT = 1L<<9,
        XCB_CW_SAVE_UNDER        = 1L<<10,
        XCB_CW_EVENT_MASK        = 1L<<11,
        XCB_CW_DONT_PROPAGATE    = 1L<<12,
        XCB_CW_COLORMAP          = 1L<<13,
        XCB_CW_CURSOR            = 1L<<14
    } xcb_cw_t;

####Note
我們一定要小心設置 valwin 參數的值。這裡有個例子：

    mask = XCB_CW_EVENT_MASK | XCB_CW_BACK_PIXMAP;
    valwin[0] = XCB_NONE;                                              /* XCB_CW_BACK_PIXMAP (1) */
    valwin[1] = XCB_EVENT_MASK_EXPOSURE | XCB_EVENT_MASK_BUTTON_PRESS; /* XCB_CW_ENENT_MASK (2048) */
                                                                       /* 比 XCB_CW_BACK_PIXMAP 要好*/

如果窗口已經被創建了，我們能夠使用 `xcb_change_window_attributes()` 函數來設置窗口要接收的事件。這裡有一個例子，配置窗口使其能夠接收暴露事件和按鈕按下事件：

    const static uint32_t values[] = { XCB_EVENT_MASK_EXPOSURE | XCB_EVENT_MASK_BUTTON_PRESS };

    /* 連接 (c) 和窗口 (win  已經被定義了 */
    xcb_change_window_attributes (c, win, XCB_CW_EVENT_MASK, values);

####Note
    一個通常出現的 Bug 就是，程序員在他們的程序中添加了控制新事件類型的代碼，而忘記在創建窗口的時候添加事件掩碼。那麼就會有一個悲慘的碼農坐下來花上幾個小時來調試程序，卻一直為“為何我的程序在我放開按鍵後沒有反應？”這個問題所困擾。他明明就只需要在按鍵按下事件後面繼續添加一個按鍵放開事件。
