---
title: "The Impossibly Deep Bug"
date: 2022-06-12T12:08:22-05:00
description: "Chasing down a bug that only keeps on getting deeper"
type: "post"
tags: ["bug", "kde", "wayland", "micro", "software"]
---


Since the past few weeks, my muscle memory has really begun to hate me. I had switched all my systems over to Wayland because [X11 bad](https://xkcd.com/963/) and I had switched my CLI text editor from Vim to [Micro](https://micro-editor.github.io/) because constantly looking up Vim shortcuts makes me slower.

One of the things I can never remember how to do in Vim is copy and paste to the system clipboard. It's some permutation of `*"+y` right? Something like that. However, with Micro, you already know how to copy and paste (because it's literally just Ctrl+C Ctrl+V and your grandma could figure that out). Also, Vim doesn't support copying and pasting in Wayland by default unless you add some magic to your `~/.vimrc`, while Micro only requires you to install the (generally incredibly useful) [wl-clipboard](https://github.com/bugaevc/wl-clipboard) utility and it'll just work™.


## But...

Yeah, that just sounds too good to be true. Wayland has a nifty little tool called [Waypipe](https://gitlab.freedesktop.org/mstoeckl/waypipe) that's just like X11 forwarding plus [VirtualGL](https://virtualgl.org/) (by the way VirtualGL is one of those evil softwares that breaks regularly and laughs when you try to debug it). You get GPU-accelerated remote windows and it's an indispensible feature in my daily workflow. However, Micro copy paste behaves incredibly strangely in a Waypipe remote session.

First of all, when you try to copy, Waypipe spits out a [very annoying informational line](https://gitlab.freedesktop.org/mstoeckl/waypipe/-/issues/62) that screws over your nice TUI app, but you can easily comment it out in the Waypipe source and recompile.

However, there's a deeper issue: after copying, you can paste into your terminal emulator just fine, but trying to paste into anything else will just lead to pasting *nothing*. And after you close the Waypipe session, it goes back to pasting whatever was originally in your clipboard.

Even stranger, Micro claims to use wl-clipboard's `wl-copy` for copying to the clipboard, and running `wl-copy hello` in a Waypipe session works perfectly. Perfectly!


## Diving deeper

OK, that's pretty spooky, so let's take a look at the Micro source code. [internal/clipboard/clipboard.go](https://github.com/zyedidia/micro/blob/master/internal/clipboard/clipboard.go) is the relevant file, but oops, it just uses another library [zyedidia/clipboard](https://github.com/zyedidia/clipboard) for the hard lifting so let's read that library instead. [clipboard_unix.go](https://github.com/zyedidia/clipboard/blob/master/clipboard_unix.go) is the file we want in that library, and after a close inspection, we do run `wl-copy` with `os.exec`, but the text we want to copy is *piped* into `wl-copy`.

Yay, progress! Running `echo hello | wl-copy` behaves in the same spooky way as Micro, so we know the bug isn't in either Micro or zyedidia/clipboard.

So it certainly smells like the bug is in wl-clipboard, so I went ahead and filed [an issue](https://github.com/bugaevc/wl-clipboard/issues/141) there.

Here's what happened next:

### Me

When running `wl-copy` on a remote server that you've connected to using [Waypipe](https://gitlab.freedesktop.org/mstoeckl/waypipe), copying text from stdin does not work:
```bash
me@ThinkPad-X1-Yoga-Gen-6 ~> waypipe ssh exozyme  # Connect to the remote machine with Waypipe
Welcome to fish, the friendly interactive shell
Type help for instructions on how to use fish
ta180m@exozyme ~> echo hello | wl-copy  # Doesn't work
```

However, copying text if it's specified in the command does work:
```bash
me@ThinkPad-X1-Yoga-Gen-6 ~> waypipe ssh exozyme  # Connect to the remote machine with Waypipe
Welcome to fish, the friendly interactive shell
Type help for instructions on how to use fish
ta180m@exozyme ~> wl-copy hello  # Does work
```

In the first case, I'm not really sure what gets copied to the clipboard, but when I try pasting, nothing is pasted while Waypipe is still open. Then if I close Waypipe and the remote connection and try pasting, the thing originally in my clipboard is pasted. For instance:
```bash
me@ThinkPad-X1-Yoga-Gen-6 ~> wl-paste
A
me@ThinkPad-X1-Yoga-Gen-6 ~> waypipe ssh exozyme  # Connect to the remote machine with Waypipe
Welcome to fish, the friendly interactive shell
Type help for instructions on how to use fish
ta180m@exozyme ~> wl-paste
A
ta180m@exozyme ~> echo B | wl-copy  # Doesn't work
ta180m@exozyme ~> wl-paste  # This only works in the current shell, if I open up a new one and try wl-paste, it pastes A
B
ta180m@exozyme ~> exit
Connection to exozy.me closed.
me@ThinkPad-X1-Yoga-Gen-6 ~> wl-paste
A
```

Any idea what's going on here?


### Maintainer

Hmm, not sure, I'm not familiar with how waypipe works. Maybe WAYLAND_DEBUG=1 on wl-copy could clear something up?


### Me

This is the output of `echo B | WAYLAND_DEBUG=1 wl-copy` in Waypipe:
```
[4030950.284]  -> wl_display@1.get_registry(new id wl_registry@2)
[4030950.314]  -> wl_display@1.sync(new id wl_callback@3)
[4030994.575] wl_display@1.delete_id(3)
[4030994.585] wl_registry@2.global(1, "wl_compositor", 4)
[4030994.592]  -> wl_registry@2.bind(1, "wl_compositor", 2, new id [unknown]@4)
[4030994.602] wl_registry@2.global(2, "zwp_tablet_manager_v2", 1)
[4030994.608] wl_registry@2.global(3, "zwp_keyboard_shortcuts_inhibit_manager_v1", 1)
[4030994.615] wl_registry@2.global(5, "xdg_wm_base", 3)
[4030994.621]  -> wl_registry@2.bind(5, "xdg_wm_base", 1, new id [unknown]@5)
[4030994.630] wl_registry@2.global(6, "zwlr_layer_shell_v1", 3)
[4030994.636] wl_registry@2.global(7, "zxdg_decoration_manager_v1", 1)
[4030994.642] wl_registry@2.global(8, "wp_viewporter", 1)
[4030994.648] wl_registry@2.global(9, "wl_shm", 1)
[4030994.654]  -> wl_registry@2.bind(9, "wl_shm", 1, new id [unknown]@6)
[4030994.663] wl_registry@2.global(10, "wl_seat", 7)
[4030994.669]  -> wl_registry@2.bind(10, "wl_seat", 2, new id [unknown]@7)
[4030994.678] wl_registry@2.global(11, "zwp_pointer_gestures_v1", 3)
[4030994.684] wl_registry@2.global(12, "zwp_pointer_constraints_v1", 1)
[4030994.690] wl_registry@2.global(13, "zwp_relative_pointer_manager_v1", 1)
[4030994.696] wl_registry@2.global(14, "wl_data_device_manager", 3)
[4030994.702]  -> wl_registry@2.bind(14, "wl_data_device_manager", 1, new id [unknown]@8)
[4030994.711] wl_registry@2.global(15, "zwlr_data_control_manager_v1", 2)
[4030994.717]  -> wl_registry@2.bind(15, "zwlr_data_control_manager_v1", 2, new id [unknown]@9)
[4030994.725] wl_registry@2.global(16, "zwp_primary_selection_device_manager_v1", 1)
[4030994.732]  -> wl_registry@2.bind(16, "zwp_primary_selection_device_manager_v1", 1, new id [unknown]@10)
[4030994.740] wl_registry@2.global(17, "org_kde_kwin_idle", 1)
[4030994.746] wl_registry@2.global(18, "zwp_idle_inhibit_manager_v1", 1)
[4030994.752] wl_registry@2.global(19, "org_kde_plasma_shell", 6)
[4030994.759] wl_registry@2.global(20, "org_kde_kwin_appmenu_manager", 1)
[4030994.765] wl_registry@2.global(21, "org_kde_kwin_server_decoration_palette_manager", 1)
[4030994.771] wl_registry@2.global(23, "org_kde_plasma_virtual_desktop_management", 2)
[4030994.777] wl_registry@2.global(25, "org_kde_kwin_shadow_manager", 2)
[4030994.783] wl_registry@2.global(26, "org_kde_kwin_dpms_manager", 1)
[4030994.789] wl_registry@2.global(27, "org_kde_kwin_server_decoration_manager", 1)
[4030994.795] wl_registry@2.global(28, "kde_output_management_v2", 2)
[4030994.801] wl_registry@2.global(29, "kde_primary_output_v1", 1)
[4030994.807] wl_registry@2.global(30, "zxdg_output_manager_v1", 3)
[4030994.813] wl_registry@2.global(31, "wl_subcompositor", 1)
[4030994.819] wl_registry@2.global(32, "zxdg_exporter_v2", 1)
[4030994.826] wl_registry@2.global(33, "zxdg_importer_v2", 1)
[4030994.832] wl_registry@2.global(36, "xdg_activation_v1", 1)
[4030994.838] wl_registry@2.global(37, "wp_drm_lease_device_v1", 1)
[4030994.844] wl_registry@2.global(40, "wl_drm", 2)
[4030994.850] wl_registry@2.global(41, "zwp_linux_dmabuf_v1", 3)
[4030994.856] wl_registry@2.global(42, "kde_output_device_v2", 2)
[4030994.862] wl_registry@2.global(44, "zwp_text_input_manager_v2", 1)
[4030994.868] wl_registry@2.global(45, "zwp_text_input_manager_v3", 1)
[4030994.874] wl_registry@2.global(46, "org_kde_kwin_contrast_manager", 2)
[4030994.880] wl_registry@2.global(47, "org_kde_kwin_blur_manager", 1)
[4030994.886] wl_registry@2.global(48, "org_kde_kwin_slide_manager", 1)
[4030994.892] wl_registry@2.global(49, "kde_output_device_v2", 2)
[4030994.898] wl_registry@2.global(50, "wl_output", 3)
[4030994.905] wl_callback@3.done(1256)
[4030994.908]  -> wl_display@1.sync(new id wl_callback@3)
[4030998.044] wl_display@1.delete_id(3)
[4030998.051] wl_seat@7.capabilities(7)
[4030998.055] wl_seat@7.name("")
[4030998.059] wl_callback@3.done(1256)
[4030998.064]  -> zwlr_data_control_manager_v1@9.get_data_device(new id zwlr_data_control_device_v1@3, wl_seat@7)
[4031003.612]  -> zwlr_data_control_manager_v1@9.create_data_source(new id zwlr_data_control_source_v1@11)
[4031003.627]  -> zwlr_data_control_source_v1@11.offer("¢fV")
[4031003.630]  -> zwlr_data_control_device_v1@3.set_selection(zwlr_data_control_source_v1@11)
[4031003.633]  -> wl_display@1.sync(new id wl_callback@12)
[4031006.384] wl_display@1.delete_id(12)
[4031006.390] zwlr_data_control_device_v1@3.data_offer(new id zwlr_data_control_offer_v1@4278190080)
[4031006.395] zwlr_data_control_offer_v1@4278190080.offer("text/plain")
[4031006.399] zwlr_data_control_offer_v1@4278190080.offer("text/plain")
[4031006.403] zwlr_data_control_offer_v1@4278190080.offer("text/plain;charset=utf-8")
[4031006.407] zwlr_data_control_offer_v1@4278190080.offer("TEXT")
[4031006.411] zwlr_data_control_offer_v1@4278190080.offer("STRING")
[4031006.414] zwlr_data_control_offer_v1@4278190080.offer("UTF8_STRING")
[4031006.418] zwlr_data_control_device_v1@3.selection(zwlr_data_control_offer_v1@4278190080)
[4031006.422] zwlr_data_control_device_v1@3.data_offer(new id zwlr_data_control_offer_v1@4278190081)
[4031006.427] zwlr_data_control_offer_v1@4278190081.offer("text/plain")
[4031006.431] zwlr_data_control_offer_v1@4278190081.offer("text/html")
[4031006.435] zwlr_data_control_offer_v1@4278190081.offer("text/plain;charset=utf-8")
[4031006.439] zwlr_data_control_device_v1@3.primary_selection(zwlr_data_control_offer_v1@4278190081)
[4031006.442] zwlr_data_control_device_v1@3.data_offer(new id zwlr_data_control_offer_v1@4278190082)
[4031006.447] zwlr_data_control_offer_v1@4278190082.offer("ï¿œï¿œfV")
[4031006.451] zwlr_data_control_device_v1@3.selection(zwlr_data_control_offer_v1@4278190082)
[4031006.455] wl_callback@12.done(1256)
[4035132.398] zwlr_data_control_source_v1@11.cancelled()
```

And this is the output of `WAYLAND_DEBUG=1 wl-copy B` in Waypipe:
```
[4158623.296]  -> wl_display@1.get_registry(new id wl_registry@2)
[4158623.319]  -> wl_display@1.sync(new id wl_callback@3)
[4158669.862] wl_display@1.delete_id(3)
[4158669.875] wl_registry@2.global(1, "wl_compositor", 4)
[4158669.883]  -> wl_registry@2.bind(1, "wl_compositor", 2, new id [unknown]@4)
[4158669.893] wl_registry@2.global(2, "zwp_tablet_manager_v2", 1)
[4158669.899] wl_registry@2.global(3, "zwp_keyboard_shortcuts_inhibit_manager_v1", 1)
[4158669.905] wl_registry@2.global(5, "xdg_wm_base", 3)
[4158669.912]  -> wl_registry@2.bind(5, "xdg_wm_base", 1, new id [unknown]@5)
[4158669.920] wl_registry@2.global(6, "zwlr_layer_shell_v1", 3)
[4158669.926] wl_registry@2.global(7, "zxdg_decoration_manager_v1", 1)
[4158669.933] wl_registry@2.global(8, "wp_viewporter", 1)
[4158669.939] wl_registry@2.global(9, "wl_shm", 1)
[4158669.945]  -> wl_registry@2.bind(9, "wl_shm", 1, new id [unknown]@6)
[4158669.953] wl_registry@2.global(10, "wl_seat", 7)
[4158669.959]  -> wl_registry@2.bind(10, "wl_seat", 2, new id [unknown]@7)
[4158669.968] wl_registry@2.global(11, "zwp_pointer_gestures_v1", 3)
[4158669.974] wl_registry@2.global(12, "zwp_pointer_constraints_v1", 1)
[4158669.980] wl_registry@2.global(13, "zwp_relative_pointer_manager_v1", 1)
[4158669.986] wl_registry@2.global(14, "wl_data_device_manager", 3)
[4158669.992]  -> wl_registry@2.bind(14, "wl_data_device_manager", 1, new id [unknown]@8)
[4158670.001] wl_registry@2.global(15, "zwlr_data_control_manager_v1", 2)
[4158670.009]  -> wl_registry@2.bind(15, "zwlr_data_control_manager_v1", 2, new id [unknown]@9)
[4158670.018] wl_registry@2.global(16, "zwp_primary_selection_device_manager_v1", 1)
[4158670.024]  -> wl_registry@2.bind(16, "zwp_primary_selection_device_manager_v1", 1, new id [unknown]@10)
[4158670.033] wl_registry@2.global(17, "org_kde_kwin_idle", 1)
[4158670.039] wl_registry@2.global(18, "zwp_idle_inhibit_manager_v1", 1)
[4158670.045] wl_registry@2.global(19, "org_kde_plasma_shell", 6)
[4158670.051] wl_registry@2.global(20, "org_kde_kwin_appmenu_manager", 1)
[4158670.057] wl_registry@2.global(21, "org_kde_kwin_server_decoration_palette_manager", 1)
[4158670.063] wl_registry@2.global(23, "org_kde_plasma_virtual_desktop_management", 2)
[4158670.069] wl_registry@2.global(25, "org_kde_kwin_shadow_manager", 2)
[4158670.075] wl_registry@2.global(26, "org_kde_kwin_dpms_manager", 1)
[4158670.081] wl_registry@2.global(27, "org_kde_kwin_server_decoration_manager", 1)
[4158670.088] wl_registry@2.global(28, "kde_output_management_v2", 2)
[4158670.094] wl_registry@2.global(29, "kde_primary_output_v1", 1)
[4158670.100] wl_registry@2.global(30, "zxdg_output_manager_v1", 3)
[4158670.106] wl_registry@2.global(31, "wl_subcompositor", 1)
[4158670.112] wl_registry@2.global(32, "zxdg_exporter_v2", 1)
[4158670.118] wl_registry@2.global(33, "zxdg_importer_v2", 1)
[4158670.124] wl_registry@2.global(36, "xdg_activation_v1", 1)
[4158670.130] wl_registry@2.global(37, "wp_drm_lease_device_v1", 1)
[4158670.136] wl_registry@2.global(40, "wl_drm", 2)
[4158670.142] wl_registry@2.global(41, "zwp_linux_dmabuf_v1", 3)
[4158670.148] wl_registry@2.global(42, "kde_output_device_v2", 2)
[4158670.154] wl_registry@2.global(44, "zwp_text_input_manager_v2", 1)
[4158670.160] wl_registry@2.global(45, "zwp_text_input_manager_v3", 1)
[4158670.166] wl_registry@2.global(46, "org_kde_kwin_contrast_manager", 2)
[4158670.172] wl_registry@2.global(47, "org_kde_kwin_blur_manager", 1)
[4158670.178] wl_registry@2.global(48, "org_kde_kwin_slide_manager", 1)
[4158670.185] wl_registry@2.global(49, "kde_output_device_v2", 2)
[4158670.191] wl_registry@2.global(50, "wl_output", 3)
[4158670.197] wl_callback@3.done(2133)
[4158670.200]  -> wl_display@1.sync(new id wl_callback@3)
[4158671.921] wl_display@1.delete_id(3)
[4158671.925] wl_seat@7.capabilities(7)
[4158671.928] wl_seat@7.name("")
[4158671.932] wl_callback@3.done(2133)
[4158671.935]  -> zwlr_data_control_manager_v1@9.get_data_device(new id zwlr_data_control_device_v1@3, wl_seat@7)
[4158671.942]  -> zwlr_data_control_manager_v1@9.create_data_source(new id zwlr_data_control_source_v1@11)
[4158671.952]  -> zwlr_data_control_source_v1@11.offer("text/plain")
[4158671.955]  -> zwlr_data_control_source_v1@11.offer("text/plain;charset=utf-8")
[4158671.958]  -> zwlr_data_control_source_v1@11.offer("TEXT")
[4158671.961]  -> zwlr_data_control_source_v1@11.offer("STRING")
[4158671.964]  -> zwlr_data_control_source_v1@11.offer("UTF8_STRING")
[4158671.967]  -> zwlr_data_control_device_v1@3.set_selection(zwlr_data_control_source_v1@11)
[4158671.971]  -> wl_display@1.sync(new id wl_callback@12)
[4158673.699] wl_display@1.delete_id(12)
[4158673.702] zwlr_data_control_device_v1@3.data_offer(new id zwlr_data_control_offer_v1@4278190080)
[4158673.706] zwlr_data_control_offer_v1@4278190080.offer("UTF8_STRING")
[4158673.710] zwlr_data_control_offer_v1@4278190080.offer("COMPOUND_TEXT")
[4158673.713] zwlr_data_control_offer_v1@4278190080.offer("TEXT")
[4158673.715] zwlr_data_control_offer_v1@4278190080.offer("STRING")
[4158673.718] zwlr_data_control_offer_v1@4278190080.offer("text/plain;charset=utf-8")
[4158673.721] zwlr_data_control_offer_v1@4278190080.offer("text/plain")
[4158673.724] zwlr_data_control_offer_v1@4278190080.offer("SAVE_TARGETS")
[4158673.727] zwlr_data_control_device_v1@3.selection(zwlr_data_control_offer_v1@4278190080)
[4158673.730] zwlr_data_control_device_v1@3.data_offer(new id zwlr_data_control_offer_v1@4278190081)
[4158673.734] zwlr_data_control_offer_v1@4278190081.offer("UTF8_STRING")
[4158673.737] zwlr_data_control_offer_v1@4278190081.offer("COMPOUND_TEXT")
[4158673.740] zwlr_data_control_offer_v1@4278190081.offer("TEXT")
[4158673.743] zwlr_data_control_offer_v1@4278190081.offer("STRING")
[4158673.746] zwlr_data_control_offer_v1@4278190081.offer("text/plain;charset=utf-8")
[4158673.749] zwlr_data_control_offer_v1@4278190081.offer("text/plain")
[4158673.752] zwlr_data_control_device_v1@3.primary_selection(zwlr_data_control_offer_v1@4278190081)
[4158673.755] zwlr_data_control_device_v1@3.data_offer(new id zwlr_data_control_offer_v1@4278190082)
[4158673.758] zwlr_data_control_offer_v1@4278190082.offer("text/plain")
[4158673.761] zwlr_data_control_offer_v1@4278190082.offer("text/plain;charset=utf-8")
[4158673.764] zwlr_data_control_offer_v1@4278190082.offer("TEXT")
[4158673.767] zwlr_data_control_offer_v1@4278190082.offer("STRING")
[4158673.770] zwlr_data_control_offer_v1@4278190082.offer("UTF8_STRING")
[4158673.773] zwlr_data_control_device_v1@3.selection(zwlr_data_control_offer_v1@4278190082)
[4158673.776] wl_callback@12.done(2133)
[4158674.051] zwlr_data_control_source_v1@11.send("text/plain;charset=utf-8", fd 4)
[4158675.799] zwlr_data_control_source_v1@11.send("text/plain;charset=utf-8", fd 4)
[4165362.697] zwlr_data_control_source_v1@11.cancelled()
```

And this is the output of `echo B | WAYLAND_DEBUG=1 wl-copy` in a local shell:
```
[4021884.136]  -> wl_display@1.get_registry(new id wl_registry@2)
[4021884.184]  -> wl_display@1.sync(new id wl_callback@3)
[4021886.871] wl_display@1.delete_id(3)
[4021886.899] wl_registry@2.global(1, "wl_compositor", 4)
[4021886.937]  -> wl_registry@2.bind(1, "wl_compositor", 2, new id [unknown]@4)
[4021886.984] wl_registry@2.global(2, "zwp_tablet_manager_v2", 1)
[4021887.017] wl_registry@2.global(3, "zwp_keyboard_shortcuts_inhibit_manager_v1", 1)
[4021887.050] wl_registry@2.global(5, "xdg_wm_base", 3)
[4021887.080]  -> wl_registry@2.bind(5, "xdg_wm_base", 1, new id [unknown]@5)
[4021887.124] wl_registry@2.global(6, "zwlr_layer_shell_v1", 3)
[4021887.155] wl_registry@2.global(7, "zxdg_decoration_manager_v1", 1)
[4021887.185] wl_registry@2.global(8, "wp_viewporter", 1)
[4021887.216] wl_registry@2.global(9, "wl_shm", 1)
[4021887.247]  -> wl_registry@2.bind(9, "wl_shm", 1, new id [unknown]@6)
[4021887.290] wl_registry@2.global(10, "wl_seat", 7)
[4021887.321]  -> wl_registry@2.bind(10, "wl_seat", 2, new id [unknown]@7)
[4021887.365] wl_registry@2.global(11, "zwp_pointer_gestures_v1", 3)
[4021887.397] wl_registry@2.global(12, "zwp_pointer_constraints_v1", 1)
[4021887.427] wl_registry@2.global(13, "zwp_relative_pointer_manager_v1", 1)
[4021887.458] wl_registry@2.global(14, "wl_data_device_manager", 3)
[4021887.490]  -> wl_registry@2.bind(14, "wl_data_device_manager", 1, new id [unknown]@8)
[4021887.535] wl_registry@2.global(15, "zwlr_data_control_manager_v1", 2)
[4021887.567]  -> wl_registry@2.bind(15, "zwlr_data_control_manager_v1", 2, new id [unknown]@9)
[4021887.611] wl_registry@2.global(16, "zwp_primary_selection_device_manager_v1", 1)
[4021887.642]  -> wl_registry@2.bind(16, "zwp_primary_selection_device_manager_v1", 1, new id [unknown]@10)
[4021887.688] wl_registry@2.global(17, "org_kde_kwin_idle", 1)
[4021887.720] wl_registry@2.global(18, "zwp_idle_inhibit_manager_v1", 1)
[4021887.752] wl_registry@2.global(19, "org_kde_plasma_shell", 6)
[4021887.785] wl_registry@2.global(20, "org_kde_kwin_appmenu_manager", 1)
[4021887.821] wl_registry@2.global(21, "org_kde_kwin_server_decoration_palette_manager", 1)
[4021887.853] wl_registry@2.global(23, "org_kde_plasma_virtual_desktop_management", 2)
[4021887.883] wl_registry@2.global(25, "org_kde_kwin_shadow_manager", 2)
[4021887.915] wl_registry@2.global(26, "org_kde_kwin_dpms_manager", 1)
[4021887.945] wl_registry@2.global(27, "org_kde_kwin_server_decoration_manager", 1)
[4021887.978] wl_registry@2.global(28, "kde_output_management_v2", 2)
[4021888.009] wl_registry@2.global(29, "kde_primary_output_v1", 1)
[4021888.043] wl_registry@2.global(30, "zxdg_output_manager_v1", 3)
[4021888.075] wl_registry@2.global(31, "wl_subcompositor", 1)
[4021888.107] wl_registry@2.global(32, "zxdg_exporter_v2", 1)
[4021888.139] wl_registry@2.global(33, "zxdg_importer_v2", 1)
[4021888.170] wl_registry@2.global(36, "xdg_activation_v1", 1)
[4021888.202] wl_registry@2.global(37, "wp_drm_lease_device_v1", 1)
[4021888.234] wl_registry@2.global(40, "wl_drm", 2)
[4021888.266] wl_registry@2.global(41, "zwp_linux_dmabuf_v1", 4)
[4021888.297] wl_registry@2.global(42, "kde_output_device_v2", 2)
[4021888.328] wl_registry@2.global(44, "zwp_text_input_manager_v2", 1)
[4021888.359] wl_registry@2.global(45, "zwp_text_input_manager_v3", 1)
[4021888.391] wl_registry@2.global(46, "org_kde_kwin_contrast_manager", 2)
[4021888.424] wl_registry@2.global(47, "org_kde_kwin_blur_manager", 1)
[4021888.455] wl_registry@2.global(48, "org_kde_kwin_slide_manager", 1)
[4021888.487] wl_registry@2.global(49, "kde_output_device_v2", 2)
[4021888.519] wl_registry@2.global(50, "wl_output", 3)
[4021888.552] wl_callback@3.done(1215)
[4021888.567]  -> wl_display@1.sync(new id wl_callback@3)
[4021888.657] wl_display@1.delete_id(3)
[4021888.673] wl_seat@7.capabilities(7)
[4021888.687] wl_seat@7.name("")
[4021888.704] wl_callback@3.done(1215)
[4021888.718]  -> zwlr_data_control_manager_v1@9.get_data_device(new id zwlr_data_control_device_v1@3, wl_seat@7)
[4021898.743]  -> zwlr_data_control_manager_v1@9.create_data_source(new id zwlr_data_control_source_v1@11)
[4021898.807]  -> zwlr_data_control_source_v1@11.offer("text/plain")
[4021898.819]  -> zwlr_data_control_source_v1@11.offer("text/plain")
[4021898.833]  -> zwlr_data_control_source_v1@11.offer("text/plain;charset=utf-8")
[4021898.847]  -> zwlr_data_control_source_v1@11.offer("TEXT")
[4021898.861]  -> zwlr_data_control_source_v1@11.offer("STRING")
[4021898.875]  -> zwlr_data_control_source_v1@11.offer("UTF8_STRING")
[4021898.893]  -> zwlr_data_control_device_v1@3.set_selection(zwlr_data_control_source_v1@11)
[4021898.909]  -> wl_display@1.sync(new id wl_callback@12)
[4021899.054] wl_display@1.delete_id(12)
[4021899.082] zwlr_data_control_device_v1@3.data_offer(new id zwlr_data_control_offer_v1@4278190080)
[4021899.098] zwlr_data_control_offer_v1@4278190080.offer("text/plain")
[4021899.112] zwlr_data_control_offer_v1@4278190080.offer("text/plain")
[4021899.124] zwlr_data_control_offer_v1@4278190080.offer("text/plain;charset=utf-8")
[4021899.137] zwlr_data_control_offer_v1@4278190080.offer("TEXT")
[4021899.150] zwlr_data_control_offer_v1@4278190080.offer("STRING")
[4021899.164] zwlr_data_control_offer_v1@4278190080.offer("UTF8_STRING")
[4021899.176] zwlr_data_control_device_v1@3.selection(zwlr_data_control_offer_v1@4278190080)
[4021899.222] zwlr_data_control_device_v1@3.data_offer(new id zwlr_data_control_offer_v1@4278190081)
[4021899.248] zwlr_data_control_offer_v1@4278190081.offer("text/plain")
[4021899.260] zwlr_data_control_offer_v1@4278190081.offer("text/html")
[4021899.273] zwlr_data_control_offer_v1@4278190081.offer("text/plain;charset=utf-8")
[4021899.286] zwlr_data_control_device_v1@3.primary_selection(zwlr_data_control_offer_v1@4278190081)
[4021899.300] zwlr_data_control_device_v1@3.data_offer(new id zwlr_data_control_offer_v1@4278190082)
[4021899.318] zwlr_data_control_offer_v1@4278190082.offer("text/plain")
[4021899.331] zwlr_data_control_offer_v1@4278190082.offer("text/plain")
[4021899.343] zwlr_data_control_offer_v1@4278190082.offer("text/plain;charset=utf-8")
[4021899.355] zwlr_data_control_offer_v1@4278190082.offer("TEXT")
[4021899.367] zwlr_data_control_offer_v1@4278190082.offer("STRING")
[4021899.380] zwlr_data_control_offer_v1@4278190082.offer("UTF8_STRING")
[4021899.392] zwlr_data_control_device_v1@3.selection(zwlr_data_control_offer_v1@4278190082)
[4021899.408] wl_callback@12.done(1215)
[4021899.575] zwlr_data_control_source_v1@11.send("text/plain;charset=utf-8", fd 8)
[4021900.191] zwlr_data_control_source_v1@11.send("text/plain;charset=utf-8", fd 8)
[4030969.780] zwlr_data_control_source_v1@11.cancelled()
```

In the first case, `wl-copy` seems to be copying control characters, which is why pasting doesn't result in anything being pasted.


### Maintainer

That looks rather strange, especially considering it's the MIME type that gets weird characters, not the contents...


### Me

Manually specifying the MIME type with `echo B | WAYLAND_DEBUG=1 wl-copy -t text/plain-text` works in Waypipe. How is the MIME type normally inferred?


### Me

I think I made more progress: wl-clipboard uses `xdg-mime query filetype` for inferring the MIME type right? If so, `xdg-mime query filetype somefile` works in a local shell but **does not output anything at all** in a Waypipe remote session.


## Aha!

So wl-clipboard is actually an innocent victim too, just like Micro and the clipboard library! `xdg-mime` is the real culprit here, so maybe time to file an issue for them?

Not so fast, let's take a glance at the `xdg-mime` source code. Here's the relevant lines:
```bash
info_kde()
{
    if [ -n "${KDE_SESSION_VERSION}" ]; then
      case "${KDE_SESSION_VERSION}" in
        4)
          DEBUG 1 "Running kmimetypefinder \"$1\""
          kmimetypefinder "$1" 2>/dev/null | head -n 1
        ;;
        5)
          DEBUG 1 "Running kmimetypefinder${KDE_SESSION_VERSION} \"$1\""
          kmimetypefinder${KDE_SESSION_VERSION} "$1" 2>/dev/null | head -n 1
        ;;
      esac
    else
        DEBUG 1 "Running kfile \"$1\""
        kfile "$1" 2> /dev/null | head -n 1 | cut -d "(" -f 2 | cut -d ")" -f 1
    fi

    if [ $? -eq 0 ]; then
        exit_success
    else
        exit_failure_operation_failed
    fi
}
```

`xdg-mime` thinks I'm using KDE 4 because this is a remote session with no environment variables set! Well, actually, I do have one variable set: `XDG_CURRENT_DESKTOP=KDE` in my `~/.ssh/environment` so apps run over Waypipe know to use KDE theming. `xdg-mime` thinks I'm an old-timer still on KDE 4 and uses the KDE 4 `kfile` command to detect the MIME type. But since I'm running hemorrhaging-edge KDE 5.soon-to-be-25.0 on Arch, that `kfile` command doesn't exist and `xdg-mime` interally errors and outputs nothing. Setting `KDE_SESSION_VERSION=5` in `~/.ssh/environment` makes copying text from stdin work in Waypipe! Case closed, 🎉

I'll end with a TL;DR, since I copied this verbatim from that wl-clipboard issue and I don't want to write an actual ending:

The funny thing is that the whole reason I went down this rabbit hole of chasing this bug was because copying text didn't work in Micro, so I read the Micro source code, then read the `zyedidia/clipboard` library code that Micro was using, then the `wl-clipboard` source code since that's what `zyedidia/clipboard` internally uses, then read the `xdg-mime` source code. (The magic of open source!) Turns out the bug was *much* deeper than I originally expected, and nothing's wrong with wl-clipboard or Waypipe. 😃
