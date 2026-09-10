# Xfce Window Buttons tasklist hotkeys

Patch project for **xfce4-panel 4.20.8**. It adds Windows 10-like keyboard control to Window Buttons while using the plugin's own filtered, grouped, and ordered tasklist as source of truth.

## Implemented behavior

Replace `INSTANCE` below with one Window Buttons plugin ID.

| Windows shortcut | Xfce command | Behavior |
|---|---|---|
| `Super+1` … `Super+9` | `xfce4-panel --plugin-event=tasklist:activate-INSTANCE:uint:N` | Activate visible slot 1–9; minimize it when already active |
| `Super+0` | `xfce4-panel --plugin-event=tasklist:activate-INSTANCE:uint:10` | Activate visible slot 10; minimize it when already active |
| `Super+Shift+N` | `xfce4-panel --plugin-event=tasklist:new-instance-INSTANCE:uint:N` | Start another instance when Window Buttons can resolve `/proc/PID/exe` |
| `Super+Ctrl+N` | `xfce4-panel --plugin-event=tasklist:activate-last-INSTANCE:uint:N` | Activate group's most recently focused window |
| `Super+Alt+N` | `xfce4-panel --plugin-event=tasklist:menu-INSTANCE:uint:N` | Open Xfce's native window/group action menu |
| `Super+T` | `xfce4-panel --plugin-event=tasklist:cycle-INSTANCE` | Activate next visible tasklist entry |
| `Super+Shift+T` | `xfce4-panel --plugin-event=tasklist:cycle-backwards-INSTANCE` | Activate previous visible tasklist entry |

Normal numbered activation chooses a window directly. For a group, first invocation selects its most recently focused visible window; invoking the same active group repeatedly advances through that group's ordered visible windows. A single visible group consumes one number.

Missing positions do nothing. Events are accepted only by the explicitly named instance, so multiple Window Buttons plugins do not all act.

## Numbering and visible order

Numbering follows top-level children in `XfceTasklist::windows` after Window Buttons applies its normal sorting or drag-and-drop order and visibility rules. It counts visible `CHILD_TYPE_WINDOW` and `CHILD_TYPE_GROUP` buttons only. Group-menu children and overflow-only children do not consume hidden shortcut slots.

This preserves tasklist decisions for workspaces, monitors, minimized-only mode, urgency visibility, skipped windows, grouping, and configured sorting. Numbering means current Window Buttons entries—not Windows pinned applications. Xfce Window Buttons has no pinned-launcher model.

## Identify plugin instance

Run:

    xfconf-query -c xfce4-panel -p /plugins -lv | grep 'tasklist$'

Typical output contains `/plugins/plugin-12 ... tasklist`; use `12` as `INSTANCE`. Panel Preferences also shows item IDs in its Items tab on supported versions.

## Configure shortcuts

Open **Settings Manager → Keyboard → Application Shortcuts**. Add commands from the table. For each digit, replace `N` with 1–10 while keeping your fixed `INSTANCE`.

Example for instance 12 and position 1:

    xfce4-panel --plugin-event=tasklist:activate-12:uint:1

Bind it to `Super+1`. Bind position 10 command to `Super+0`. Repeat with `new-instance`, `activate-last`, and `menu` only for modifiers wanted. No helper modifies xfconf automatically.

## Build and install on Arch/Artix

Build as regular user from repository root:

    makepkg -s

Install produced package:

    sudo pacman -U ./xfce4-panel-tasklist-hotkeys-4.20.8-1-x86_64.pkg.tar.zst

Restart panel after installation:

    xfce4-panel -r

Package conflicts with stock `xfce4-panel` because both install the same files. Versioned `provides=("xfce4-panel=4.20.8")` keeps dependencies on `xfce4-panel` satisfied. Pacman prompts to remove stock package during installation. No systemd dependency is added.

## Revert to stock package

Refresh package databases and replace patched package with repository package:

    sudo pacman -Syy xfce4-panel

Confirm removal of `xfce4-panel-tasklist-hotkeys` when Pacman asks, then restart panel with `xfce4-panel -r`.

## Known differences from Windows 10

- Positions represent currently visible Window Buttons entries, not pinned apps.
- No app is launched for an absent slot because tasklist has no pinned launcher metadata.
- `Super+Shift+N` reuses existing native Window Buttons logic. Launch succeeds only when `/proc/PID/exe` is available and executable invocation is sufficient; command-line arguments and desktop-file launch semantics are unavailable.
- `Super+Alt+N` opens Xfce window/group actions, not a Windows Jump List. Xfce has no Jump List subsystem.
- `Super+T` activates entries immediately. Windows gives taskbar keyboard focus and allows arrow/Enter interaction; Window Buttons has no equivalent focus mode.
- Group cycling approximates Windows behavior using tasklist group order and tracked `last_focused` timestamps.

## Troubleshooting

- Command does nothing: verify plugin ID and visible slot count. Use decimal unsigned values such as `uint:10`.
- Several Window Buttons instances exist: each binding must use intended instance ID in event name.
- New instance does nothing: selected process likely has no usable `/proc/PID/exe`; this is intentional rather than guessing a launcher.
- Order seems unexpected: inspect Window Buttons sorting, grouping, workspace, monitor, and minimized-only settings. Keyboard numbering mirrors those decisions.
- After package upgrade, restart panel using `xfce4-panel -r`.

## Updating and rebasing

For a future stable release:

1. Change `pkgver` and tarball SHA-256 in `PKGBUILD`.
2. Extract that exact official release tarball.
3. Apply or rebase `patches/0001-window-buttons-windows-taskbar-hotkeys.patch` against it.
4. Regenerate patch SHA-256 and `.SRCINFO`, then run `makepkg -s` and tests.

Keep package tied to an explicit Xfce release. Refresh `0001-window-buttons-windows-taskbar-hotkeys.patch` at repository root and do not switch source to Git HEAD.

## Validation scope

The patched 4.20.8 source was configured and compiled with upstream Autotools build files. Build completed with zero compiler warnings/errors caused by patch. Live panel replacement and interactive shortcut/window tests were not performed because validation permission was build/package only.

## Licensing

Repository metadata and documentation use the root BSD-3-Clause `LICENSE`. Patch modifies GPL/LGPL-covered xfce4-panel source; resulting modified source and binaries remain under applicable upstream licenses. See upstream `COPYING` and `COPYING.LIB` files in release archive.
