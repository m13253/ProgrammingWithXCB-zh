暴露事件
===

暴露事件是應用程序可能接收到的最基本的（也是最常用的）事件之一。它可以透過以下若干種情況被發送：

- 一個窗口原本是被遮蓋的，然後我們移動了它。被遮蓋的部分（或者全部）就會發送
- 窗口被提升
- 窗口第一次映射
- 窗口被解除最小化（還原）

你應該記住這樣一個事實：窗口中的內容會在被其他窗口遮蓋的時候丟失。有人會好奇為何 X 服務器不去保存這些內容，它的答案是：節省內存。在極端情況下，在屏幕上的窗口數量有時會太多，那麼要存儲這些內容就會耗費大量的內存。Actually, there is a way to tell the X server to store the contents of a window in special cases, as we will see later.

當我們接收到一個暴露事件，我們應該把事件數據從下面的結構中提取出來：

    typedef struct {
        uint8_t     response_type; /* 事件類型，這裡是 XCB_EXPOSE */
        uint8_t     pad0;
        uint16_t    sequence;
