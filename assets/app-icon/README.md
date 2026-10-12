# SelfPhoto Editor app icon

The canonical icon is [`selfphoto-editor.svg`](selfphoto-editor.svg), which clips the supplied
[`Icon.jpeg`](../../Asset/Icon.jpeg) to a rounded square. [`Logo.jpeg`](../../Asset/Logo.jpeg) is
the wordmark used in the repository README.

## Files

- `selfphoto-editor.svg`: canonical master.
- `photocraft-1024.png`: 1024 px render on the macOS grid.
- `photocraft.icns`: macOS bundle icon (`CFBundleIconFile`).
- `photocraft.ico`: Windows icon, 16–256 px, embedded in the `.exe` by `apps/photocraft/build.rs`.
- `hicolor/<size>/apps/ai.storyteller.photocraft.png` (16–512) and `hicolor/scalable/...svg`:
  Linux icon theme.

The app also sets the window icon and Wayland app ID at runtime (`apps/photocraft/src/app_icon.rs`,
`main.rs`). On Windows the taskbar shows the icon of the Start Menu shortcut that launches the
exe, so the MSI's `<Icon Id>` keeps the `.exe` extension (ICE50); `app_icon.rs` documents the
details and tests the `.ico` sizes and the WiX icon references.

## Regenerate

Replace `Asset/Icon.jpeg`, then run `packaging/icons.sh`. It needs `resvg`, plus `iconutil` on
macOS; the `.ico` is packed by `cargo xtask ico`.
