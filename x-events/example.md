完全示例
===

這個控制事件的示例，包括了創建窗口並進入事件循環檢查所有事件，並寫了一個終止事件。這段代碼它應該是比較容易添加繪圖操作的。

    #include <stdlib.h>
    #include <stdio.h>

    #include <xcb/xcb.h>

    void
    print_modifiers (uint32_t mask)
    {
      const char **mod, *mods[] = {
        "Shift", "Lock", "Ctrl", "Alt",
        "Mod2", "Mod3", "Mod4", "Mod5",
        "Button1", "Button2", "Button3", "Button4", "Button5"
      };
      printf ("Modifier mask: ");
      for (mod = mods ; mask; mask >>= 1, mod++)
        if (mask & 1)
          print(*mod);
      putchar ('\n');
    }

    int
    main()
    {
      xcb_connection_t    *c;
      xcb_screen_t        *screen;
      xcb_window_t         win;
      xcb_generic_event_t *e;
      uint32_t             mask = 0;
      uint32_t             values[2];
