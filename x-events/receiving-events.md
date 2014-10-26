接收事件：寫一個處理事件的循環
===

在我們寄存了所需的事件後，我們就需要進入一個循環來接收事件並操縱它們。這裡有兩種方法可供選擇：阻塞方法和非阻塞方法。

- `xcb_wait_for_event (xcb_connection_t *c)` 是阻塞的方法。它會在一個事件被 X 服務器排隊前一直等待（阻塞）。然後它接收這個事件，存入一個新分配的結構中（它從隊列中離隊）並返回它。這個結構會被釋放。函數會在出錯時返回 NULL 。
- `xcb_poll_for_event (xcb_connection_t *c, int *error)` 是非阻塞的方法。它監視事件隊列並返回（也會讓它離隊）一個已經存在的事件，存入一個新分配的結構中。這個結構同樣會被釋放。如果沒有事件，它會返回 NULL 。如果出錯，參數 `error` 會被設置錯誤碼。

這裡有各種方法來寫一個這樣的循環。我們推薦兩種方法來寫，並各自使用上面中的一個函數。第一個使用 `xcb_wait_for_event_t` ，它有點像 Xlib 只用 XNextEvent 時的循環：

    xcb_generic_event_t *e;

    while ((e = xcb_wait_for_event (c))) {
      switch (e->response_type & ~0x80) {
        case XCB_EXPOSE: {
          /* 操縱暴露事件類型 */
          xcb_expose_event_t *ev = (xcb_expose_event_t *)e;
        
          /* ... */
          
          break;
        }
        case XCB_BUTTON_PRESS: {
          /* 操縱按鍵按下事件類型 */
          xcb_button_press_event_t *ev = ()

          /* ... */

          break;
        }
        default: {
          /* 未知事件類型，無視它！ */
          break;
        }
        }
        /* 釋放通用事件 */
        free (e);
      }

