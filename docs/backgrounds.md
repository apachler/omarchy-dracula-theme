# Generating backgrounds for this theme

A spec for whatever generates the wallpapers — an image model, a designer, or
you. Every number here came from measuring Omarchy's own first-party
backgrounds and this theme's palette, not from taste.

## Hard requirements

| | Value | Why |
| --- | --- | --- |
| **Resolution** | **3840×2160 minimum** | Omarchy's own backgrounds run 3840×2160 to 7680×3215. A wallpaper is the one image viewed full-screen at 100%, so anything smaller is upscaled by the compositor and looks soft. 1920×1080 is the floor below which it is visible on any modern laptop |
| **Aspect** | 16:9 (1.7778) | Optionally a second file at 21:9 (3440×1440) for ultrawides, as first-party themes do |
| **Format** | WebP | Generate to **PNG**, convert to WebP. Never generate straight to JPEG — a dark gradient re-encoded twice bands |
| **Quality** | q90, method 6 | q80 (what the *themes-page screenshot* asks for) bands visibly on dark gradients. q90 is roughly 1.5× the size and clean |
| **File size** | under 700KB; 1.5MB ceiling | The first-party set runs 180–640KB at 4K |
| **Colour depth** | 8-bit RGB, no alpha | |

Do not upscale a smaller image to meet the resolution. It adds bytes and no
detail, and it is visible.

## Palette

The field — the large flat area that most of the image is — must sit in
Dracula's own background family:

- **Hue 230–250°.** Dracula's `background` is `#282a36`, which is hue 231.
- **Value 8–15%.** Darker than the UI background on purpose: panels sit on top
  at 96% alpha and need to read against it.
- **Saturation 40–60%.** More saturated than the UI, which keeps a wallpaper
  from looking like a dead grey field.

Accents come from the palette and nowhere else:

```
purple  #bd93f9   (H 265)   the theme accent — the one to lean on
pink    #ff79c6   (H 326)
cyan    #8be9fd   (H 190)
green   #50fa7b   yellow #f1fa8c   orange #ffb86c   red #ff5555
```

Off-palette accents are the most common failure. Two from the current set, for
calibration: a teal at H 184 (against Dracula's cyan at 190) and an electric
violet at H 287 (against purple at 265). Neither is wrong on a wallpaper, but
they will not match window borders or panel chrome, so keep them incidental
rather than the subject.

## Composition

- **Keep one third of the frame quiet.** Something has to be legible behind
  desktop clutter.
- **Keep the top and bottom edges calm.** The Quickshell bar can be dragged to
  any screen edge, and a busy strip under it makes the widgets hard to read.
- **Weight the detail to one side.** Off-centre reads better than a centred
  subject, which competes with centred panels and the launcher.
- **No text, no logos, no wordmarks, no watermarks, no signature.** A brand
  mark in a public MIT repository is a trademark problem, not a style choice.
- **Nothing that needs a licence you do not hold.** You are publishing this.

## Naming

```
backgrounds/0-castle.webp
backgrounds/1-portal.webp
backgrounds/2-sigil.webp
```

`omarchy-theme-set` sorts the directory and takes the first entry as the
default when the current wallpaper is not one of the theme's own, so the number
prefix decides what a new user sees first. Name the rest for what they show.

## A prompt to start from

> A 4K desktop wallpaper, 3840×2160, 16:9. Dark background in deep blue-violet
> (#191a21 to #282a36), hue around 235, low brightness. [SUBJECT] positioned
> off-centre toward the right third, leaving the left half quiet and near-empty.
> Accent lighting in Dracula purple #bd93f9 and pink #ff79c6, with sparing cyan
> #8be9fd highlights. Smooth gradients, no banding, no text, no logos, no
> watermark. Calm, uncluttered top and bottom edges.

Swap `[SUBJECT]` and keep the rest. Ask for PNG output at full resolution.

## Converting

With ImageMagick:

```sh
magick input.png -strip -quality 90 -define webp:method=6 backgrounds/N-name.webp
```

Without it, Pillow does the same:

```python
from PIL import Image
Image.open("input.png").convert("RGB").save(
    "backgrounds/N-name.webp", "WEBP", quality=90, method=6)
```

`-strip` matters: camera and generator metadata is dead weight in a wallpaper,
and can carry more about where the file came from than you intend to publish.

## Checking a candidate

```sh
python3 - <<'PY'
from PIL import Image
import colorsys, os, sys
f = sys.argv[1] if len(sys.argv) > 1 else "input.png"
im = Image.open(f).convert("RGB"); w, h = im.size
r, g, b = im.load()[3, 3]
hh, ss, vv = colorsys.rgb_to_hsv(r/255, g/255, b/255)
print(f"{w}x{h}  ratio={w/h:.4f}  {os.path.getsize(f)/1024:.0f}KB")
print(f"field #{r:02x}{g:02x}{b:02x}  H={hh*360:.0f} S={ss*100:.0f} V={vv*100:.0f}")
print("resolution:", "ok" if w >= 3840 else "TOO SMALL — do not upscale, regenerate")
print("hue:", "ok" if 225 <= hh*360 <= 255 else "OUT OF FAMILY")
PY
```
