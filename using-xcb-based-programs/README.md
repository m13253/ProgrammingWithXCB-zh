#安裝 XCB
TODO: 這些介紹都過期了（XCB UCCU，我不翻譯了！）
#編譯基於 XCB 的程序
編譯基於 XCB 的程序要求連接 XCB 庫，這可以用 `pkg-config` 輕易完成：

    gcc -Wall prog.c -o prog `pkg-config --cflags --libs xcb`
