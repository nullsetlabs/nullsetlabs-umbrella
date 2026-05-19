# Null Set Labs Brand Assets

Drop the Gemini logo files into this folder using the filenames below. The website will pick them up automatically once they're wired into `index.html`.

## File naming convention

When you save the four Gemini variants out of the chat or out of Gemini directly, name them exactly:

- `logo-lockup-dark.png` (and `.svg` if you have it) — primary lockup, dark background, used in the website hero
- `logo-lockup-light.png` — lockup on cream/off-white background, for documents and letterhead
- `logo-glyph-only.png` (and `.svg`) — just the empty set glyph with the blue-to-copper slash, no text. For favicon, social avatars, app icons.
- `logo-wordmark-only.png` — NULL SET LABS wordmark + tagline, no glyph. For document headers and email signatures.

## What to ask Gemini for, if you don't have the high-res variants yet

> Please export each of the four logo variants as a separate high-resolution PNG file with a transparent background where possible. The glyph-only variant should also be exported as SVG. Target resolution: 2400 pixels on the longest dimension for each lockup variant; 1024 pixels for the glyph-only variant.

## Source files for reference

If you also want to keep the original Gemini exports (the grid image you generated, raw outputs, etc.), drop them in a `source/` subfolder inside this folder so the production files stay clean.

## Why these specific variants

- **Dark lockup** is the website hero and the social card OG image
- **Light lockup** is for printed materials, letterheads, and college application packets
- **Glyph-only** is the favicon (16/32/180 pixel sizes derive from this), the GitHub org avatar, and any social profile picture
- **Wordmark only** is the email signature and document header for cases where the glyph is too tall

## Once the files are here

Tell me they're saved and I'll swap out the current inline SVG glyph on the website with the production PNG, update the favicon, generate the Open Graph preview image, and wire the brand-assets folder into the build.
