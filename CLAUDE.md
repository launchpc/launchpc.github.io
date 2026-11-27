# LaunchPC — notes for Claude

Read this first. It explains the project, what has been done, and how things
are wired together.

Owner: Khaled (GitHub: khaledahmed212). He often works from his phone and is
new to Claude Code: keep replies short, use plain words (no jargon), and
finish the work yourself rather than handing steps back to him.

## What LaunchPC is

A free, all-in-one Windows 10/11 (64-bit) PC optimizer and cleaner. Python 3
with psutil, UI shown in a Microsoft Edge app window, built into one
`LaunchPC.exe` with PyInstaller. Current version: **0.1.1 Beta** (tag
`v0.1.1-beta`).

Features: health score and recommendations, empty Recycle Bin, clean junk
files, space by folder, largest/unused files, old downloads, duplicate finder,
installed apps with sizes plus uninstall, startup apps on/off (tidy names,
merged duplicates), live CPU/memory/disk/network, Task Manager-style process
list, notification center (bell), light/dark mode, accent colors, subtle
effects and animations, in-app updates.

## Where everything lives

| What | Where |
|---|---|
| Website (live) | This repo, `launchpc/launchpc.github.io`, served at **https://launchpc.github.io**. Merging to `main` publishes it in about a minute. |
| Releases / downloads | GitHub Releases on `khaledahmed212/LaunchPC`. |
| Old website address | `khaledahmed212.github.io/LaunchPC/` only redirects here (`index.html` in `khaledahmed212/LaunchPC`). |
| Bug reports / ideas | Issues on `khaledahmed212/LaunchPC`, forms in its `.github/ISSUE_TEMPLATE/` (`bug.yml`, `idea.yml`). |
| App source code | Only on Khaled's PC (`LaunchPC2` folder). **Not on GitHub yet**, so a cloud session can't see it unless he uploads it. |

**Never move the releases out of `khaledahmed212/LaunchPC`.** The app's
updater (`UPDATE_REPO` in `launchpc.py`) and this website's download buttons
read from there. Moving them breaks updates for everyone who installed the app.

## Rules for the website

- Keep the design and animations as they are unless Khaled asks.
- Every visible text needs an Arabic version in a `data-ar` attribute.
  Product names (LaunchPC, GitHub, Windows dialog text) stay in English.
- Before pushing, test in a browser: English and Arabic, desktop and phone
  width, Safari/WebKit too, and check for JavaScript errors. Sections fade in
  on scroll (`.reveal`), so scroll through the page before screenshots.

## How the website works

Single `index.html` (no build step), plus `og.jpg` (1200×630 link preview) and
`apple-touch-icon.png`.

- **Sections:** a Windows 11-style interactive app preview (`.lp`, inside
  `.stage`) with a taskbar and an 11-step guided tour (`#tourBtn`); then
  download, features, how it works, safety, "What's new", FAQ, feedback and
  footer. The preview is always English and left-to-right.
- **Languages:** `setLang` swaps text from `data-ar` and keeps the English in
  `dataset.en`; sets `lang="ar"` / `dir="rtl"`. Choice saved in `localStorage`
  key `lpc_lang`, otherwise detected from `navigator.language`. RTL styling via
  `html[lang=ar]` CSS.
- **Language switch animation:** visible text blocks fade out quickly (130 ms),
  text swaps while hidden, then blocks fade and rise back in a sweep (900 ms
  total) timed with `outInv`. Built with the Web Animations API.
  - **Don't** use View Transitions (caused a flash on iPhone Safari).
  - **Don't** wrap words in spans (caused layout jumps).
  - **No** glowing line or band — Khaled asked for it to be removed.
  - `warmUp()` preloads the Arabic font in a hidden 1px box.
- **Releases:** on load the page fetches
  `api.github.com/repos/khaledahmed212/LaunchPC/releases`, takes the newest
  release with an `.exe`, and updates every `a.dl` link, the version/size line
  (`#meta`, `renderMeta`) and "What's new" (`#relList`, notes shown in English).
  If GitHub can't be reached, the hard-coded `v0.1.1-beta` links, `0.1.1` /
  `12.9` MB and static "What's new" items are used — keep those current.
- **Download helper:** modal `#dlm`. On Windows it shows how to get past the
  SmartScreen warning ("More info" → "Run anyway"). On phones/Macs it stops the
  download and offers "Send the link to my PC" (share sheet or copy).
- **Mobile fixes to keep:** `main{overflow-x:clip}` (a glow made the page too
  wide), the header badge hidden under 420px, and nothing positioned far
  off-screen (that widened the page in RTL).

## The app (source on Khaled's PC)

- `launchpc.py` — whole backend. Dependencies: `psutil`, `send2trash`. Runs a
  local `ThreadingHTTPServer` on `127.0.0.1` (random port, per-run token) and
  opens the UI in an Edge `--app` window (own profile, 1320×860). Every `api_*`
  method is an endpoint the UI calls.
  - Constants: `APP_VERSION = "0.1.1"`, `APP_CHANNEL = "Beta"`,
    `UPDATE_REPO = "khaledahmed212/LaunchPC"`.
  - Data in `%APPDATA%\LaunchPC\` (`settings.json`, `last_scan.json`,
    `notifications.json`); defaults in `DEFAULTS`.
  - Deletes go to the Recycle Bin (send2trash), never permanent. `PROTECTED`
    lists processes that must never be ended.
  - Startup apps: `pretty_startup_name`, `merge_startup_duplicates`; merged
    items carry several ids joined by `"\n"`, split in `api_toggle_startup`.
  - Updater: `api_check_update` reads the releases API and picks the `.exe`;
    `api_install_update` downloads it and swaps the exe.
- `ui/index.html` — the entire app UI (notification bell `notify()`,
  `checkAlerts`, `checkNewStartup`; effects toggle `html.fx`; circular reveal
  when switching theme).
- `Build EXE.bat` — builds `dist\LaunchPC.exe` with PyInstaller (Windows only).
  `Start LaunchPC.bat` runs from source.
- `.github/workflows/build.yml` — ready but unused: builds `LaunchPC.exe` on a
  GitHub Windows machine (on a `v*` tag or by hand), once the code is on GitHub.
- Khaled's PC also has a self-test: `test\selftest.py`, `test\Run test.bat`.

### Releasing a new version

1. Bump `APP_VERSION` in `launchpc.py`.
2. Build the exe (`Build EXE.bat` on Windows, or the GitHub workflow).
3. Create a release on `khaledahmed212/LaunchPC` with a tag like
   `v0.1.2-beta`, marked pre-release, with a file named exactly
   **`LaunchPC.exe`**.
4. Update the fallback version, size and links in this site's `index.html`.

## History

- 0.1.0 Beta: first release of the app and website.
- 0.1.1 Beta: readable startup app names and merged duplicates, notification
  center, subtle effects, more animations.
- Website: interactive preview with tour, link preview image, What's new,
  Arabic version, download helper, feedback forms, animated language switch
  (fixed the iPhone flash and stutter, removed the blue line). Moved from
  `khaledahmed212.github.io/LaunchPC` to launchpc.github.io (old address
  redirects).
- A 30-second vertical announcement video and a TikTok account were made.

## Ideas / next steps

- Put the app code in a private GitHub repo and use the build workflow, so
  new versions can be built and released without Khaled's PC.
- Update the TikTok bio and the video's end card to the new address.
- Microsoft Store listing (sign-up trouble, not done).

## Git

Commit as Khaled: `git -c user.name="khaledahmed212" -c
user.email="khaledahmed212@users.noreply.github.com" commit ...`.
