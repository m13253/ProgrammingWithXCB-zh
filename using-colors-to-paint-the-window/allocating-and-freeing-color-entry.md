分配和釋放顏色記錄
===

當我們可訪問一些顏色映射時，我們就能分配顏色了。顏色信息被存儲在這樣的結構中：

    typedef struct {
        uint8_t  response_type;
        uint8_t  pad0;
