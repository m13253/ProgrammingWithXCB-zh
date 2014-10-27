#請求與回复：殺掉 Xlib 效率的殺手
為了向 X 服務器查詢信息，我們不得不去發送請求和輪詢回复。在 Xlib 上，這兩個任務是這樣被自動完成的： Xlib 先鎖定系統，然後發送一個請求，等待來自 X 服務器的回复並解鎖。這是很令人厭煩的。尤其是一個程序向 X 服務器發送了大量的請求時。的確，Xlib 不得不在詢問下一個消息之前，等待消息的結尾（因為 Xlib 發送的東西被鎖了）。舉個例子，這是使用 Xlib 的四(N=4)個請求與回覆的時間線，可以看到有五次(T_round_trip)的來回讀寫(T_read/T_write)需求時間。

    W-----RW-----RW-----RW-----R

- W: 寫入請求
- -: 等待數據
- R: 讀取回覆

    總時間為 N * (T_write + T_round_trip + T_read)

如果使用 XCB ，便能阻止這來回所需的時間並無需鎖定系統。經常發送請求， XCB 便會返回一個稱為 **cookie** 的描述符。然後我們用這個 **cookie** 的來查詢回覆， XCB 會返回一個指向回覆的指針。那麼，用 XCB ，我們可以發送大量請求，過一會就可以在需要時查詢所有的請求。這裡也是一個時間線例子：

    WWWW--RRRR

    總時間為 N * T_write + max(0, T_round_trip - (N-1) * T_write) + N * T_read。比起 Xlib 的來回來不知快哪裡去了。

這裡有個程序，計算了創建 500 個 atoms 時 Xlib 和 XCB 所用的時間。它使用了 Xlib 的方法，不正確的 XCB 方法（類似 Xlib），還有正確的 XCB 方法。在我的電腦上， XCB 比 Xlib 快 25 倍。

    #include <stdlib.h>
    #include <stdio.h>
    #include <string.h>
    #include <sys/time.h>

    #include <xcb/xcb.h>

    #include <X11/Xlib.h>

    double
    get_time(void)
    {
      struct timeval timev;

      gettimeofday(&amp;timev, NULL);

      return (double)timev.tv_sec + (((double)timev.tv_usec) / 1000000);
    }

    int
    main ()
    {
      xcb_connection_t         *c;
      xcb_atom_t               *atoms;
      xcb_intern_atom_cookie_t *cs;
      char                    **names;
      int                       count;
      int                       i;
      double                    start;
      double                    end;
      double                    diff;

      /* Xlib */
      Display *disp;
      Atom    *atoms_x;
      double   diff_x;

      c = xcb_connect (NULL, NULL);

      count = 500;
      atoms = (xcb_atom_t *)malloc (count * sizeof (atoms));
      names = (char **)malloc (count * sizeof (char *));

      /* init names */
      for (i = 0; i < count; ++i) {
        char buf[100];

        sprintf (buf, "NAME%d", i);
        names[i] = strdup (buf);
      }

      /* bad use */
      start = get_time ();

      for (i = 0; i < count; ++i)
        atoms[i] = xcb_intern_atom_reply (c,
                                          xcb_intern_atom (c,
                                                           0,
                                                           strlen(names[i]),
                                                           names[i]),
                                          NULL)->atom;

      end = get_time ();
      diff = end - start;
      printf ("bad use time  : %f\n", diff);

      /* good use */
      start = get_time ();

      cs = (xcb_intern_atom_cookie_t *) malloc (count * sizeof(xcb_intern_atom_cookie_t));
      for(i = 0; i < count; ++i)
        cs[i] = xcb_intern_atom (c, 0, strlen(names[i]), names[i]);

      for(i = 0; i < count; ++i) {
        xcb_intern_atom_reply_t *r;

        r = xcb_intern_atom_reply(c, cs[i], 0);
        if(r)
          atoms[i] = r->atom;
        free(r);
      }

      end = get_time ();
      printf ("good use time : %f\n", end - start);
      printf ("ratio         : %f\n", diff / (end - start));
      diff = end - start;

      /* free var */
      free (atoms);
      free (cs);

      xcb_disconnect (c);

      /* Xlib */
      disp = XOpenDisplay (getenv("DISPLAY"));

      atoms_x = (Atom *)malloc (count * sizeof (atoms_x));

      start = get_time ();

      for (i = 0; i < count; ++i)
        atoms_x[i] = XInternAtom(disp, names[i], 0);

      end = get_time ();
      diff_x = end - start;
      printf ("Xlib use time : %f\n", diff_x);
      printf ("ratio         : %f\n", diff_x / diff);

      free (atoms_x);
      for (i = 0; i < count; ++i)
        free (names[i]);
      free (names);

      XCloseDisplay (disp);

      return 0;
    }

