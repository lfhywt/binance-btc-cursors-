# binance-btc-cursors

A black-and-gold Windows mouse cursor theme that fuses Binance's diamond geometry with Bitcoin's ₿ mark — 48 cursors, 5 size sets, one-click installer, zero admin rights.

[中文完整说明](使用说明.txt)

![preview](https://github.com/user-attachments/assets/5f92a0f1-45b0-47e9-8cf3-f6dea22736dc)

> Unofficial fan artwork. Not affiliated with, endorsed by, or sponsored by Binance, Bitcoin, or any related organisation.

## What's inside

- **48 static cursors**: 17 standard Windows states, 1 Bitcoin alternate arrow, 30 extended states
- **5 size sets**: 32, 48, 64, 96 and 256 px — 256 being the hard ceiling of both the CUR format and Windows
- **Multi-resolution set** in `cursors/` so Windows can pick the frame itself
- Everything is per-user (`HKCU` + `%LOCALAPPDATA%`), nothing needs elevation

## Install

1. Download or clone the repository, and keep the folder intact (the scripts copy files out of `sizes/`).
2. Double-click one of:

| Script | Effect |
| --- | --- |
| `install.cmd` | multi-size assets; your current pointer size is left alone |
| `install-32px.cmd` … `install-96px.cmd` | single-size assets **and** set the Windows pointer size to match |
| `install-256px.cmd` | the largest set Windows supports (2.67× the 96px art) |

3. Cursors apply immediately. Reopen any app that still shows the old pointer.

The installer validates every CUR through the Windows cursor API before touching anything, writes the 17 system pointer paths, reads them back and reports `17/17`. If anything fails it rolls the registry and the pointer size back automatically.

### Why the size scripts change your pointer size

Windows renders *every* cursor scaled to `HKCU\Control Panel\Cursors\CursorBaseSize` (Settings → Accessibility → Mouse pointer size), so swapping only the `.cur` files looks identical at all five sizes. The installer therefore calls the undocumented `SystemParametersInfo(SPI_SETCURSORSIZE = 0x2029)`, which applies a new base size immediately; `SPI_SETCURSORS (0x0057)` alone only reloads the files. On builds that reject the call, it falls back to a registry write plus a "sign out to apply" notice.

## Restore

Double-click `restore.cmd`. Each run rolls back exactly one install layer and also restores the pointer size that was active before that install; the console prints how many older layers are still available.

Installs and backups live in `%LOCALAPPDATA%\BinanceBitcoinCursors\installs\<timestamp>_<id>\`, and each install folder keeps its own copy of `restore.cmd`, so you can restore from there too.

## Verify without changing anything

```
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\install.ps1 -ValidateOnly
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\install.ps1 -Size 256 -ValidateOnly
```

This checks that every CUR loads through the Windows cursor API and — for fixed sizes — that each file carries exactly one frame of the expected size with a valid hotspot.

## Layout

```
cursors/             multi-resolution CURs used by install.cmd
sizes/32px … 256px   48 single-frame CURs per size
source/              original sprite atlases and generation notes
install.cmd          install.ps1 / cursor-tools.ps1
restore.cmd          restore.ps1
validation.txt       per-file frame sizes and hotspots
使用说明.txt           full documentation (Chinese)
```

## Notes

- **256px is upscaled** from the 96px set with high-quality bicubic resampling — the artwork tops out at 96px native, so edges are slightly softer. A 288px (3× 96px) set is not representable: CUR stores width/height in single bytes (0 meaning 256) and Windows caps the pointer size slider at 256.
- **Hotspots** are preserved proportionally across every size.
- **Extended cursors ship as assets only.** Windows has no global mapping for drag-copy, grab/grabbing, zoom or scroll-arrow states, so those have to be chosen manually in Mouse Properties or used by apps that accept custom cursors.
- Tested on Windows 11 25H2 (26200) at 100% display scaling; the Windows 10 path is the same API surface.
