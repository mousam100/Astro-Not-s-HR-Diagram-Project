# Astro-Not's HR Diagram Project — Hipparcos Edition

Build a **Hertzsprung–Russell diagram** from real **ESA Hipparcos** data, then learn to
rebuild it with data from another telescope.

The HR diagram plots stars by colour (temperature) against true brightness. When you do that
for thousands of real stars, they fall into clear families — the main sequence, giants, and
white dwarfs — and the whole story of how stars live and die becomes visible in a single plot.

This repository is a single, self-contained Jupyter notebook that goes from a raw Hipparcos
catalogue to an interactive HR diagram, an animation of stellar evolution, and a guided
project to repeat the analysis with a different dataset. It mirrors the Gaia notebook
step-for-step, so you can compare ESA's first astrometry mission against its modern successor.

## What's inside

`hr_diagram_hipparcos.ipynb` walks through:

0. **Setup** — installs and imports everything needed
1. **Get the data** — downloads the real ESA Hipparcos catalogue via `astroquery` (with an offline fallback)
2. **Raw HR diagram** — colour vs. absolute magnitude, straight from the data
3. **Cleaned diagram** — keeping only stars with high-precision distances
4. **Temperature-coloured diagram** — main sequence, giants, and white dwarfs labelled
5. **Interactive playground** — sliders for distance and data-quality cuts
6. **Famous stars** — the Sun, Sirius, Betelgeuse, Rigel, and more, placed on the diagram
7. **Animation** — an MP4 of a Sun-like star evolving from main sequence to white dwarf
8. **Isochrones** — reading a star cluster's age from its turn-off point
9. **Challenge + project** — rebuild the diagram with the improved Hipparcos-2 re-reduction, and compare against Gaia

Every plot is interactive: zoom, pan, hover for star details, and drag sliders.

## Running it

**Google Colab (easiest):** open the notebook in Colab and run the cells top to bottom. All
dependencies install from the first cell, and `ffmpeg` (needed for the animation) is already
available.

**Locally:** you need Python 3.9+ and Jupyter.

```bash
pip install numpy pandas plotly ipywidgets matplotlib astroquery
jupyter notebook hr_diagram_hipparcos.ipynb
```

For the stellar-evolution animation in section 7, `ffmpeg` must be installed
(`apt-get install ffmpeg`, `brew install ffmpeg`, or `conda install ffmpeg`). If it isn't, the
notebook falls back to an in-browser animation automatically.

To run everything, choose **Run all** (or press **Shift+Enter** through each cell in order).

## The core idea

Three quantities from the catalogue are all you need:

| Quantity | What it tells you | Hipparcos column | (Gaia equivalent) |
|----------|------------------|------------------|-------------------|
| colour index (B − V) | temperature (x-axis) | `B-V` | `bp_rp` |
| apparent magnitude | how bright the star looks | `Vmag` | `phot_g_mean_mag` |
| parallax | distance, used to get true brightness | `Plx` | `parallax` |

The notebook renames Hipparcos' columns to match the Gaia pipeline (`Vmag → phot_g_mean_mag`,
`B-V → bp_rp`, `Plx → parallax`, `e_Plx → parallax_error`), so the rest of the code carries
over unchanged.

Absolute (true) magnitude comes from apparent magnitude and parallax:

```
abs_mag = phot_g_mean_mag + 5 + 5 * log10(parallax / 1000)
```

Plot `bp_rp` (here, B − V) against `abs_mag`, flip the y-axis so bright stars sit on top, and
the HR diagram appears.

## A note on Hipparcos vs. Gaia

Hipparcos is shallower and less precise than Gaia, so a couple of values differ from the Gaia
notebook:

- the colour axis is **B − V** rather than Gaia's BP − RP (they play the same role),
- temperature comes from a closed-form **B − V → Teff** relation (Ballesteros' formula),
- the default quality cut is loosened to **`parallax_error / parallax < 0.10`** (10%), since
  Hipparcos parallaxes are less precise than Gaia's 5%.

Run both notebooks and the difference is striking: Gaia's main sequence is razor-sharp, while
Hipparcos' is the same shape with more scatter — a direct view of 25 years of progress in
astrometry.

## The project

The final section rebuilds the analysis with the improved **Hipparcos-2** re-reduction
(van Leeuwen 2007, catalogue `I/311/hip2`), which has substantially smaller parallax errors,
and points you toward overlaying the cleaned Hipparcos and Gaia diagrams directly. A manual
CSV route via [VizieR](https://vizier.cds.unistra.fr) is included as a backup.

## Data sources

- **Hipparcos** — catalogue `I/239/hip_main` via [VizieR](https://vizier.cds.unistra.fr) or `astroquery`
- **Hipparcos-2** (re-reduction) — catalogue `I/311/hip2`
- **Gaia** (EDR3, for comparison) — [ESA Gaia Archive](https://gea.esac.esa.int/archive/)

## License

MIT — free to use, adapt, and share.
