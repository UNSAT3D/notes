# Plan

Although the existing classical image analysis pipeline is fast and automated (or automatable at least), it is expected not to generalise well to other soil types.
The hope is that a deep learning model will generalise better.

There are two approaches we can take, the difference boils down to using simulation data (plan A) or only using experimental data (plan B).


## Tradeoffs
| x | plan A | plan B |
| -- | -- | -- |
| pros | have labels (so supervised) | 500x the data |
| cons | generalisation issues | no labels (so unsupervised) |

### Generalisation Issues
|simulation | experimental |
| -- | -- |
| - | root |
| oil | air |
| clean | noisy |
| 2D | 3D |
| spheres | irregular shapes |
| model | nature |

### Labels
For the simulation data, scaled to range from 0 to 1, an accurate segmentation is simply:

| value | class |
| -- | -- |
| 1 | soil |
|>0.95 | water |
| < 0.95 | air |

For the experimental data, we only have the labels from the classical pipeline.
Training on those doesn't seem useful as we want the model to generalise better.
They can potentially be used as an aid in the unsupervised training.

## Plan A
This is the plan from the proposal, already adapted a little.

1. Add noise to the 2D simulation data.
2. Train a 2D U-Net to segment the noisy simulation data, into soil, water, and air, using the labels trivially obtained from the noise free simulation data.
3. Keep using the existing pipeline to find the root 
4. In the non-root region, apply the model trained on simulation to segment the soil, water, and air, 2D slice by 2D slice

## Plan B
1. Use the experimental data to train an unsupervised deep learning model.
2. Apply it directly to new experimental data.
