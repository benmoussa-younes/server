# The product's visuals

Server Edition ships an optional emerald visual set under the same
`AGPL-3.0-or-later` as the code: a wallpaper, a mark and a GRUB image. The SVG
sources are next to the rendered PNGs.

No role installs these files. A site that wants them copies them, or declares
its own paths; the product names the machine through `identity_name`, not
through an image.

| File | Size | Use |
|---|---|---|
| `server-wallpaper.svg` / `server-wallpaper.png` | 3840×2160 | a desktop wallpaper |
| `server-logo.svg` | 512×512 | the vector source |
| `server-logo-64.png` | 64×64 | a login mark, rendered from the SVG |
| `server-grub.svg` / `server-grub.png` | 1920×1080 | a boot-menu image |
| `transparent.png` | 1×1 | a neutral placeholder |

The emerald palette is the product's default. A fork or a site that wants its
own marks replaces these files or points its own roles at its own paths, and
keeps its own licence for them.
