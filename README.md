# Colorice

[![PyPI](https://img.shields.io/pypi/v/colorice?style=for-the-badge&logo=pypi&logoColor=white&color=bd93f9)](https://pypi.org/project/colorice/)
[![Python](https://img.shields.io/pypi/pyversions/colorice?style=for-the-badge&logo=python&logoColor=white&color=bd93f9)](https://pypi.org/project/colorice/)
[![License](https://img.shields.io/badge/license-GPL--3.0-bd93f9?style=for-the-badge)](LICENSE)

**A modern alternative to pywal — wallpaper-driven Linux desktop themes, perceptually accurate.**

- Color extraction in **Oklab** (perceptually uniform), not RGB/HSL — equal numeric steps actually look equal, so palettes feel harmonious
- **WCAG contrast enforcement** — foregrounds at 4.5:1 (AA) against bg, no washed-out palettes
- **Pywal-compatible templates** — existing pywal templates work as-is
- **Oklab manipulation filters** — derive shades inside templates: `{color4.lighten_20}`, `.darken_15`, `.saturate_10`
- **22 bundled templates** with live-reload hooks (kitty, alacritty, hyprland, sway, i3, polybar, waybar, dunst, mako, rofi, swaylock, zellij, neovim, vim, cava, …)

### 📸 In Action
<p align="center">
  <img width="99%" alt="Wallpaper on the left and a desktop themed by colorice on the right — kitty, neovim, polybar all matching the wallpaper's palette" src="https://github.com/user-attachments/assets/39fe3af7-aea9-4107-b697-b3ec0b3cb5e2" />
</p>
<br><br>

<details>
  <summary>More Examples! ✨ </summary>

  <p align="center">
    <img width="49%" alt="Desktop themed by colorice — example 3" src="https://github.com/user-attachments/assets/d1a260b9-b547-4fe6-97c7-5c6243deb82b" />
    <img width="49%" alt="Desktop themed by colorice — example 1" src="https://github.com/user-attachments/assets/cc4babb2-9e72-4a1a-a45c-c539d9617de9" />
  </p>

  <p align="center">
    <img width="49%" alt="Desktop themed by colorice — example 4" src="https://github.com/user-attachments/assets/fdabeb25-ac64-483e-9bdd-8eebec100fb4" />
    <img width="49%" alt="Desktop themed by colorice — example 2" src="https://github.com/user-attachments/assets/9f8480f9-303e-422f-b470-34b2f4d26154" />
  </p>
  <p align="center"><em>From image → a complete desktop theme</em></p>
  <br><br>

  <p align="center">
    <img width="49%" alt="Theme generated from a mostly monochrome image, foreground still legible" src="https://github.com/user-attachments/assets/74c87089-565d-4318-9ae6-7eae472851b5" />
    <img width="49%" alt="Theme generated from a flat-tone image, foreground still legible" src="https://github.com/user-attachments/assets/a1483904-12a7-4f7c-b3ae-ca6aae8dce0f" />
  </p>
  <p align="center"><em>Respects contrast ratios even for palettes sourced from mostly monochrome or flat images.</em></p>
  <br><br>

  <p align="center">
    <img width="49%" alt="Vivid palette extracted from a richly colored wallpaper" src="https://github.com/user-attachments/assets/15af6d3e-0110-4ee5-ae5c-c51b739596e6" />
    <img width="49%" alt="Vivid palette extracted from another richly colored wallpaper" src="https://github.com/user-attachments/assets/d01bc890-d2d2-4f92-91b4-9dfc4e3e5733" />
  </p>
  <p align="center"><em>Picks a rich variety of colours for your palette given a rich and vibrant image.</em></p>
  <br><br>
</details>

## How it compares

| Feature | colorice | pywal | wallust | matugen |
|---|---|---|---|---|
| Color space | **Oklab** | RGB/HSL | LCH | HCT |
| Contrast enforcement | ✅ WCAG AA | ❌ | ✅ | ❌ |
| Pywal-compatible templates | ✅ | ✅ | partial | ❌ |
| Color manipulation in templates | ✅ Oklab filters | ❌ | ✅ | ❌ |
| Mood variants | ✅ (4) | ❌ | ❌ | ❌ |

## Install

Requires Python 3.11+. Works anywhere Python runs — the bundled templates target Linux WMs and editors, but the core engine (extract → template → write) is platform-agnostic. macOS/Windows users can use the terminal/editor templates as-is and write their own for anything else (CSS variables, theme files, design palettes — colorice doesn't care what's in the template).

```bash
pipx install colorice
```

Or with pip:

```bash
pip install colorice
```

Upgrade later:

```bash
pipx upgrade colorice
```

Run `colorice --help` for the full flag list.

## Quick start

> **Coming from pywal?** Setup is the same — uncomment the templates you want, point your apps at the rendered files. Existing pywal templates work in colorice as-is.

**1. Install bundled templates and starter config**

```bash
colorice --init
```

Drops 22 templates into `~/.config/colorice/templates/` and a starter `config.toml` into `~/.config/colorice/`.

**2. Pick which tools to theme**

Open `~/.config/colorice/config.toml` — every entry ships **commented out** so you opt in only to what you use. Uncomment the entries for your tools.

**3. Wire each app to load the output**

Each template's header (e.g. `~/.config/colorice/templates/kitty.conf`) describes how to integrate that tool. The pattern depends on the app:

- Most apps load colorice's output via an `include`, `source`, `import`, or `colorscheme`/`theme` directive in their own config (kitty, alacritty, hyprland, i3, sway, polybar, waybar, neovim, vim, zellij, …).
- A few apps without an include mechanism have their full config rewritten by colorice directly (dunst, cava). Nothing to wire — just enable the template.

**4. Generate and apply**

```bash
colorice ~/wallpapers/sunset.jpg --segment --apply
```

`--segment` enables region-aware extraction (richer palettes on complex images). Colorice extracts colors, shows 4 palette variants to pick from, then renders every configured template and reloads your apps.

Re-apply later without re-extracting:

```bash
colorice --apply
```

## One-keybind rice refresh

```bash
#!/usr/bin/env bash
WALLPAPER=$(find ~/Pictures/Wallpapers -type f | shuf -n 1)
feh --bg-fill "$WALLPAPER"
colorice "$WALLPAPER" --apply --no-preview -q
notify-send "Theme refreshed" "$(basename "$WALLPAPER")"
```

Bind to `Mod+W` in your WM config and one hotkey:
- Picks a random wallpaper
- Generates a fresh scheme
- Reskins every configured app live (kitty, hyprland, neovim, polybar, dunst, cava, …)
- Notifies on done

## Examples

```bash
# Generate and interactively select a scheme
colorice ~/wallpapers/sunset.jpg

# Generate, select, and apply to all configured tools
colorice ~/wallpapers/sunset.jpg --apply

# Quiet mode, pipe JSON to stdout
colorice ~/wallpapers/ocean.png -q -o -

# Light theme with specific moods
colorice wallpaper.jpg --light -m vibrant,cool

# Region-aware extraction (better for complex images)
colorice wallpaper.jpg --segment --apply

# Re-apply existing scheme to templates (no image needed)
colorice --apply

# Preview template output without writing
colorice wallpaper.jpg --dry-run --apply
```

## Template system

Colorice includes a pywal-compatible template engine. Templates use `{color0}` through `{color15}`, `{background}`, `{foreground}`, `{cursor}`, and `{wallpaper}` placeholders.

Edit `~/.config/colorice/config.toml` to map templates to output paths:

```toml
[[templates]]
name = "kitty"
input = "kitty.conf"                              # filename in ~/.config/colorice/templates/
output = "~/.config/kitty/current-theme.conf"     # absolute path where the rendered file goes
hook = "killall -USR1 kitty"                      # optional shell command after render
```

Each template file's header (`~/.config/colorice/templates/<name>.*`) lists how to wire that specific tool.

### Color manipulation

Templates support Oklab color manipulation filters for deriving colors beyond the base 16:

```
{color4.lighten_20}           Increase lightness by 0.20
{color0.darken_10}            Decrease lightness by 0.10
{color1.saturate_15}          Increase chroma by 0.15
{color5.desaturate_10}        Decrease chroma by 0.10
{color4.lighten_20.strip}     Manipulation + format modifier
```

Manipulations can be chained — they apply left to right:

```
{color4.lighten_20.saturate_10.strip}
{color0.darken_10.desaturate_5}
```

Format modifiers (always last in the chain):
- `.strip` — hex without `#` (`1a2b3c`)
- `.red`, `.green`, `.blue` — integer channel value (0-255)
- `.rgb` — `rgb(26,43,60)`
- `.rgba` — `rgba(26,43,60,1.0)`

## How it works

1. **Extract** — KMeans clustering in Oklab color space, with farthest-first selection for perceptual diversity. Optional Felzenszwalb segmentation for region-aware extraction. Results are cached per-image.
2. **Mood transform** — adjusts chroma/lightness/hue per the selected mood (vibrant/muted/warm/cool).
3. **Assign roles** — maps colors to 16 ANSI slots, sorted by chroma so the palette tracks the wallpaper's character. Foregrounds enforce WCAG contrast against the background via binary search on Oklab lightness.
4. **Apply** — renders templates with the scheme and runs post-apply hooks.

## Troubleshooting

- **"No templates configured"** — open `~/.config/colorice/config.toml` and uncomment the tools you want.
- **Apply runs but apps don't change** — make sure each app's own config has the `include` / `source` / `colorscheme` directive (see step 3 of Quick start, and each template file's header).
- **Hook fails for systemd-managed apps** — use `systemctl --user restart <app>` instead of `pkill <app>` (systemd will respawn it without your custom flags otherwise).
- **Picom hook fails on slower machines** — increase the `sleep` value in the picom hook (default `0.5`).
- **Color8 / comments look dim in some apps** — colorice enforces 4.5:1 against the terminal palette. Some apps bypass the terminal palette and hardcode their own colors (e.g. micro's default monokai theme uses hex values directly). Switch those apps to a colorscheme that uses ANSI named colors (e.g. `set colorscheme simple` in micro).

## File locations (XDG-compliant)

```
~/.config/colorice/
  config.toml            Template configuration
  templates/             Template files

~/.local/share/colorice/
  colors.json            Generated scheme

~/.cache/colorice/
  <hash>.json            Extraction cache
```

Respects `$XDG_CONFIG_HOME`, `$XDG_DATA_HOME`, and `$XDG_CACHE_HOME`.

## Development

```bash
git clone https://github.com/rattle99/colorice.git
cd colorice
pip install -e ".[dev]"
pytest
```

Issues, feedback, and PRs welcome at https://github.com/rattle99/colorice/issues.

## License

GPL-3.0 — see [LICENSE](LICENSE).
