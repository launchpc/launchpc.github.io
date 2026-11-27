# LaunchPC website — notes for Claude

LaunchPC is a free Windows PC optimizer (Windows 10/11, 64-bit). It is written
in Python with psutil, shows its UI in a Microsoft Edge app window, and is
built into a single `LaunchPC.exe` with PyInstaller. Current version:
v0.1.1-beta.

The owner is not a developer-tools expert: explain things in plain, simple
words, avoid jargon, and keep answers short.

## Where things are

- **Website:** this repo (`launchpc/launchpc.github.io`), live at
  https://launchpc.github.io. Everything is in one `index.html`, plus
  `og.jpg` (share image) and `apple-touch-icon.png`.
- **Releases and downloads:** GitHub releases in `khaledahmed212/LaunchPC`.
  **Never move them.** The app's built-in updater reads releases from that
  repo. That repo's old website only redirects here.
- **App source code:** only on the owner's PC (the `LaunchPC2` folder), not on
  GitHub yet. It can't be read or changed from a cloud session.

## Rules

- Keep the design and animations as they are unless the owner asks to change
  them.
- Every visible piece of text needs an Arabic version (see below).
- Before pushing a change, test it in a browser: English and Arabic, desktop
  and phone width, and check for JavaScript errors. Sections fade in on
  scroll (`.reveal`), so scroll through the page before taking screenshots.
- Work on the session's `claude/...` branch; don't open pull requests unless
  asked.

## How the site works

- **English/Arabic:** English text is in the HTML; the Arabic version is in a
  `data-ar` attribute on the same element. Switching sets `lang="ar"` and
  `dir="rtl"`. The choice is saved in `localStorage` (`lpc_lang`); visitors
  whose browser language is Arabic get Arabic by default. Product names
  (LaunchPC, GitHub, Windows dialog text) stay in English.
- **Downloads update themselves:** on load, the page fetches
  `api.github.com/repos/khaledahmed212/LaunchPC/releases`, takes the newest
  release with an `.exe` asset, and updates every `a.dl` link, the version /
  size line (`#meta`), and the "What's new" list (`#relList`, release notes
  shown in English). No site edit is needed for a new release.
- **Fallback:** if GitHub can't be reached, the hard-coded `v0.1.1-beta` links,
  version `0.1.1`, size `12.9` MB (in `renderMeta`) and the static "What's
  new" items are used. Update them when a new version is released.
- **App preview:** an interactive fake of the app inside `.stage`, with a
  1-minute tour (`#tourBtn`, 11 steps). The preview is always English and
  left-to-right.
- **Download helper:** after clicking Download, a popup (`#dlm`) explains the
  Windows SmartScreen warning ("More info" → "Run anyway"). On phones and
  Macs it shows "open this on your PC" instead.

## To do / ideas

- Put the app source (LaunchPC2) on GitHub, maybe as a private repo, so it can
  be worked on from cloud sessions.
