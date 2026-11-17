# NACHTFAHRT + FM-1 Big Screen

[NACHTFAHRT](https://github.com/FartyPants/fm1-nachtfahrt) by Cranky Man (FartyPants), a generative 1970s Berlin
School instrument for the M-VAVE FM-1, with one addition: while an app asks for it, the FM-1 sends its screen over its
USB cable, so [FM-1 Mirror](https://github.com/pfkellogg/fm1-mirror) (Mac, Windows, Linux) and
[FM-1 Mirror + Spyglass](https://github.com/pfkellogg/fm1-mirror-spyglass-android) (Android) show it big, live. For
players who can't read the FM-1's 1.54-inch screen.

This is an **unofficial** build, not a NACHTFAHRT release, and Cranky Man has nothing to do with it. Everything else is
**NACHTFAHRT 1.0** (commit `f2de0d6`) as published. It's offered to Cranky Man as
[pull request #9](https://github.com/FartyPants/fm1-nachtfahrt/pull/9) (branch `big-screen-pr`: the firmware only).
**There is no download here:** the code is public, but no ready-made build is released while the pull request is open.

Tested on a real FM-1 (2026-11-17, flash chip 0x856014) with FM-1 Mirror 1.3.11 on a Mac: the whole screen arrives,
it updates live while NACHTFAHRT plays and answers, the piece's name reaches the spyglass, the music stays clean while
mirroring, and with no app asking nothing is sent.

**Watch it:** [NACHTBAR: a Late-Night Tenor Sax for the M-VAVE FM-1, and NACHTFAHRT on a Big Screen (Before/After)](https://youtu.be/TzhZUkLPeHU)
(3:00): NACHTFAHRT 1.0 before (FM-1 Mirror can't show it), and this build after, with Smooth on and then off.

**Newer FM-1s:** NACHTFAHRT's own issue tracker has a report that it doesn't work properly on newer FM-1s (UCUN flash
chip `0xB36014`). This build has the same limitation.

## What changed in the code

- `firmware/src/screen_mirror.c` / `.h`: the firmware-neutral mirror (MIT), the same code as in the SLOOP, Melodee,
  Skiff, JIANT FM, ChoralRoot, Erik37 and M-VAVE Open Big Screen builds.
- `firmware/src/nachtfahrt_mirror.c` (MIT): the glue. The hello from `usb.c`, sending through `usb.c`'s SysEx ring
  without ever waiting on it, the main loop's poll, and the piece playing as the sound's name (`F0 7D 'F' 'S' 02 <name> F7`).
- Small hooks in `lcd.c` (`lcd_fill`, `lcd_blit`), `usb.c` (the hello; `SXQ` may be set from outside), `main.c`
  (`NACHTFAHRT_BIG_SCREEN`, 1 by default, 0 builds without it; the SysEx ring 192 -> 512 words) and
  `app/main_fm1.c` (`mirror_service()` in the main loop).
- RAM .data+.bss 88,052 of 98,304 bytes; image 430,964 bytes.

## Building on a Mac without Docker (this branch only)

`build.sh` and `tools/build.py` here also run JieLi's **Windows** toolchain 2.5.3 under Wine: set `JIELI_WINE` to the
wine binary and `JIELI_WIN_BIN` to the toolchain's `pi32/bin`. Not part of the pull request.

## The protocol

The app sends `F0 7D 'F' 'S' 00 F7` about once a second (`01` also asks for the whole screen again, `02` only for the
name). While they keep coming, every rectangle drawn on the LCD also goes out as `F0 7D 'F' 'S' 01 row col <runs> F7`,
run-length coded RGB565. Three seconds after the last hello it stops.

Contact: pfkellogg@gmail.com
