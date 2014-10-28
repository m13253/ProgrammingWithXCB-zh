鼠標的按下和放開事件
===

我們的窗口首先要對付的是鼠標按下和放開事件。為了寄存這些事件類型，我們應該在創建窗口的時候添加一個（或者更多）以下的掩碼：

- XCB_EVENT_MASK_BUTTON_PRESS   鼠標指針在窗口內按下任意鍵
- XCB_EVENT_MASK_BUTTON_RELEASE 鼠標指針在窗口內放開任意鍵

這個結構會被之前我們所寫的循環中被檢查，這兩個事件都會如此，結構代碼如下：

    typedef struct {
        uint8_t         response_type; /* 事件類型，這裡是 xcb_button_press_event_t 或者 xcb_button_release_event_t */
        xcb_button_t    detail;
        uint16_t        sequence;
        xcb_timestamp_t time;          /* 時間，以毫秒為單位 */
        xcb_window_t    root;
        xcb_window_t    event;
        xcb_window_t    child;
        int16_t         root_x;
        int16_t         root_y;
        int16_t         event_x;       /* 鼠標事件被觸發時在窗口中的 X 坐標 */
        int16_t         event_y;       /* 鼠標事件被觸發時在窗口中的 Y 坐標 */
        uint16_t        state;         /* 鼠標按鍵掩碼 */
        uint8_t         same_screen;
    } xcb_button_press_event_t;

    typedef xcb_button_press_event_t xcb_button_release_event_t;

`time` 這個成員是用來計算“雙擊”這個行為的（如果按鍵被短時間內連續按下兩次，就可以看作雙擊）。
`state` 這個成語是鼠標按鍵的掩碼。它可以用位或來進行組合（它被定義在 `xcb_button_mask_t` 和 `xcb_mod_mask_t` 枚舉中）：

- XCB_BUTTON_MASK_1
- XCB_BUTTON_MASK_2
- XCB_BUTTON_MASK_3
- XCB_BUTTON_MASK_4
- XCB_BUTTON_MASK_5
- XCB_MOD_MASK_SHIFT
- XCB_MOD_MASK_LOCK
- XCB_MOD_MASK_CONTORL
- XCB_MOD_MASK_1
- XCB_MOD_MASK_2
- XCB_MOD_MASK_3
- XCB_MOD_MASK_4
- XCB_MOD_MASK_5

顧名思義，前五個掩碼代表鼠標上的五個按鍵，而其他的掩碼則代表著特殊鍵（通常，Mod1 代表 "Alt" 或者 "Meta" 鍵）。

TODO: 問題：當按下各種按鍵時，狀態似乎沒有改變。
