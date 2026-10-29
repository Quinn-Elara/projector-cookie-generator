# Photometric Cookie Generator

**[Try the live demo](https://quinn-elara.github.io/projector-cookie-generator/)**

Generate a projector cookie (gobo) texture from a photometric file, for use with projection lamps in 3D modelling software and game engines.

Drop in an **IES** or **LDT** file and get a PNG texture that reproduces the lamp's real-world light distribution. The tool also reports the lamp's beam angles and the projector field of view you need to set in your engine.

Everything runs locally in your browser. No files are uploaded anywhere.

## Demo files

You can find some example photometric files using the links below.

 - **[Pixar RenderMan Library](https://renderman.pixar.com/ies-profiles)** by Dylan Sisson and Leif Pedersen.

## Features

- **IES and LDT support**: parses `.ies` and `.ldt` (EULUMDAT) files, including LDT symmetry modes.
- **Three texture types**
  - *Cookie / gobo*: greyscale projection texture.
  - *Equirectangular panorama*: the full distribution mapped to a panorama.
  - *Full colour*: adds colour temperature and lens effects.
- **Output resolution**: 128 to 2048 px square.
- **Live preview** on light and dark backgrounds.
- **Base controls**
  - Gamma (default 2.2, matching the sRGB encoding Second Life expects)
  - Auto-scale beam to fill the texture, with an adjustable beam edge (% of peak intensity)
  - Soft edge falloff with adjustable width (on by default)
  - Gradient noise dithering to reduce banding
- **Lens effects** (full colour mode): colour temperature (1800–8000 K), chromatic aberration, vignette, lens dirt/dust, edge fringe glow, Fresnel rings, and haze. Presets: None, Fresnel, Flat / PC, PAR / reflector, Vintage / hazy.
- **Lamp data readout**: peak candela, angle counts, beam angle (50% of peak), field angle (10% of peak), projector FOV (radians and degrees), watts, colour temperature, CRI, lumen flux, and luminaire/lamp/manufacturer info where the file provides it.
- **Second Life spotlight parameters**: suggested radius, falloff, and intensity from your wall distance and clip depth.
- **Descriptive filenames**: the download is named from the manufacturer, lamp, and projector FOV, e.g. `Maker-Lamp_FOV-1p2345_cookie.png`.

## Usage

1. Open the [live demo](https://quinn-elara.github.io/projector-cookie-generator/), or open `index.html` locally in any modern browser.
2. Drag and drop an `.ies` or `.ldt` file onto the drop zone, or click to browse.
3. Choose a resolution and texture type, then adjust the settings.
4. Check the preview and the lamp data panel.
5. Click **Download PNG**.

Use the **Reset** button to clear the current file and start again.

### Running locally

No install or build step is needed. Clone the repo and open the file:

```bash
git clone https://github.com/Quinn-Elara/projector-cookie-generator.git
cd projector-cookie-generator
# then open index.html in your browser
```

### Setting up the projector in Second Life

Enter the **Projector FOV** reported by the tool (in radians) in the **FOV** box on the Features tab of the build tools. Second Life accepts 0 to 3 radians with three decimals, so the reported value is already rounded to three decimals and capped at 3.0 (about 172°). It is also appended to the filename.

- Use a prim with **equal X and Y scale**. The viewer uses the X/Y ratio as the projector's aspect ratio, so a non-square prim stretches the texture.
- Second Life reads the texture as **sRGB** and converts it to linear light in its shader. The default **Gamma** of 2.2 approximates sRGB encoding, so the projected light is proportional to the photometric data. A gamma of 1.0 would give a darker, higher-contrast result.
- When **Auto-scale beam to fill texture** is on (the default), the FOV is chosen so the lamp's pattern fills the texture. When it is off, the widest FOV Second Life allows (3.0 rad) is used, which gives a small bright area for wide lamps.
- **Check the shape for each lamp.** A lamp's pattern can look different at different intensity levels, so the **Beam edge** setting changes the beam's shape. Louvered, gridded or shuttered fixtures often look squarer at higher values than at lower values. The tool does not choose this for you, so check the preview and adjust it for each lamp.

### Second Life parameters

Set **Distance to wall** and **Wall clip depth** to see suggested Second Life spotlight values.

- **Distance to wall** is how far the projector is from the surface it lights.
- **Wall clip depth** is how far the light radius extends past the wall. A smaller value is safer for thin walls but dimmer.

The tool calculates radius, falloff, and intensity so wall brightness approximates the real lamp. It warns you if the lamp is too weak for falloff alone to compensate, or if the radius hits Second Life's 20 m limit.

## How it works

The photometric data is mapped onto the square texture the way Second Life projects it. The viewer uses a perspective frustum, so a texel's distance from the centre is proportional to the tangent of its angle from the lamp's axis, not to the angle itself. Straight lines stay straight under this projection, so the beam keeps its shape: a square beam, such as a shuttered profile spot, produces a square pattern in the texture, and a round lamp produces a round one. Nothing in the tool imposes a shape of its own. The edge falloff works on intensity rather than position: light at or above the **Beam edge** level (a percentage of peak, 50% by default) is kept exactly, and below it the light fades to nothing at a lower level set by **Falloff width**. Because the fade follows the lamp's own contours, the texture ends in black without changing the beam's shape. Auto-scale fits the level where the fade reaches zero into the frame, and a thin border is always black, because a lit edge would make the projection end in a hard square. Intensities are normalised to the peak candela value and gamma-encoded before export.

A single perspective projector cannot cover a whole hemisphere. For very wide lamps the outermost few degrees are cropped by Second Life's 3.0 rad FOV limit, and the centre of the beam gets relatively few pixels, so a larger output resolution helps.

## Supported formats

| Format | Extension | Notes |
|--------|-----------|-------|
| IES | `.ies` | Standard IESNA LM-63 files; tilt data is skipped, not applied |
| EULUMDAT | `.ldt` | Symmetry modes 0–4 supported |

Files are validated on load, and malformed input (truncated data, unsorted angles, implausible angle counts, unsupported extensions) is rejected with an error message.

## Browser support

Any modern browser with HTML5 canvas and the File API (current Chrome, Firefox, Edge, Safari).

## Deployment

The live demo is deployed by GitHub Actions (`.github/workflows/pages.yml`) on every push to `main`. The workflow stamps the commit hash into the page footer, which links to that commit. Local copies show `dev` instead.

## Contributing

Issues and pull requests are welcome. Bug reports are most useful with the IES/LDT file attached, if you're able to share it.

## License

Licensed under the [GPL-3.0](LICENSE) license.
