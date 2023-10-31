# W-Net: A Deep Model for Fully Unsupervised Image Segmentation

## Links
- [arxiv](https://arxiv.org/abs/1711.08506)
- [papers with code](https://paperswithcode.com/paper/w-net-a-deep-model-for-fully-unsupervised)
- [reproduction](https://gurudeep1998.medium.com/w-net-a-deep-model-for-fully-unsupervised-image-segmentation-reproduction-2651540eaed6)

## Model architecture

This paper proposes the W-Net architecture, so called because it consists of two back to back U-Nets.
It is essentially an autoencoder, where both the encoder and the decoder separately are U-Nets.
- $U_\text{enc}$: an encoder, $X \times Y \times F \rightarrow X \times Y \times K$
- $U_\text{dec}$: a decoder, $X \times Y \times K \rightarrow X \times Y \times F$

In our case, $F$, the number of features in the input, is one, and $K = 4$.
X, Y are however big we make our subvolumes, and we may have a Z as well.


The encoder directly outputs pixel-wise class probabilities for K classes.
The decoder takes this as input and tries to reconstruct the input image.

## Training procedure

They train this with two losses: the usual reconstruction loss used in autoencoders:
$J_\text{reconstr} =  \|X - U_\text{dec}(U_\text{enc}(X)) \|^2$
and a more complicated loss that represents some kind of (dis)similarity metric between different classes, the (soft) normalized cut loss, described in more detail below.
For now we can just call it $J_\text{Ncut}$.

For each batch of images, they first update only the encoder by minimizing this Ncut loss (as it only depends on the encoder.
Then, they update both the encoder and the decoder based on the reconstruction loss.

**Possible modification**: Here I think it may be interesting to add a third loss: the comparison to our pseudo-labels.
The contribution of this third loss could be large initially and then be scaled down as the model learns.


## Soft Normalized Cut

The issue here is how do we decide whether one segmentation is better than another, if we don't have labels?
The authors of this paper address this by converting the image to a graph, and turning to a concept in graph theory.

### From pixels to a graph

Every pixel becomes a node in the graph, and there are (undirected) edges with an associated weight $w(u, v)$ between every pair of nodes $u$ and $v$.
These weights are computed by a predefined fixed function of the node locations and values (i.e. in our case the greyvalues and the 3D locations). 
Equation (6) in the paper gives their specific choice for this.

So now we have a graph $G(V, E)$ with nodes $V$ and edges $E$. 

### graph cut
We want to split this up into $K$ different subgraphs.
First consider $K = 2$.

The concept from graph theory is a *cut*: for a partition of the nodes into two subsets $A$ and $B$, it is defined as:
$cut(A, B) = \sum_{u\in A} \sum_{v \in B} w(u, v)$,
that is, it is the sum of edge weights for edges from nodes in $A$ to nodes in $B$.

So this is a measure of similarity between A and B. The bigger the cut, the stronger they were connected.
The goal then is to minimize this cut, for which efficient algorithms exist.

### normalized cut

The issue with this is that it is biased towards making one of the sets, say $B$, very small. 
The less nodes in $B$, the less edges, and the less the edge weight will be.

To counteract this, the normalized cut was introduced in [this paper](https://people.eecs.berkeley.edu/~malik/papers/SM-ncut.pdf).
As the name suggests, it normalizes this cut, with the same sum over edge weights but now over *all* edges coming out of A and B.
Even though the formula is the same, this is called $\text{assoc}(A, V)$ rather than $\text{cut}(A, V)$, probably because these sets are now not disjoint.
To be precise, the normalized cut between $A$ and $B$ is:
$\text{Ncut}(A, b) = \frac{\text{cut}(A, B)}{\text{assoc}(A, V)} + \frac{\text{cut}(A, B)}{\text{assoc}(B, V)}$

### Final generalizations

This generalizes to $K$ classes as:

$\text{Ncut}_K(V) = \sum_{k=1}^K \frac{\text{cut}(A_k, V - A_k)}{\text{assoc}(A_k, V)}$

(where $V - A_k$ are all nodes that are not in the $kth$ partition $A_k$.)

This can be rewritten exactly as

$\text{Ncut}_K(V) = K -  \sum_{k=1}^K \frac{\text{assoc}(A_k, A_k)}{\text{assoc}(A_k, V)}$ .


Finally the encoder outputs class probabilities for each pixel. We can turn them into hard class assignments and apply this loss as is, but that won't be differentiable.

So there is a slight modification to the *soft* normalized cut, where in assoc we don't sum only over the nodes in the given set, but rather over all, multiplied by the probability of belonging to that set.
i.e.:

$\text{assoc}(A, B) = \sum_{u,v \in V} p(u \in A) w(u, v) p(v \in B)$ .

## Postprocessing

They perform two postprocessing steps, it seems only at inference time and not during training, but this is not entirely clear from the paper.

I haven't looked into this in detail, but roughly they are:
- sharpening of class edges
- merging similar regions

These parts may require the most modifications.
