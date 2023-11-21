---
date: 2023-11-17
location: SURF, Amsterdam
project:
  - "[[UNSAT]]"
type:
  - meeting
present:
  - "[[Pablo Rodríguez]]"
  - "[[Aron Jansen]]"
  - "[[Monica Rotulo]]"
  - "[[Bryan Cardenas Guevara]]"
context:
  - Netherlands eScience Center
description: Talk with SURF researchers in medical imaging
tags:
  - meeting
---
## Notes

- Our potential collaborators in [[SURF]] work on medical imaging
- The first part of the meeting was devoted to give them more context
---
- We are interested in the possibility of using [W-NET](https://arxiv.org/abs/1711.08506)
	- Adding a supervised loss
- Bryan suggests starting with a U-NET
	- Input every slice
	- Get the computer-generated labels and use them as ground-truth
	- U-NET is very good at filtering out noise
-  He also recommends [DINO](https://arxiv.org/abs/2104.14294)
	- [Applied to hystopathology](https://github.com/mahmoodlab/HIPT)
- Patching and masking
	- Create a threshold for black
	- If the average is too high, reject
	- No patching may not be neccessary
		- The NN can learn the border
- Using polar coordinates is probably an overkill
- Group equivariant networks may make sense
	- But the advice is not to start directly with one
- Gravity breaks the symmetry in the vertical plane(s)
	- Consider using two networks
		- Vertical regular convolutional neural network
		- Horizontal equivariant neural network
	- Or taking cubes rather than slices