打開字體
===
我們用這個函數來打開一個字體：

    xcb_void_cookie_t xcb_open_font (xcb_connection_t *c,
                                     xcb_font_t        fid,
                                     uint16_t          name_len,
                                     const char       *name);

`fid` 參數是 `xcb_generate_id()` 所定義的字體的 Id ，`name` 參數是欲打開的字體的名稱。在終端中使用命令 `xlsfonts` 可以得知電腦中的有效字體。`name_len` 是字體名稱的長度（由 `strlen()` 給出）。
