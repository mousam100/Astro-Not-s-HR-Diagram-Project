# Astro-Not's HR Diagram Project

Build a **Hertzsprung–Russell diagram** from real **ESA** star catalogues, then learn to
rebuild it with data from a different telescope.

The HR diagram plots stars by colour (temperature) against true brightness. When you do that
for thousands of real stars, they fall into clear families — the main sequence, giants, and
white dwarfs — and the whole story of how stars live and die becomes visible in a single plot.

This repository contains **two self-contained Jupyter notebooks** that each go from a raw
catalogue to an interactive HR diagram, an animation of stellar evolution, and a guided
project. They follow the same steps with two different missions, so you can build the diagram
once and then watch how it changes with the telescope:

| Notebook | Mission | Stars | Colour axis |
|----------|---------|-------|-------------|
| `hr_diagram_gaia.ipynb` | **Gaia** (EDR3) | ~1.8 billion (a chunk) | BP − RP |
| `hr_diagram_hipparcos.ipynb` | **Hipparcos** (Gaia's predecessor) | ~118,000 | B − V |

Run both and the difference is striking: Gaia's main sequence is razor-sharp, while
Hipparcos' is the same shape with more scatter — a direct view of 25 years of progress in
astrometry.

## What's inside

Each notebook walks through the same nine steps:

0. **Setup** — installs and imports everything needed
1. **Get the data** — downloads a real ESA catalogue (with an offline fallback so it always runs)
2. **Raw HR diagram** — colour vs. absolute magnitude, straight from the data
3. **Cleaned diagram** — keeping only stars with high-precision distances
4. **Temperature-coloured diagram** — main sequence, giants, and white dwarfs labelled
5. **Interactive playground** — sliders for distance and data-quality cuts
6. **Famous stars** — the Sun, Sirius, Betelgeuse, Rigel, and more, placed on the diagram
7. **Animation** — an MP4 of a Sun-like star evolving from main sequence to white dwarf
8. **Isochrones** — reading a star cluster's age from its turn-off point
9. **Challenge + project** — rebuild the diagram with data from another telescope

Every plot is interactive: zoom, pan, hover for star details, and drag sliders.

## Which notebook to start with

Start with **`hr_diagram_gaia.ipynb`** — it builds the diagram from scratch with the sharpest
data available. Then open **`hr_diagram_hipparcos.ipynb`**, which repeats the whole pipeline
on Hipparcos and ends by pointing back to Gaia for a side-by-side comparison. They're
independent, so either runs on its own.

## Running it

**Google Colab (easiest):** open a notebook in Colab and run the cells top to bottom. All
dependencies install from the first cell, and `ffmpeg` (needed for the animation) is already
available.

**Locally:** you need Python 3.9+ and Jupyter.

```bash
pip install numpy pandas plotly ipywidgets matplotlib astroquery
jupyter notebook            # then open either notebook
```

For the stellar-evolution animation in section 7, `ffmpeg` must be installed
(`apt-get install ffmpeg`, `brew install ffmpeg`, or `conda install ffmpeg`). If it isn't, the
notebook falls back to an in-browser animation automatically.

To run everything, choose **Run all** (or press **Shift+Enter** through each cell in order).

## The core idea

Three quantities from the catalogue are all you need:

| Quantity | What it tells you | Gaia column | Hipparcos column |
|----------|------------------|-------------|------------------|
| colour index | temperature (x-axis) | `bp_rp` (BP − RP) | `B-V` |
| apparent magnitude | how bright the star looks | `phot_g_mean_mag` | `Vmag` |
| parallax | distance, used to get true brightness | `parallax` | `Plx` |
| parallax error | data quality, for cuts | `parallax_error` | `e_Plx` |

The Hipparcos notebook renames its columns to match Gaia's (`Vmag → phot_g_mean_mag`,
`B-V → bp_rp`, `Plx → parallax`, `e_Plx → parallax_error`), so the rest of the pipeline is
identical across both notebooks.

Absolute (true) magnitude comes from apparent magnitude and parallax:

```
abs_mag = phot_g_mean_mag + 5 + 5 * log10(parallax / 1000)
```

Plot colour against `abs_mag`, flip the y-axis so bright stars sit on top, and the HR diagram
appears.

## Gaia vs. Hipparcos — what differs

Hipparcos is shallower and less precise than Gaia, so the Hipparcos notebook makes three small
changes:

- the colour axis is **B − V** rather than Gaia's BP − RP (they play the same role),
- temperature comes from a closed-form **B − V → Teff** relation (Ballesteros' formula),
- the default quality cut is loosened to **`parallax_error / parallax < 0.10`** (10%), versus
  Gaia's 5%, since Hipparcos parallaxes are less precise.

Everything else — the plots, the physics, the structure — is the same.

## The project

Each notebook ends with a guided project:

- **Gaia notebook** → rebuild the analysis with **Hipparcos** (which is exactly the second
  notebook in this repo).
- **Hipparcos notebook** → rebuild with the improved **Hipparcos-2** re-reduction
  (van Leeuwen 2007, catalogue `I/311/hip2`, smaller parallax errors), then overlay the
  cleaned Hipparcos and Gaia diagrams directly.

A manual CSV route via [VizieR](https://vizier.cds.unistra.fr) is included as a backup in case
`astroquery` can't reach the network.

## Data sources

- **Gaia** (EDR3) — [ESA Gaia Archive](https://gea.esac.esa.int/archive/)
- **Hipparcos** — catalogue `I/239/hip_main` via [VizieR](https://vizier.cds.unistra.fr) or `astroquery`
- **Hipparcos-2** (re-reduction) — catalogue `I/311/hip2`

## License

MIT — free to use, adapt, and share.
