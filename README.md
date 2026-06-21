# Astro-Not's HR Diagram Project

Build a **Hertzsprung–Russell diagram** from real **ESA Gaia** data, then learn to rebuild it
with data from another telescope.

The HR diagram plots stars by colour (temperature) against true brightness. When you do that
for thousands of real stars, they fall into clear families — the main sequence, giants, and
white dwarfs — and the whole story of how stars live and die becomes visible in a single plot.

This repository is a single, self-contained Jupyter notebook that goes from a raw Gaia
catalogue to an interactive HR diagram, an animation of stellar evolution, and a guided
project to repeat the analysis with a different dataset.

## What's inside

`hr_diagram_gaia.ipynb` walks through:

0. **Setup** — installs and imports everything needed
1. **Get the data** — downloads a real chunk of the ESA Gaia archive (with an offline fallback)
2. **Raw HR diagram** — colour vs. absolute magnitude, straight from the data
3. **Cleaned diagram** — keeping only stars with high-precision distances
4. **Temperature-coloured diagram** — main sequence, giants, and white dwarfs labelled
5. **Interactive playground** — sliders for distance and data-quality cuts
6. **Famous stars** — the Sun, Sirius, Betelgeuse, Rigel, and more, placed on the diagram
7. **Animation** — an MP4 of a Sun-like star evolving from main sequence to white dwarf
8. **Isochrones** — reading a star cluster's age from its turn-off point
9. **Challenge + project** — rebuild the diagram using data from another telescope (Hipparcos)

Every plot is interactive: zoom, pan, hover for star details, and drag sliders.

## Running it

**Google Colab (easiest):** open the notebook in Colab and run the cells top to bottom. All
dependencies install from the first cell, and `ffmpeg` (needed for the animation) is already
available.

**Locally:** you need Python 3.9+ and Jupyter.

```bash
pip install numpy pandas plotly ipywidgets matplotlib astroquery
jupyter notebook hr_diagram_gaia.ipynb
```

For the stellar-evolution animation in section 7, `ffmpeg` must be installed
(`apt-get install ffmpeg`, `brew install ffmpeg`, or `conda install ffmpeg`). If it isn't, the
notebook falls back to an in-browser animation automatically.

To run everything, choose **Run all** (or press **Shift+Enter** through each cell in order).

## The core idea

Three quantities from the catalogue are all you need:

| Quantity | What it tells you | Gaia column |
|----------|------------------|-------------|
| colour index (BP − RP) | temperature (x-axis) | `bp_rp` |
| apparent magnitude | how bright the star looks | `phot_g_mean_mag` |
| parallax | distance, used to get true brightness | `parallax` |

Absolute (true) magnitude comes from apparent magnitude and parallax:

```
abs_mag = phot_g_mean_mag + 5 + 5 * log10(parallax / 1000)
```

Plot `bp_rp` against `abs_mag`, flip the y-axis so bright stars sit on top, and the HR diagram
appears.

## The project

The final section rebuilds the same analysis with data from **ESA's Hipparcos mission**
(Gaia's predecessor, ~118,000 stars), pulled live with `astroquery`. The notebook handles the
download and renames Hipparcos' columns to match Gaia's, so the rest of the pipeline carries
over with minimal changes. A manual CSV route via [VizieR](https://vizier.cds.unistra.fr) is
included as a backup.

## Data sources

- **Gaia** (EDR3) — [ESA Gaia Archive](https://gea.esac.esa.int/archive/)
- **Hipparcos** — catalogue `I/239` via [VizieR](https://vizier.cds.unistra.fr) or `astroquery`

## License

MIT — free to use, adapt, and share.
