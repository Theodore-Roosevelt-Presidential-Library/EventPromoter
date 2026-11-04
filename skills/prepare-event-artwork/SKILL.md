---
name: prepare-event-artwork
description: Find the real artwork for a Library event on trlibrary.com and size it correctly for each promotion channel. Use when an event listing needs a cover image, when asked to get event photos or graphics, or when a published listing is showing a generic default image instead of the event's own art. Includes the trick for recovering full-resolution originals from the site's thumbnails.
---

# Event artwork

Almost every event on trlibrary.com **already has real artwork.** The site just serves small
teaser crops, which is why it looks like there's nothing usable.

## Start from the feed

Every event node carries `image[]` with three renditions — `square__l`, `4_3__l`,
`16_9__l`. **Take `16_9__l`** as the starting point for a 1200×628 cover. No scraping needed.

## 🔑 Strip the Drupal image style for the original

Those renditions are resized WebP. For the full-resolution original, delete the style
segment from the URL:

```
rendition: /sites/default/files/styles/16_9__l/public/2026-08/FILE.png.webp?h=fa6f1cf3
original:  /sites/default/files/2026-08/FILE.png
```

⚠️ **All three edits are required, together:**

1. drop `styles/<style-name>/public/`
2. drop the trailing `.webp`
3. drop the query string

Do fewer and the site returns a **404 served as an HTML page** — and `curl -o out.jpg` will
cheerfully save that HTML as `out.jpg`. **Check what you got:**

```bash
curl -sL -o out.img -w "%{http_code} %{content_type}\n" "<original-url>"
file out.img      # must say PNG/JPEG, not HTML
```

This recovers **1920×1080 originals** from what look like small thumbnails. Try it before
concluding an event has no usable art — but fall back to the `16_9__l` rendition if the
strip 404s rather than shipping an HTML file.

## Sizes

| Channel | Size | Notes |
|---|---|---|
| **Facebook event cover** | **1200×628** | FB renders ~1200px wide; smaller upscales and goes soft |
| Google Business Profile | 1200×900 or 1200×628 | Manual upload — Claude can't automate it |
| Dakota Media Access bulletin | 16:9 landscape | Email to info@freetv.org |
| evvnt | any; 2:1 ideal | |

⚠️ **Don't go below ~1200px wide for anything Facebook-facing.** A 400px file gets upscaled
about 3× and text turns to mush — clearly visible on any graphic with type in it.

## Crop vs. pad — this is the judgment call

**Photographs → crop to fill.** Center-crop horizontally. When cropping vertically, bias
about 30% from the top so faces don't get decapitated.

**Logos and designed graphics → letterbox (pad), never crop.** Cropping a promotional graphic
slices off the date, venue and title — exactly the information the graphic exists to convey.

**Match the pad color to the art.** A logo PNG with a baked-in white background padded onto
black looks like a floating white box. Pad it on white instead. TRPL near-black `#111111`
works for art that's genuinely transparent or dark.

**Tall portraits into a wide banner:** cropping produces an extreme close-up of someone's
nose. Use a blurred copy of the image as the background and center the portrait on top:

```python
from PIL import Image, ImageFilter
bg = im.copy()  # crop to target ratio, resize, then:
bg = bg.resize((W,H), Image.LANCZOS).filter(ImageFilter.GaussianBlur(28))
fg = im.copy(); fg.thumbnail((W,H), Image.LANCZOS)
bg.paste(fg, ((W-fg.width)//2, (H-fg.height)//2))
```

## Recipe

```bash
curl -sL -o out.jpg "<original-url>"   # strip the /styles/.../public/ segment first
```

The site serves WebP, so convert before saving: `Image.open(f).convert('RGB').save(out,'JPEG',quality=92)`

## Where images can actually be uploaded

| Channel | Claude can upload? |
|---|---|
| **Facebook** | ✅ Yes — `file_upload` to the hidden file input. See `publish-facebook`. |
| Google Business Profile | ❌ Native OS picker, no DOM input |
| ND Tourism | ❌ Manual, via each event's Photos section |
| Dakota Media Access | ❌ Emailed to info@freetv.org — **draft it, don't send** |
| Prairie Public · Visit Dickinson · Watford City · Bismarck-Mandan CVB | ❓ Not established — work it out on first attempt and record the answer here |

Say so plainly rather than leaving the operator to discover a listing has no image.

---

**If this channel misbehaves** — a selector is gone, a form rejects valid input, or a
workaround documented above stops working — use `report-channel-issue` to file it (it checks
for an existing issue first). Platforms change constantly; the fix only happens if the
divergence gets captured.
