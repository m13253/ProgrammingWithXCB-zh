鍵盤的按下和放開事件
===

如果一個窗口被我們的程序控制，成為了鍵盤焦點，它就能接收鍵盤的按下和放開事件。那麼我們應該在創建窗口時添加以下掩碼：

- XCB_EVENT_MASK_KEY_PRESS      當我們控制窗口為鍵盤焦點時，某按鍵被按下的事件
- XCB_EVENT_MASK_KEY_RELEASE    當我們控制窗口為鍵盤焦點時，某按鍵被放開的事件

這個結構會被我們的事件循環檢查，結構代碼如下：

    typedef struct {
        uint8_t         response_type; /* 事件類型 */
        xcb_keycode_t   detail;
        uint16_t        sequence;
        xcb_timestamp_t time;          /* 時間，以毫秒為單位 */
        xcb_window_t    root;
        xcb_window_t    event;
        xcb_window_t    child;
        int16_t         root_x;
        int16_t         root_y;
        int16_t         event_x;
        int16_t         event_y;
        uint16_t        state;
        uint8_t         same_screen;
    } xcb_key_press_event_t;

    typedef xcb_key_press_event_t xcb_key_release_event_t;

`detail` 成員可以參考物理鍵盤的按鍵。

TODO: 關於從鍵碼中取得 ASCII 碼的事情。
