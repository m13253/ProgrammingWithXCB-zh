#X 連接
使用 XCB ，最**主要**的概念就是 **X 連接**。這是一個結構，描述了對我們給出的 X 服務器打開的一個連接。它隱藏了一條來自服務器的消息隊列，還有一條等待客戶端請求的隊列。在 XCB 裡，這個結構被稱為 `xcb_connection_t`，和 Xlib 的 Display 相似。當我們連接到 X 服務器時，這個函數庫會返回一個指向這樣的結構的指針。然後我們就可以使用任意的 XCB 函數，透過這個指針來向 X 服務器收發信息了。