# myles.media

An [Omarchy](https://omarchy.org) shell plugin: one media control for the whole desktop.

A bar widget plus a full panel that unifies **Spotify**, **YouTube**, **Radio Garden**, local
files, and anything else MPRIS can see — with a picture-in-picture video window, downloads,
and Chromecast/DLNA casting.

This is a fork of Omarchy's built-in `omarchy.media`, taken over via `omarchy plugin clone`
and heavily extended.

---

## Install

```bash
omarchy plugin add https://github.com/Omarchy-plugin/omarchy-myles-media.git --enable --yes
```

That clones, validates, installs to `~/.config/omarchy/plugins/myles.media/`, and puts the
widget on your bar. Choose a bar section when prompted.

### Disable the built-in

The stock `omarchy.media` widget does the same job and will fight with this one. Turn it off:

```bash
omarchy plugin disable omarchy.media
```

### Update

```bash
omarchy plugin update myles.media --yes
```

### Uninstall

```bash
omarchy plugin disable omarchy.media   # if you re-enabled it
omarchy plugin remove myles.media --yes
```

Your saved library and settings survive uninstall. To wipe them too:

```bash
rm -rf ~/.local/state/omarchy/media ~/.cache/myles.media
```

---

## Dependencies

Most of these ship with Omarchy already. Install anything missing:

```bash
omarchy pkg add mpv yt-dlp ffmpeg nodejs python imagemagick curl playerctl
```

| Command | Needed for | Required? |
| --- | --- | --- |
| `mpv` | Video playback and PiP | Yes |
| `yt-dlp` | YouTube search, all downloads | Yes |
| `ffmpeg` / `ffprobe` | Audio-only (MP3) conversion, metadata | Yes |
| `node` | Search backends, `MediaModel.js` | Yes |
| `python3` | Search/cast/radio helpers | Yes |
| `curl` | Radio Garden + favourite link checks | Yes |
| `convert` (ImageMagick) | Fallback artwork extraction | Recommended |
| `playerctl` | MPRIS control of external players (Spotify, browsers) | Recommended |
| `cliamp` | Primary playback backend | Optional, see below |

### About `cliamp`

This plugin prefers [`cliamp`](https://github.com/bjarneo/cliamp) — a retro terminal music
player it drives for playback, volume, queue, and EQ — and falls back to `mpv` when `cliamp`
is not installed. It ships in the `omarchy` repository:

```bash
omarchy pkg add cliamp
```

Without it you still get video PiP, downloads, YouTube search, local files, and casting.

---

## Using it

### Bar widget

| Click | Action |
| --- | --- |
| **Left** | Open the media panel |
| **Right** | Open Favourites |
| Transport buttons | Previous / play-pause / next |
| Heart | Favourite the current track or station |
| Download | Save the current track as video or MP3 |

### Panel hubs

| Hub | What it does |
| --- | --- |
| **Favourites** | Loved tracks and stations |
| **Recents** | Recently played from the drawer |
| **Downloads** | Saved from Now Playing (`~/Downloads/Media`) |
| **Radio Garden** | Search stations by city or frequency; play in-drawer |
| **Spotify** | Search tracks; play in-drawer |
| **YouTube** | Search and play video and audio |
| **Media Player** | Mirror and control `mpv` when it is already running |
| **Local** | Browse local audio files |

Pin the hubs you use most — pins persist across restarts. Casting targets (Chromecast,
DLNA) appear in the panel when devices are discovered on the network.

Every source plays **inside the drawer**. Nothing launches an external app behind your back.

---

## Where things live

| What | Path |
| --- | --- |
| Library, settings, recents, pins | `~/.local/state/omarchy/media/` |
| Downloads | `~/Downloads/Media/` |
| Search cache | `~/.cache/myles.media/` |
| Plugin code | `~/.config/omarchy/plugins/myles.media/` |

Move the library between machines with the built-in export/import in the panel, or by copying
`~/.local/state/omarchy/media/` directly.

---

## Customising

`~/.config/omarchy/plugins/myles.media/` is yours to edit — the shell hot-reloads on save.
For a small behavioural change, edit and move on. For a large one, fork this repo.

```bash
git clone https://github.com/Omarchy-plugin/omarchy-myles-media.git
omarchy plugin add /path/to/omarchy-myles-media --enable --yes
```

`manifest.json` holds the plugin id, version, and entry points. Bump `version` when you publish
a change.

---

## Troubleshooting

**Widget is missing.** The bar was reset:

```bash
omarchy plugin enable myles.media --section center
```

**Search returns nothing.** Usually a missing `yt-dlp` or `node`:
`command -v yt-dlp node`.

**Nothing plays.** Check which backend is live:

```bash
command -v cliamp   # absent means mpv fallback is in use
```

**Download fails.** `ffmpeg` is required for MP3 output:
`command -v ffmpeg`.

**Read the logs:**

```bash
journalctl --user -xeu omarchy-shell -n 100 --no-pager
```

**Force a reload after editing code:**

```bash
omarchy restart shell
```

**Note:** `omarchy refresh shell` resets `shell.json` to defaults, which drops the widget off
the bar. The plugin files are untouched — re-enable it as shown above.

---

## Development

```bash
# Validate the manifest the same way the installer does
omarchy plugin validate .

# Run the MediaModel unit tests
node test-media-model.js
```

---

## License

[MIT](LICENSE) © 2026 Mylesoft
