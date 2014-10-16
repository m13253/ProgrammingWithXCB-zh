#開啟和關閉對 X 服務器的連接
一個 X 程序首先需要開啟一個通往 X 服務器的連接。這個函數就是用來打開一個連接的。它要求 DISPLAY 的名稱，它可以傳遞一個 NULL。通常情況下，這個 DISPLAY 的名稱會被設置為環境變量的 DISPLAY 。

    xcb_connection_t *xcb_connect(const char *displayname,
                                  int        *screenp);

第二個參數會返回用戶這個連接的屏幕的號碼。它返回了一個描述 XCB 連接的結構。
