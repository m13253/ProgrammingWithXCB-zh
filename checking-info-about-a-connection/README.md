#檢查連接的基本信息
當我們已經打開了一個對 X 服務器的連接，我們應該檢查一些關於它的基本的信息：有多少屏幕，屏幕分辨率有多大，它支持多少種顏色（黑白？灰度？256 色？還是更多？）等等。我們可以從 `xcb_screen_t` 中獲得這些信息：

    typedef struct {
        xcb_window_t    root;
        xcb_colormap_t  default_colormap;
        uint32_t        white_pixel;
        uint32_t        black_pixel;
        uint32_t        current_input_masks;
