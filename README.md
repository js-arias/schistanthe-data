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

Key | Weight | Environment
--- | ------ | -----------
  1 |  0.001 | oceanic plateaus
  2 |  0.005 | continental shelf
  3 |  1.000 | lowlands
  4 |  1.000 | highlands
  5 |  0.001 | ice sheets

- `landscape-key.tab`:
  This file contains the keys for the landscape features
  of the paleolandscape model.
- `model-pix-weights.tab`:
  This file contains the definition of the pixel priors
  used in the analysis.

### Project

- `project-360.tab`:
This file contains the list of data files used by `PhyGeo`
to perform an analysis.

### Inference

Maximum likelihood reconstruction
was done with the command `diff ml` in `PhyGeo`.

## Results

### Maximum likelihood

The maximum likelihood estimation
of the concentration parameter was 99.21875
(log Likelihood = -376.549583).

```bash
# maximum likelihood estimation
phygeo diff ml project-360.tab

# conditional likelihoods for pixels at each node
phygeo diff like -lambda 99.21875 -o ml project-360.tab
```

Unfortunately,
the file with the conditional likelihoods for each node
(called `ml-project-360.tab-vireya-99.218750-down.tab`)
is too large for this repository
(more than 250 mb),
so you must run `phygeo diff like`
to get this file.

With the conditional likelihoods
we can make an estimate of the ancestral locations
using an approximation of the stochastic mapping.
Due to the large size of the resulting file
(>50 mb),
I put an example file in this repository
(`pp-1k-vireya-99.218750x1000.tab`) using only 1000 particles,
but all posterior analyzes were run with the stochastic mapping
using 10000 particles
(`pp-ml-vireya-99.218750x10000.tab`).

```bash
# stochastic mapping
phygeo diff particles -p 10000 -i ml-project-360.tab-vireya-99.218750-down.tab -o pp-ml project-360.tab
```

- `pp-1k-vireya-99.218750x1000.tab`:
  This file contains the stochastic mapping of 1000 particles
  in the maximum likelihood reconstruction.

Most of the time,
we are interested on the reconstructions from this stochastic histories.
while we can use the raw frequency of the particles at each node,
it is preferable to smooth the results
using an spherical KDE.
Both options can be done with the command `phygeo diff freq`.
Here I use a KDE with a lambda of 1000,
that means that the final distribution will be quite concentrated
and more similar to the using of the raw frequencies.

```bash
# KDE with lambda 1000
phygeo diff freq -kde 1000 -i pp-ml-vireya-99.218750x10000.tab -o kde project-360.tab
```

- `kde-project-360.tab-pp-ml-vireya-99.218750x10000.tab.tab`
  This file contains the frequency of the stochastic maps
  smoothed with a spherical normal KDE using a lambda of 1000.

The 95% of the empirical posterior for each node
(i.e., the "up-pass" pixel probabilities conditioned by the maximum likelihood estimation)
are stored as pictures in the following directories:

- `recs-95`:
  the reconstructions for each node.
- `unrot-95`:
  the reconstructions for each node, rotated to the current geography.
  We use a map with current borders for the reference.

The file `vireya-tree.svg` contains a tree with the node IDs.

```bash
# node reconstructions
phygeo diff map -c 1440 -key landscape-key.tab -gray -i kde-project-360.tab-pp-ml-vireya-99.218750x10000.tab.tab -o "recs-95/r" project-360.tab

# node reconstructions (unrotated)
phygeo diff map -c 1440 -key landscape-key.tab -gray -unrot -contour world_location_map-black_lines-1440.png -i kde-project-360.tab-pp-ml-vireya-99.218750x10000.tab.tab -o "unrot-95/u" project-360.tab 
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
