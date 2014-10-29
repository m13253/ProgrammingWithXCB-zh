鼠標移動事件
===

就像鼠標按鍵按下和放開事件一樣，我們同樣能控制鼠標移動事件。它們被分為兩種。一種只是單純的鼠標移動，一種是拖動。以下掩碼可以在創建窗口時添加：

- XCB_EVENT_MASK_POINTER_MOTION     鼠標移動事件
- XCB_EVENT_MASK_BUTTON_MOTION      鼠標按鍵按下事件，這裡可以是一個或多個按鍵被按下
- XCB_EVENT_MASK_BUTTON_1_MOTION    和 XCB_EVENT_MASK_BUTTON_MOTION，不過僅限於第一個按鍵被按下
- XCB_EVENT_MASK_BUTTON_2_MOTION
  XCB_EVENT_MASK_BUTTON_3_MOTION 
  XCB_EVENT_MASK_BUTTON_4_MOTION
  XCB_EVENT_MASK_BUTTON_5_MOTION
  和 XCB_EVENT_MASK_BUTTON_1_MOTION 類似。

這個結構同樣會被循環檢查，結構的代碼如下：

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
        int16_t         event_x;       /* 當事件被生成的時候，鼠標的 X 坐標 */
        int16_t         event_y;       /* 當事件被生成的時候，鼠標的 Y 坐標 */
        uint8_t         state;         /* 鼠標按鍵掩碼 */
        uint8_t         same_screen;
    } xcb_motion_notify_event_t;
