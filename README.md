# Dracula for Omarchy

The [Dracula](https://draculatheme.com) palette as an [Omarchy](https://omarchy.org)
theme. Omarchy ships twenty-two first-party themes and Dracula is not among
them, so this is that.

## Install

```sh
omarchy theme install https://github.com/apachler/omarchy-dracula-theme.git
```

`omarchy-theme-install` derives the theme name from the repository name — it
strips the `omarchy-` prefix and the `-theme` suffix — so this installs as
**dracula** and is selectable from the theme switcher
(`SUPER + SHIFT + CTRL + SPACE`) or with `omarchy theme set dracula`.

## What is in here, and what deliberately is not

`colors.toml` is the whole theme.

Omarchy generates every per-application config from that palette when a theme
is set — the terminal, Neovim, Hyprland, btop, Chromium, Helix, VS Code, the
shell itself and Claude Code's colours are all rendered from
`default/themed/*.tpl` against the colorset. Shipping hand-written versions of
those would only give them a second place to drift from.

It also would not work. A theme installed from a git repository is held to
colour: `omarchy-theme-set` refuses to stage any file that runs code — every
`*.lua`, the terminal configs, `vscode.json` — because Hyprland and Neovim load
theirs at startup. That restriction is the right one, and this theme is built
to live inside it rather than around it.

`icons.theme` names the GTK icon set (`Yaru-purple`, for Dracula's accent).

`hyprland_active_border` and `hyprland_inactive_border` set the window borders.
They are read through the gradient helpers in
`default/themed/hyprland.lua.tpl` and `shell.toml.tpl`, which resolve bare
colour names out of this file — so `blue magenta 45deg` is Dracula's purple
running into its pink, without a second copy of either hex.

`backgrounds/` carries four wallpapers; the theme switcher cycles them and
`0-castle.webp` is the default. **They are 1672x941**, which is below the
3840x2160 the first-party themes ship — fine at 1080p, soft on a 1440p or 4K
display. Your own wallpapers go in `~/.config/omarchy/backgrounds/dracula/`,
which is picked up without touching this repository. `docs/backgrounds.md` is
the spec for generating replacements — resolution, palette, composition and a
prompt to start from.

Not included: `preview.png`. The theme switcher's previews are desktop
screenshots, which have to be taken on a running machine.

## Editing it

Two things about this file are constrained rather than chosen, and both produce
a plausible-looking but wrong Dracula if changed.

**There is no `purple` key, and there must not be one.**
`omarchy-theme-color` aliases `magenta` from `purple`, so a `purple` entry would
become ANSI 5. Dracula puts pink at 5 and purple at 4 — which is what `magenta`
and `blue` say instead. Reading the palette back, `{{ purple }}` correctly
resolves to pink, because that is Omarchy's convention rather than Dracula's
naming.

**`lighter_background` is not `selection`.** Dracula names one colour "Current
Line / Selection" and it is tempting to use `#44475a` for both. Don't: panels
are painted with `lighter_background`, so a selected row on a panel would then
be the same colour as the panel and disappear. `#363848` is the midpoint of
`background` and `selection`, which keeps the two distinguishable.

**ANSI 0 is `#282a36`, not the spec's `#21222c`.** `omarchy-theme-color`
assigns `color0` from `background` unconditionally, so the distinction Dracula
draws between its background and its ANSI black cannot survive the generator.
`dark_background` carries `#21222c` for anything wanting the darker chrome.

Check any change with Omarchy's own parser rather than by eye:

```sh
omarchy-theme-color --file colors.toml --all
```

## Credits

Palette by [Dracula](https://draculatheme.com), used under its MIT license.
This packaging is MIT too — see `LICENSE`.
