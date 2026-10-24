// Dear programmer:
// When I wrote this code, only God and I knew how it worked.
// Now, only god knows it!
// Therefore, if you are trying to optimize this routine and it fails (most surely).
// please increase this counter as a warning for the next person:
// total_hours_wasted_here = 25




# pyPod 2.0 — an iPod (1st/2nd gen) music player for Linux and Windows

A floating, frameless, transparent desktop widget built with Python + PySide6 (Qt 6).
Only the iPod body is visible on your desktop; the rest of the window is fully transparent.

## Run from source (Linux)

(Windows: see [Windows](#windows) below.)

```bash
# Debian/Ubuntu: Qt 6.5+ needs libxcb-cursor0 on X11
sudo apt install python3-venv libxcb-cursor0

cd pypod
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python3 pypod.py                      # first start asks for your music folder
python3 pypod.py --folder ~/Music --scale 1.25 --on-top   # optional flags
```

Transparency needs a compositing window manager (GNOME, KDE, XFCE with compositor, etc.).
The chosen folder, volume, window position, size and settings are saved to `~/.config/pypod/config.json`
and restored on the next start.

## Controls

| Action | Mouse | Keyboard |
|---|---|---|
| Scroll list / volume / seek | drag in a circle on the wheel (or mouse wheel) | Up / Down |
| Select (centre button) | click centre | Enter |
| MENU (back) | click `menu` | Esc / Backspace |
| Previous / Next | click ⏮ / ⏭ (hold to seek) | Left / Right |
| Play / Pause | click ⏯ | Space |
| Volume | scroll on the Now Playing screen | + / - |
| Move the widget | drag the casing or the screen | |
| Menu: Always on Top, Size, Folder, Quit | right-click | |

* **Now Playing:** scrolling changes the volume. Press the centre button to enter *scrub mode*, then scroll to seek ±5 s; press centre or MENU to leave it.
* **Menus:** Music → Folders (browse your folder tree, "Play All" per folder) / Songs / Artists / Albums / Change Music Folder, Shuffle Songs, Settings (Always on Top, Repeat, Shuffle, Clicker Volume, Size, Music Folder…, Rescan, Quit).
* Library scan is recursive (.mp3, .flac, .ogg/.opus, .m4a, .wav) and reads tags (title / artist / album / track number) with `mutagen`, and falls back to the file name.

Optional: drop a pixel/Chicago-style `.ttf` into `assets/` (for example *ChicagoFLF* or *Silkscreen*) and it is used for the LCD. Without one, a monospace system font is used.

## Album art & ticker

* **Now Playing** shows the cover next to the title / artist / album. Covers come from the file's tags (MP3, FLAC, M4A) or from a `cover.jpg` / `folder.png` (also `front`, `album`, `artwork`) sitting next to the song. Songs without any art get an animated **disc and jewel case**: the disc is *slammed* up into the case (accelerating fall, impact flash and starburst, a shaking case, a rebound, a thud sound) and is thrown into a violent spin that settles to a steady turn while the song plays; it coasts to a stop when you pause (iridescent in *Color* mode, LCD-tinted in *LCD* mode). Set Album Art to *Off* for the classic centred layout.
* A small cover (or a tiny spinning disc, for songs without art) also appears in the **top bar** of every other screen while a song is loaded.
* While music is loaded, every menu gets a **sliding "title - artist" strip** under the top bar (it shows one menu row fewer).
* **Settings → Appearance → Album Art:** Color / LCD (tinted to the screen colours, like a real monochrome iPod) / Off. **Mini Ticker:** On / Off. **Slam Shake:** On / Off (the case shaking on impact; the thump when the disc hits the case follows *Clicker Volume*).

## Discord Rich Presence

Shows what you are listening to on your Discord profile ("Listening to pyPod", title, artist, with a progress bar; "Paused - artist" when paused). It is **off by default** and nothing is sent anywhere until you turn it on.

One-time setup (Discord needs an application of your own, which is free):

1. Open <https://discord.com/developers/applications> → **New Application**. The name you give it is what shows as "Listening to ...".
2. Copy the **Application ID** from the *General Information* page.
3. *Rich Presence → Art Assets* → upload a square image (for example the pyPod icon) and give it the key `pypod`.
4. In pyPod: **Settings → Discord → Client ID...** → paste the ID, then switch **Presence** to **On**.

**Settings → Discord → Status** tells you what is going on: *Connected*, *No Discord* (the Discord desktop app is not running, or its IPC socket was not found), *Bad ID* (Discord refused the Application ID), *No ID*, *Rejected* or *Off*. pyPod retries by itself, so you can start it before Discord. Use **Image Key...** if you named the art asset something other than `pypod`. You can also provide the ID through the `PYPOD_DISCORD_ID` environment variable.

Works with the Discord desktop app on Windows (named pipe) and Linux (native, Flatpak or Snap) and with clients that provide the same local socket (e.g. Vesktop with its built-in Rich Presence support).

## Visualizer

A live spectrum visualizer. It is **off by default**: switch it on in **Settings → Visualizer → Visualizer**.

* **Where it shows:** a strip on the Now Playing screen (between the cover / text and the progress bar), and a full-screen **Visualizer** entry that appears in the main menu while it is on. On the full-screen view the centre button cycles the style and the wheel changes the volume (a volume bar replaces the title while you turn it).
* **Style:** Bars, Mirror, Wave, LED.
* **Colors:** LCD (screen ink), Rainbow, Fire, Ocean, Neon, Sunset, or **Cover**, which picks a few vivid colours from the song's cover art and blends them across the bars (darker at the base, brighter at the tips). Songs without a cover fall back to Rainbow.
* **Bars:** 16 / 24 / 32. **Gain:** Low / Normal / High. **Peaks:** falling peak caps on/off. **Smoothing:** Snappy / Normal / Smooth.
* **Source** (read-only, in the same menu) tells you where the movement comes from: *Live* = real spectrum of the track, *Loading* = the track is being analysed, *Simulated* = the track could not be decoded, so the bars move in a generic pattern instead.

How it works: when a song starts, pyPod decodes it in the background (a few tenths of a second) and then looks up the spectrum for the current playback position, so it stays in sync through seeking. It needs one of:

* **miniaudio** (`pip install miniaudio`) – decodes MP3 / FLAC / OGG / WAV inside Python, or
* **ffmpeg** on your `PATH` (already installed on most Linux desktops, e.g. Nobara; on Windows put `ffmpeg.exe` on the `PATH` or next to `pyPod.exe`) – handles every format including M4A / Opus.

`numpy` (`pip install numpy`) makes the analysis very cheap; without it a slower pure-Python fallback is used. `./install_launcher.sh` installs both extras if it can. `pip install -r requirements-visualizer.txt` does the same by hand.

## Skins & customization

**Settings → Appearance** (or right-click → Skin):

* **Skin:** three skins that follow the song's cover (they fade smoothly when the song changes): **Cover Match** (the cover's real dominant colours), **Cover Vibrant** (the same colours made vivid, slowly drifting between each other) and **Cover Lava** (a dark casing with glowing blobs in the cover's colours that drift, swell and stretch like a lava lamp). Songs without a cover keep the colours of the last cover (silver before the first one). Then the plain skins: White, Silver, Graphite, Black, Red, Orange, Yellow, Green, Mint, Blue, Navy, Purple, Pink, or **Custom...** (any colour via a colour picker).
* **Rainbow Mode:** the casing and wheel slowly cycle through every colour. **Rainbow Speed:** Slow / Medium / Fast.
* **Wheel:** Match (follows the skin), White or Black.
* **Screen:** LCD colour scheme: Classic, Green, Amber, Mono, Night, Ruby.
* **Opacity:** 100% down to 60% (needs a compositor). **Shadow:** drop shadow on/off.

**Settings → Clicker Volume** sets the wheel / button clicks (and the disc thump) to 0% (off), 25%, 50%, 85% or 100%; each change plays a test click. **Click Engine** chooses how the clicks are played: *Auto* (the system player `pw-play` / `paplay` / `aplay` when one is installed, otherwise Qt), *Qt* or *System*. Run `python3 pypod.py --sound-test` for a diagnosis: it prints your config, the audio devices and the state of Qt's sound effect, then plays three clicks through Qt and three through the system player. On start-up pyPod also prints one line like `pyPod: sound click.wav: Qt status=..., system player=...` to the terminal. (A config saved by an older version with the clicker switched off becomes 0%.)

**Settings** also has **Sensitivity** (how far you rotate on the wheel per click: Low / Normal / High), **Size**, **Always on Top**, **Repeat** and **Shuffle**.
Everything is saved to `~/.config/pypod/config.json` (`%APPDATA%\pypod\config.json` on Windows).

## Windows

Same app, same features. Pick one of three ways:

**1. Run from source (quickest).** Install Python 3.9+ from <https://www.python.org/downloads/> (tick *Add python.exe to PATH*), unzip pyPod, then double-click **`run_windows.bat`**. The first start creates a `.venv` and installs the dependencies (a minute or two).

**2. Start-menu / taskbar entry.** Double-click **`install_launcher.bat`**. It sets up the `.venv`, draws an icon and adds a *pyPod* shortcut to the Start menu: search for it, right-click → **Pin to taskbar**. Remove it again with `install_launcher.bat -Uninstall`.

**3. A standalone `pyPod.exe`** (nothing needs to be installed to run it). Double-click **`build_windows.bat`** (needs Python and internet once). It produces `pyPod-windows-x64.zip`: unzip it anywhere, run `pyPod\pyPod.exe`, right-click → **Pin to taskbar**. `build_windows.bat -OneFile` makes a single `pyPod.exe` instead (slower to start, and antivirus programs flag those more often).
No Windows PC at hand? Put the project in a GitHub repository and run the included workflow (**Actions → build-windows → Run workflow**); the zip appears under *Artifacts*.

Windows notes:

* **Always on Top** works natively. The window is transparent and frameless; drag the casing to move it. Right-click for the menu, or **Settings → Quit**.
* **Settings** are saved in `%APPDATA%\pypod\config.json`.
* **Clicks** use Windows' built-in sound API (*Click Engine → Auto* or *System*); the volume levels work through quieter copies of the click. *Qt* is the alternative.
* **Visualizer:** the exe contains `miniaudio` (MP3 / FLAC / OGG / WAV). For M4A / Opus, put `ffmpeg.exe` next to `pyPod.exe`; otherwise those songs show *Simulated*.
* **Discord** presence works with the Discord desktop app (and Vesktop's built-in Rich Presence).
* Windows SmartScreen may warn about an unsigned exe the first time: *More info → Run anyway*.
* The exe has no console window, so for `--sound-test` use the source version: `.venv\Scripts\python.exe pypod.py --sound-test`.

## Build an AppImage (Linux)

```bash
cd pypod
./build_appimage.sh          # needs python3, internet access (pip + appimagetool download)
./pyPod-x86_64.AppImage
```

The script creates a virtualenv, freezes the app with PyInstaller (trimmed Qt modules), assembles an AppDir
(AppRun, `.desktop`, generated icon) and runs `appimagetool`. Build on an older distro (e.g. Ubuntu 22.04)
so the result runs on newer ones. If the target machine has no FUSE: `./pyPod-x86_64.AppImage --appimage-extract-and-run`.

## Troubleshooting

* **"Could not load the Qt platform plugin xcb"** → `sudo apt install libxcb-cursor0`.
* **Always on Top does nothing on GNOME/Wayland** (Wayland forbids it for ordinary clients) → run with XWayland: `QT_QPA_PLATFORM=xcb python3 pypod.py`.
* **No sound / MP3 won't play** → Qt 6 ships an FFmpeg backend in the PySide6 wheel; if you use a distro Qt, install `gstreamer1.0-plugins-good`/`-ugly` or `ffmpeg`.
* **Opaque black/white box instead of transparency** → no compositor running.
