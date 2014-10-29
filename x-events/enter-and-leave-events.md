鼠標指針進入和離開區域事件
===

另一種事件也許是應用所感興趣的，一個鼠標指針進入窗口或離開窗口。一些程序使用這些事件來讓用戶知道這個應用現在是聚焦狀態。為了寄存這些事件類型，當我們創建窗口時，添加一個或多個以下的掩碼：

- xcb_event_enter_window_t 鼠標指針進入窗口事件
- xcb_event_leave_window_t 鼠標指針離開窗口事件

這個結構會被我們寫的循環中被檢查，結構代碼如下：

    typedef struct {
        uint8_t         response_type; /* 事件類型 */
        uint8_t         detail;
        uint16_t        sequence;
        xcb_timestamp_t time;          /* 時間，以毫秒為單位 */
        xcb_window_t    root;
        xcb_window_t    event;
        xcb_window_t    child;
        int16_t         root_x;
        int16_t         root_y;
        int16_t         event_x;       /* 事件被生成時，鼠標的 X 坐標 */
        int16_t         event_y;       /* 事件被生成時，鼠標的 Y 坐標 */
        uint16_t        state;         /* 鼠標按鍵掩碼 */
        uint8_t         mode;          /* 鼠標被按下的按鍵數 */
        uint8_t         same_screen_focus;
    } xcb_enter_notify_event_t;

    typedef xcb_enter_notify_event_t xcb_leave_notify_event_t;
