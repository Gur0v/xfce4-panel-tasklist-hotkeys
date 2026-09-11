# Xfce tasklist hotkeys

This project patches **xfce4-panel 4.20.8** to add Windows-like keyboard shortcuts to the Window Buttons tasklist.

I packaged this patch for the Arch User Repository (AUR) as [`xfce4-panel-tasklist-hotkeys`](https://aur.archlinux.org/packages/xfce4-panel-tasklist-hotkeys).

## Features

Replace `INSTANCE` with your Window Buttons plugin ID and `N` with a tasklist position.

| Shortcut | Command | Action |
|---|---|---|
| `Super+1`–`Super+9` | `xfce4-panel --plugin-event=tasklist:activate-INSTANCE:uint:N` | Open or minimize item 1–9 |
| `Super+0` | `xfce4-panel --plugin-event=tasklist:activate-INSTANCE:uint:10` | Open or minimize item 10 |
| `Super+Shift+N` | `xfce4-panel --plugin-event=tasklist:new-instance-INSTANCE:uint:N` | Start another instance of the app |
| `Super+Ctrl+N` | `xfce4-panel --plugin-event=tasklist:activate-last-INSTANCE:uint:N` | Open the last-used window in a group |
| `Super+Alt+N` | `xfce4-panel --plugin-event=tasklist:menu-INSTANCE:uint:N` | Open the window or group menu |
| `Super+T` | `xfce4-panel --plugin-event=tasklist:cycle-INSTANCE` | Select the next tasklist item |
| `Super+Shift+T` | `xfce4-panel --plugin-event=tasklist:cycle-backwards-INSTANCE` | Select the previous tasklist item |

The numbers follow the visible order in Window Buttons. A window group takes one number, and pressing its shortcut repeatedly cycles through the windows in that group.

## Install from the AUR

Using an AUR helper such as `yay`:

    yay -S xfce4-panel-tasklist-hotkeys

Restart the panel after installation:

    xfce4-panel -r

This package replaces and conflicts with the standard `xfce4-panel` package.

## Build manually

From the repository directory, run as a regular user:

    makepkg -si
    xfce4-panel -r

## Configure shortcuts

Find the Window Buttons plugin ID:

    xfconf-query -c xfce4-panel -p /plugins -lv | grep 'tasklist$'

If the output contains `/plugins/plugin-12 ... tasklist`, use `12` as `INSTANCE`.

Open **Settings Manager → Keyboard → Application Shortcuts** and add the commands from the table. For example, bind this command to `Super+1` when the plugin ID is 12:

    xfce4-panel --plugin-event=tasklist:activate-12:uint:1

## Return to the standard panel

    sudo pacman -S xfce4-panel
    xfce4-panel -r

## Notes

- Empty tasklist positions do nothing.
- Starting another app instance requires Xfce to find its executable through `/proc/PID/exe`.
- If shortcuts stop working after changing the panel layout, check whether the plugin ID changed.
- The patch targets Xfce 4.20.8 and may need changes for other versions.

## License

The packaging files use the BSD 3-Clause License. The patched Xfce source remains under its upstream licenses.
