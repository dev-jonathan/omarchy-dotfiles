# omarchy-dotfiles

Personal Omarchy configuration. Currently includes the US keyboard layout with Brazilian
accents, the input-remapper preset, and session autoload setup.

## Keyboard

A 75% ANSI US keyboard that also types Portuguese. The layout keeps every US key as printed
(`[ ] { } ? ; : / \ ' "`) and moves the accents to Right Alt.

| File | Purpose |
| --- | --- |
| `.config/xkb/symbols/brus` | Extends `us(altgr-intl)` with a tilde dead key on `` ` `` and `ç` on `c`. |
| `.config/omarchy/keyboard-layouts` | Selects `brus` / `altgr-intl` for Hyprland. |

| Keys | Result |
| --- | --- |
| `RAlt` + `'` + vowel | á é í ó ú |
| `RAlt` + `` ` `` + vowel | ã õ |
| `RAlt` + `Shift` + `` ` `` + vowel | à è ì ò ù |
| `RAlt` + `6` + vowel | â ê î ô û |
| `RAlt` + `c` | ç |
| `RAlt` + `Shift` + `c` | Ç |
| `RAlt` + `Shift` + `'` + u | ü |

`Shift + Caps Lock` remains the compose key. Plain `` ` `` and `~` still type as-is.

Restore by copying both files and reloading Hyprland:

```bash
cp -a .config/xkb "$HOME/.config/"
mkdir -p "$HOME/.config/omarchy"
cp .config/omarchy/keyboard-layouts "$HOME/.config/omarchy/"
hyprctl reload
```

`omarchy setup keyboard` only lists layouts from the installer catalog, so running it replaces
`brus` with a stock layout. Copy `.config/omarchy/keyboard-layouts` back afterwards.

## Input Remapper

Validated with `input-remapper-git 2.2.1.r18.gd18d486d-1`, Python 3.14, and kernel `6.18.57-2-lts`.

- Device: `SINOWEALTH 2.4G Wireless Receiver`.
- Preset: `Turbo Scroll`.
- Mapping: hold the side button with code `276` and move the mouse vertically to scroll.
- Output: vertical wheel (`REL_WHEEL`, code `8`), with gain `-12`.

| File | Purpose |
| --- | --- |
| `.config/input-remapper-2/config.json` | Assigns the device preset for automatic loading. |
| `.config/input-remapper-2/presets/SINOWEALTH 2.4G Wireless Receiver/Turbo Scroll.json` | Defines the input combination and scroll output. |
| `.local/bin/input-remapper-autoload` | Waits 5 seconds and runs autoload using `/usr/bin/python3`. |
| `.config/autostart/input-remapper-autoload.desktop` | Runs the script when the graphical session starts. |

## Restore

Install the package and enable the service:

```bash
omarchy pkg aur add input-remapper-git
sudo systemctl enable --now input-remapper.service
```

Back up any existing configuration. From the repository root, copy the files:

```bash
mkdir -p "$HOME/.config/autostart"
cp -a .config/input-remapper-2 "$HOME/.config/"
install -Dm755 .local/bin/input-remapper-autoload "$HOME/.local/bin/input-remapper-autoload"
cp .config/autostart/input-remapper-autoload.desktop "$HOME/.config/autostart/"
```

The `.desktop` file's `Exec` field points to `/home/devjonathan/.local/bin/input-remapper-autoload`. Update this path if your home directory differs. For other hardware, review the receiver name and the preset's `origin_hash`.

Load the configuration and inspect the logs:

```bash
~/.local/bin/input-remapper-autoload
systemctl status input-remapper.service
journalctl -b -t input-remapper-autoload --no-pager
```

The script explicitly uses the system Python to prevent runtime managers from selecting a different interpreter. Autoload works; the tray icon (`input-remapper-tray`) has a preexisting Python import failure that these files do not fix.

## Changes with agents

Use the `$omarchy` skill when asking an agent to modify these settings. On the local installation, its instructions are available at `/usr/share/omarchy/default/agents/skills/omarchy/SKILL.md`.

Keep customizations in `~/.config/` and `~/.local/`. Back up existing files before applying changes, and do not edit packaged files under `/usr/share/omarchy/`.
