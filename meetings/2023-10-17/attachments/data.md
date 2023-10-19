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

There are two types of soil for which we have such series of scans: 
- coarse: 2 series
- fine: 4 series

Each 3D image 1600 2d slices of resolution 650x650

**Total voxels** ~ 6x7x1600x650^2 = 28T,
Not all are usable as the scanning region is spherical, say 10T usable, still 500x the amount in the simulations

We have the possibility to generate about 2 more such series.

The difference between air and water is the most difficult, thresholding doesn't work there, depending on the saturation level.
Coarse sand is more difficult than fine sand.

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
