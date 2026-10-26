# Photometric Cookie Generator

**[Try the live demo](https://quinn-elara.github.io/projector-cookie-generator/)**

Generate a projector cookie (gobo) texture from a photometric file, for use with projection lamps in 3D modelling software and game engines.

Drop in an **IES** or **LDT** file and get a PNG texture that reproduces the lamp's real-world light distribution. The tool also reports the lamp's beam angles and the projector field of view you need to set in your engine.

Everything runs locally in your browser. No files are uploaded anywhere.

## Features

- **IES and LDT support**: parses `.ies` and `.ldt` (EULUMDAT) files, including LDT symmetry modes.
- **Three texture types**
  - *Cookie / gobo*: greyscale projection texture.
  - *Equirectangular panorama*: the full distribution mapped to a panorama.
  - *Full colour*: adds colour temperature and lens effects.
- **Output resolution**: 128 to 2048 px square.
- **Live preview** on light and dark backgrounds.
- **Base controls**
  - Gamma
  - Peak-candela normalisation
  - Auto-scale beam to fill the texture
  - Soft edge falloff with adjustable width
  - Blue noise dithering to reduce banding
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

### Setting up the projector in your engine

The tool reports a **Projector FOV** in radians and degrees, which is also appended to the filename. Enter this value as the projector's field of view in your engine so the cookie's scale matches the lamp's real distribution. When **Auto-scale beam to fill texture** is on, the reported FOV is adjusted to match.

### Second Life parameters

Set **Distance to wall** and **Wall clip depth** to see suggested Second Life spotlight values.

- **Distance to wall** is how far the projector is from the surface it lights.
- **Wall clip depth** is how far the light radius extends past the wall. A smaller value is safer for thin walls but dimmer.

The tool calculates radius, falloff, and intensity so wall brightness approximates the real lamp. It warns you if the lamp is too weak for falloff alone to compensate, or if the radius hits Second Life's 20 m limit.

## How it works

The photometric data is sampled over the 0–90° range from the lamp's axis and mapped radially onto a square texture, so the centre of the image is the beam axis and the edge is 90° off-axis. Intensities are normalised to the peak candela value and gamma-encoded before export.

## Supported formats

| Format | Extension | Notes |
|--------|-----------|-------|
| IES | `.ies` | Standard IESNA photometric files |
| EULUMDAT | `.ldt` | Symmetry modes 0–4 supported |

## Browser support

Any modern browser with HTML5 canvas and the File API (current Chrome, Firefox, Edge, Safari).

## Contributing

Issues and pull requests are welcome. Bug reports are most useful with the IES/LDT file attached, if you're able to share it.

## License

Licensed under the [GPL-3.0](LICENSE) license.
