窗口屬性
===

各種各樣的參數和窗口管理器通信，傳遞名為“屬性”的數據。這些屬性指示 X 服務器來應對不同窗口，並且存儲為一種可在不同機器上可讀的格式（記住 X 客戶端可以在遠端機器上執行）。

這些屬性（一行字符串和一個整數等）同樣也是 Id，類型為 `xcb_atom_t` ：

    typedef uint32_t xcb_atom_t;

為了改變窗口的屬性，我們使用這個函數：

    xcb_void_cookie_t xcb_change_property (xcb_connection_t *c,
                                           uint8_t          mode,     /* 屬性模式 */
                                           xcb_window_t     window,   /* 窗口 */
                                           xcb_atom_t       property, /* 要改變的屬性 */
                                           xcb_atom_t       type,     /* 屬性類型 */
                                           uint8_t          format,   /* 屬性格式 (8, 16, 32) */
                                           uint32_t         data_len, /* data 參數的長度 */
                                           const void      *data);    /* 數據 */

`mode` 參數能取以下值（它們被定義在 `xproth.h` 頭文件的 `xcb_prop_mode_t` 枚舉中）：

- XCB_PROP_MODE_REPLACE
- XCB_PROP_MODE_PREPEND
- XCB_PROP_MODE_APPEND
