# The product's visuals

Server Edition ships an emerald visual set under the same
`AGPL-3.0-or-later` as the code: a wallpaper, a mark and a GRUB image. The SVG
sources are next to the rendered PNGs.

The visuals the product applies are not here: they ship inside their roles
(`roles/plymouth/files/theme/`, `roles/branding/files/server-grub.png` and its
SVG source), because `galaxy.yml` keeps `assets/` out of the collection and a
file read from there would be missing once the collection is consumed. The files below are not installed by any role: a site that wants them
copies them, or declares its own paths; the product names the machine through
`identity_name`, not through an image.

| File | Size | Use |
|---|---|---|
| `server-wallpaper.svg` / `server-wallpaper.png` | 3840×2160 | a desktop wallpaper |
| `server-logo.svg` | 512×512 | the vector source |
| `server-logo-64.png` | 64×64 | a login mark, rendered from the SVG |
| `transparent.png` | 1×1 | a neutral placeholder |

The emerald palette is the product's default. A fork or a site that wants its
own marks replaces these files or points its own roles at its own paths, and
keeps its own licence for them.
