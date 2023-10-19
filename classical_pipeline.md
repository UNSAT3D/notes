# Classical image analysis pipeline

## Overview
This analysis works on the 3D image (rather than 2D slices).
The code is all in Python and is available [here](https://github.com/FloAns/Rooted_Soil-Tomograph_Image-Processing)
The method is described in detail in [Floriana's PhD thesis](https://theses.hal.science/tel-03149877/).

## Steps
1. bilateral filter to sharpen image (alternative: NLM non local mean)
2. linear stretching: fixing both peaks at fixed grey values
3. extract soil particles:
	1. In the experiment, first the dry soil is weighed, before the plant is inserted and watered, so the total mass is known.
	2. From this (and the average soil density), the total amount of voxels that should be labeled as soil is known
	3. Soil is the lightest in the scans, so set a threshold on the grey value, above which everything is classified as soil
	4. Lower this threshold until the right amount of voxels are labeled as soil
4. extract root:
	1. Use a (mathematical, not learned) edge detector: the local 3d variance in some radius r (related to the particle size)
	2. From the edges, determine connected components
	3. Sort these by size
	4. The biggest one is background, the second biggest is the root
	5. increase root area by r/2
5. distinguish air vs water
	1. The amount of water added on day 0 is also measured
	2. As with the soil, from this the total amount of water voxels at day 0 is known.
	3. Water has the lowest grey values, so determine again a threshold grey value by increasing it until the amount of voxels below the threshold is correct
	4. On subsequent days, use the same threshold that was fixed on day 0, as the grey value of water doesn't change

The only quantitative checks that can be done are global: the total mass of soil, root and water, which is known at the start of the experiment.
