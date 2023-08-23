# Phylogenetic biogeography of *Schistanthe* section of *Rhododendron*

The section *Schistanthe* of *Rhododendron*
(Ericales, Magnoliophyta)
is a clade of about 300 species distributed mostly in Malesia
(Indonesia,
Malaysia,
the Philippines,
and Papua New Guinea),
and its phylogeny has been used to test different biogeographic methods.

This repository contains a phylogenetic biogeography
using the computer program [PhyGeo](https://github.com/js-arias/phygeo).

## Source data

### Geographic data model

The geographic data model is an equal area pixelation of the Earth,
with 360 pixels in the equatorial ring.

- `pixels-360.tab`:
  This file contains the pixel IDs
  and their associated geographic locations.

### Paleogeographic model

The plate motion model is Muller et al. (2022).
The paleolandscape model is based on an unrotated version
of Cao et al. (2017) for the 0-400 Ma period,
and an unrotated version of the PaleoMap model
(Scotese and Wright 2018),
for the period 405-540 Ma.
Then the pixels were rotated
using the Müller et al. (2022) plate motion model.

- `muller-motion-360-5.tab`:
  This file contains the pixelated version of the plate motion model,
  with e360 pixelation,
  and time slices for each 5 million years,
  from 600 Ma to present.
- `muller-landscape-cao-paleomap-360-5.tab`:
  This file contains the pixelated version of the paleolandscape model,
  with e360 pixelation,
  and time slices for each 5 million years,
  from 540 Ma to present.

### Phylogeny

The phylogenetic tree is the one
produced by Webb and Ree (2012),
but as the current age of the group is estimated to be
in the Miocene
(Goetsch et al 2011;
Mo et al 2022),
the length of the branches was re-scaled
to set the origin of the group at 12.3 Ma.

- `rhodo-tree-360.tab`:
  This file contains the phylogeny as a tab-delimited table.

### Distribution records

Specimen data were obtained from the maps of Brown et al. (2006)
and expanded with data from GBIF
(for *Rhododendron maddenii*)
as explained in Arias (2017).

- `rhodo-points-360.tab`:
  This file contains the pixel assignments
  for the whole collection of specimen data,
  using an e360 pixelation.

## Analysis

### Landscape

Key | Prior | Environment
--- | ----- | -----------
  1 | 0.001 | oceanic plateaus
  2 | 0.005 | continental shelf
  3 | 1.000 | lowlands
  4 | 1.000 | highlands
  5 | 0.001 | ice sheets

- `landscape-key.tab`:
  This file contains the keys for the landscape features
  of the paleolandscape model.
- `model-pix-prior.tab`:
  This file contains the definition of the pixel priors
  used in the analysis.

### Project

- `project-360.tab`:
This file contains the list of data files used by `PhyGeo`
to perform an analysis.

### Inference

Maximum likelihood reconstruction
was done with the command `diff ml` in `PhyGeo`.

Bayesian estimates were first approximated
by a simple integration of several values
of the concentration parameter,
assuming a uniform prior distribution.
Then,
the resulting posterior was approximated with a Gamma function.

## Results

### Maximum likelihood

The maximum likelihood estimation
of the concentration parameter was 150.0.

```bash
# maximum likelihood estimation
phygeo diff ml project-360.tab

# stochastic mapping
phygeo diff like -lambda 150 -o ml project-360.tab
```

- `ml-project-360.tab-vireya-150.000000x1000.tab`:
  This file contains the stochastic mapping of 1000 particles
  in the maximum likelihood reconstruction.

### Bayesian estimation

The posterior distribution of the concentration parameter,
using a uniform prior,
is approximated by a gamma function,
with $\alpha$ = 75.75, $\beta$ = 0.5.

```bash
# numerical integration
phygeo diff integrate -max 300 -parts 100 project-360.tab > vireya-integrate-360.tab

# sampling posterior for stochastic mapping
phygeo diff integrate -distribution "gamma=75.75,0.5" -p 100 -o sample project-360.tab
```

- `vireya-integrate-360.tab`:
  This file contains the likelihood values
  used to estimate the posterior distribution
  of the concentration parameter.
- `sample-project-360.tab-vireya-sampling-1000x100.zip`:
  This zip file contains the stochastic mappings
  of 1000 samples from the posterior,
  with 100 particles per sample.

The 95% of the posterior sample
(based on a spherical normal KDE,
using lambda 1000)
for each reconstruction is given in the following directories:

- `recs-95`:
  the reconstructions for each node.
- `unrot-95`:
  the reconstructions for each node, rotated to the current geography.

The file `vireya-tree.svg` contains a tree with the node IDs.

```bash
# node reconstructions
phygeo diff map -c 1440 -key landscape-key.tab -gray -kde 1000 -i sample-project.tab-vireya-sampling-1000x100.tab -o "recs-95/r" project-360.tab

# node reconstructions (unrotated)
phygeo diff map -c 1440 -key landscape-key.tab -gray -kde 1000 -i sample-project.tab-cireya-sampling-1000x100.tab -unrot -o "unrot-95/u" project-360.tab
```

## References

References are also available as BiBTeX in the file `biblio.bib`.

Arias, J. S.
(2017)
An event model for phylogenetic biogeography using explicitly geographical ranges.
Journal of Biogeography, 44, 2225-2235.
DOI: [10.1111/jbi.13024](https://doi.org/10.1111/jbi.13024).

Brown, G. K. et al.
(2006)
Historical biogeography of *Rhododendron* section *Vireya* and the Malesian Archipelago.
Journal of Biogeography, 33, 1929-1944.
DOI: [10.1111/j.1365-2699.2006.01548.x](https://doi.org/10.1111/j.1365-2699.2006.01548.x).

Cao, W. et al.
(2017)
Improving global paleogeography since the late Paleozoic using paleobiology.
Biogeosciences, 14, 5425-5439.
DOI: [10.5194/bg-14-5425-2017](https://doi.org/10.5194/bg-14-5425-2017).

Goetsch, L. A. et al.
(2011)
Major speciation accompanied the dispersal of Vireya Rhododendrons (Ericaceae, *Rhododendron* sect. *Schistanthe*) through the Malayan archipelago: evidence from nuclear gene sequences.
Taxon, 60, 1015-1028.
DOI: [10.1002/tax.604006](https://doi.org/10.1002/tax.604006).

Mo, Z.-Q. et al.
(2022)
Resolution, conflict and rate shifts: insights from a densely sampled plastome phylogeny for *Rhododendron* (Ericaceae).
Annals of Botany, 130, 687-701.
DOI: [10.1093/aob/mcac114](https://doi.org/10.1093/aob/mcac114).

Müller, R. D. et al.
(2022)
A tectonic-rules-based mantle reference frame since 1 billion years ago – implications for supercontinent cycles and plate–mantle system evolution.
Solid Earth, 12, 1127-1159.
DOI: [10.5194/se-13-1127-2022](https://doi.org/10.5194/se-13-1127-2022).

Scotese, C.S., Wrigth, N.
(2018)
PALEOMAP Paleodigital elevation models (PaleoDEMs) for Phanerozoic.
URL: <https://www.earthbyte.org/paleodem-resource-scotese-and-wright-2018/>.

Webb C. O., Ree, R. H.
(2012)
Historical Biogeography Inference in Malesia.
In: Gower et al. (Eds)
Biotic evolution and environmental change in Southeast Asia,
Cambridge, pp. 191-215.
