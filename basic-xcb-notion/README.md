#基礎 XCB 概念
XCB 提供了 X 協議層的低級封裝。這個函數庫允許程序進行對 X 服務器的非常低級的訪問。自從 X 協議被標準化之後，一個客戶端可以使用任意 XCB 實現來與 X 服務器通信（當然也和 Xlib 同樣）。我們將會給出一個短暫的描述，來解釋基礎的 XCB 的概念。