# Zen Ctrl+Tab previews: raise the limit to 12

Zen's Ctrl+Tab popup lists your recently used tabs. It shows at most 7 previews by default. This guide raises that limit to 12 with a preference change. Nothing is patched or installed.

No source build is required. The only requirement is a Zen release that has the `browser.ctrlTab.maxPreviews` preference.

## Check your Zen version

The preference exists in current Zen releases. It is confirmed present in 1.23.4b (checked in the released Linux build) and missing in 1.19.11b, where the limit 7 is hardcoded in the JavaScript and cannot be changed. Unless you are on a recent release, update Zen first. On Arch Linux with the `zen-browser-bin` AUR package:

```sh
yay -S zen-browser-bin
```

You can check what you have in the Zen menu under Help, then About Zen. To confirm the preference exists, open `about:config` and search for `browser.ctrlTab.maxPreviews`. If it shows up, you are set.

## Before you start

Two things need to be true:

1. The preference exists. See the version check above.
2. The popup is enabled. Search for `browser.ctrlTab.sortByRecentlyUsed` and set it to `true` if it is `false`. Without this, Ctrl+Tab switches tabs without opening the preview popup.

## Option 1: about:config

1. Open a new tab and go to `about:config`.
2. Accept the warning if Zen shows one.
3. Search for `browser.ctrlTab.maxPreviews`.
4. Click the edit button, enter `12`, and confirm.
5. Set `browser.ctrlTab.sortByRecentlyUsed` to `true` if needed.
6. Open at least 12 tabs and press Ctrl+Tab.

The value is clamped to the range 4 to 49, so 12 is accepted. Any number in that range works.

To undo, open the same preference and click Reset. Zen falls back to 7.

## Option 2: user.js

Use this when you want the setting in a file you can copy between profiles or machines.

1. Open `about:support`.
2. Find Profile Directory and click Open Directory.
3. Close Zen completely.
4. Copy `user.js` from this repository into the profile directory. If a `user.js` already exists, add the two lines to it instead of replacing it.
5. Start Zen.

```js
user_pref("browser.ctrlTab.sortByRecentlyUsed", true);
user_pref("browser.ctrlTab.maxPreviews", 12);
```

Zen reads `user.js` at every startup and applies these values. Changes you make to the same preferences in `about:config` are overwritten on the next start while the file is present.

To undo, close Zen, delete `user.js` or remove those lines, start Zen, then reset both preferences in `about:config`. Removing the file stops future overrides, but it does not clear values that are already stored in the profile.

## Building Zen from source

This is only for developers who already build Zen. Regular users do not need it, the preference above is enough. Add this entry to `prefs/firefox/browser.yaml`:

```yaml
- name: browser.ctrlTab.maxPreviews
  value: 12
```

Then rebuild:

```sh
npm run ffprefs
npm run build:ui
```

`npm run build:ui` runs `mach build faster`, which needs one completed full build first. On a fresh checkout run `npm run build` once before using the UI-only command.

## Troubleshooting

- The popup does not open: check `browser.ctrlTab.sortByRecentlyUsed`. It must be `true`.
- Fewer than 12 previews appear: Zen's Ctrl+Tab list skips hidden tabs, the empty new tab placeholder, and pending tabs. Pending tabs are excluded unless `zen.ctrlTab.show-pending-tabs` is `true`. You also need at least 12 normal tabs open for 12 previews to show.
- You changed the value but see no difference: close every Zen window and start Zen again, especially after editing `user.js`.
- Nothing changes at all and the preference is missing from `about:config`: your Zen release does not have the preference yet (1.19.11b does not, 1.23.4b does). Update Zen.
- Layout: 12 previews are arranged in a 7 column grid, so you get one row of 7 and a second row of 5. The panel width stays the same because it is calculated from the column count. The taller panel still fits a 1080p screen.

## Why not a Zen Mod or an extension

A Zen Mod is a CSS file plus optional preference toggles. The toggles support booleans and strings, not integer preferences, and the preview buttons are created in JavaScript, so CSS cannot add more of them.

WebExtensions cannot bind Ctrl+Tab, cannot write arbitrary preferences, and cannot add elements to browser chrome.

A preference change or a source build is the only way to do this today.
