#開啟和關閉對 X 服務器的連接
一個 X 程序首先需要開啟一個通往 X 服務器的連接。這個函數就是用來打開一個連接的。它要求 Display 的名稱，它可以傳遞一個 NULL。通常情況下，這個 Display 的名稱會被設置為環境變量 DISPLAY 。

    xcb_connection_t *xcb_connect(const char *displayname,
                                  int        *screenp);

第二個參數會返回用戶這個連接的屏幕的號碼。它返回了一個不透明的描述 XCB 連接的結構。以下顯示了這個連接時怎麼開啟的：

    #include <xcb/xcb.h>
    int main () {
      xcb_connection_t *c;
      /* 開啟到 X 服務器的連接。使用環境變量 DISPLAY 來作為默認 Display 的名稱。*/
      c = xcb_connect (NULL, NULL);
      return 0;
    }

關閉連接，用這個函數：

    void xcb_disconnect(xcb_connection_t *c);

####和 XCB 的比較

    XOpenDisplay()      xcb_connect()
    XCloseDisplay()     xcb_disconnect()
