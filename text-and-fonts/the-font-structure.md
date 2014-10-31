字體結構
===

為了支持 flexible 字體，字體類型被定義了。你知道嗎？它也是一個 Id ：

    typedef uint32_t xcb_font_t;

它用來包含關於一個字體的信息，並且傳遞若干函數來操縱字體選擇和文字描繪。我們使用這個函數向 X 服務器請求一個 Id 給我們字體：

    xcb_font_t xcb_generate_id (xcb_connection *c);
