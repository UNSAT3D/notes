# Data
We have two types of data: 3D experimental scans and 2D simulation data.
## Experimental

The experimental data are X-ray scans, 3D greyscale images (64k levels), with one value per voxel.
The sample contains:
- soil
- plant root
- water
- air

The scans consist of 7 days of growth of a plant, one scan per day.

The naming convention is (F/C)(D/L)M(#)
- F/C: fine or coarse soil
- D/L: dense or loose
- M: the type of plant, in our case always Maize
- #: A number identifying the specific sample

There are two types of soil for which we have such series of scans: 
- coarse: 2 series
    - The average particle diameter is 330 microns
- fine: 4 series
    - The average particle diameter is 1900 microns


Each 3D image 1600 2d slices of 650x650 pixels.
The slices are horizontal slices, ordered **from top to bottom**?

The resolution for horizontal slices is 80 micron per pixel.

**Total voxels** ~ 6x7x1600x650^2 = 28T,
Not all are usable as the scanning region is spherical, say 10T usable, still 500x the amount in the simulations

We have the possibility to generate about 2 more such series.

One scan is about 1.3Gb, for a total of 6*7*1.3 = 55Gb.
These scans are all downsampled by a factor of 2 in each(?) direction, so the originals are about 8 times as large.

The difference between air and water is the most difficult, thresholding doesn't work there, depending on the saturation level.
Coarse sand is more difficult than fine sand.

### Questions
- The ordering of horizontal slices is from top to bottom right? i.e. slice 1 is physically above slice 2?
- What is the resolution in the z direction?
- The readme of the published data says the pixel size is 60 micrometers rather than 80?


## Simulation
Lattice-Boltzmann-DEM simulations.
The data of interest is the final, equilibrated state of the evolution.

Simulations are done in 2D due to computational constraints.

There are no roots in this simulation, as they are too difficult to simulate.
Air is also difficult because of the large density ratio with water. Oil is simulated instead of air.
Soil grains are spherical in the simulation.

So here there are 3 classes:
- soil
- water
- oil ("air")

The data at each pixel is the water/"air" density ratio.

In principle there is more information per simulation, such as the pressure.

The physical variables in a simulation: 
- water saturation
- diffusivity of interface G: the higher the sharper interface

There are:
- 30 high resolution simulations, at 800x800 pixels
- 7 low resolution simulations, at  300-400x300-400 pixels
**Total pixels** ~ 30x800^2 + 7 x 400^2 = 20M 

The simulation output is very clean, there is no noise.
To segment the simulation output, thresholding would suffice.
