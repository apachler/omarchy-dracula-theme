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

Not included, and easy to add locally: `backgrounds/` and `preview.png`. Both
are artwork rather than palette. Drop images into
`~/.config/omarchy/backgrounds/dracula/` and they are picked up without
touching this repository; the theme works without either.

## Editing it

Two things about this file are constrained rather than chosen, and both produce
a plausible-looking but wrong Dracula if changed.

**There is no `purple` key, and there must not be one.**
`omarchy-theme-color` aliases `magenta` from `purple`, so a `purple` entry would
become ANSI 5. Dracula puts pink at 5 and purple at 4 — which is what `magenta`
and `blue` say instead. Reading the palette back, `{{ purple }}` correctly
resolves to pink, because that is Omarchy's convention rather than Dracula's
naming.

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
