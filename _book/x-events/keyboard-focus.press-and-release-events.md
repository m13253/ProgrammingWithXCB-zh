jianp1de1anx1he1fangk1shshij2
===

rug1chuangyige11hkongzhbbei1wom1dchengxu1i,chengw1l1jpjd1,t2jiun1jies2jp12anx1he1fangk1shshij2.nam1wom1yingg1zai1chuangj1ck1shi2tianj1yyx4yanm1:

- XCB_EVENT_MASK_KEY_PRESS      dang1wom1kongzhi1chuangk1wei1jp11shi2,mou1anj1bei1anx1de1shj2
- XCB_EVENT_MASK_KEY_RELEASE    dang1wom1kongzhi1chuangk1wei1jp11shi2,mou1anj1bei1anx1dfangk1hj2

zhege1jiejieg2huib1wom1de1shij2xunh1zhong1bejianc1,jieg2daim1rux1:

    typedef struct {
        uint8_t         response_type; /* shij2leix1 */
        xcb_keycode_t   detail;
        uint16_t        sequence;
        xcb_timestamp_t time;          /* shj1,yi2haomiao1wei1danw1 */
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

`detail` chengy2kekeyi1cank1wul3anjianp1de1j1.

TODO: guanyu1cong1 ASCIIjian3ma4zhong1qude1 ma6zhonde1shiq1.
