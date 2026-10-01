---
title: Downsampling k-space
---
## Motivations

Undersampling k-space is one of the main methods used to accelerate MRI acquisition, since fewer acquired lines means less scan time, which matters directly for patient comfort. However, skipping lines will create artefacts, so it is important to build intuition for how the choice of downsampling factor and strategy (skipping vs. zero filling) trades off. 

## The demonstration

The k-space sampling interval sets the field of view, and the extent of k-space sets the
resolution:

$$
\mathrm{FOV} = \frac{1}{\Delta k}, \qquad \Delta x = \frac{1}{N\,\Delta k}
$$ (eqFOV)

In this demo k-space is downsampled by a factor $R$ along either the phase-encode or the
frequency-encode direction. Two ways of doing this are compared: removing every $R$-th
line, or zero filling the outer k-space.

### Removing lines

To downsample by skipping, only one line out of every $R$ is kept, which multiplies the
effective sample spacing by $R$: $\Delta k_\mathrm{eff} = R\, \Delta k$. By [](#eqFOV), the
FOV shrinks by the same factor $R$. Anatomy that was inside the original FOV but now falls
outside the new, smaller one wraps back around into the image, which is called aliasing.

### Zero filling

As in [](./01-central-mask.md), the removed k-space can instead be zero filled: the array
size stays the same, so by [](#eqFOV) the FOV should be unchanged and I think that no aliasing occurs. 

## Interactive exploration

:::{figure} #figDownsample
:label: downsampleFig
Downsampling the phase-encode direction by a factor $R$ (slider). Top row: removing every
$R$-th PE line. Bottom row: keeping the same lines but zeroing the skipped ones
in place instead. Each row shows, left to right, the resulting k-space (log magnitude),
the reconstructed magnitude image, and the difference from the full-k-space reference.
:::

:::{tip} Try this
Drag the slider and watch both rows at once. Compare the two difference panels at the
same $R$: are they the same, or does one look worse than the other?
:::

## What the interactivity reveals

:::{attention} Observations
Contrary to my initial belief that zero-filling the missing k-space lines would prevent aliasing, it seems that aliasing still occurs. In fact we can see that the FOV stays the same, but a wrapped-around copy of the brain appears anyway.
We also clearly see that the number of ghosts appearing matches the downsampling factor $R$: removing every $R$-th line produces $R$ overlapping copies of the brain in the image, however it is hard to tell for the "removing" example if this follows the same rule.
Interestingly, removing the k-space lines (rather than zero-filling them) creates a new artefact on top of the aliasing. Since the remaining lines are packed together, their spacing is no longer $\Delta k$ but $R\,\Delta k$, so the IFFT has no way of knowing that these samples used to sit further apart. This difference is what gives the "remove" example a different, more irregular-looking image compared to the zero-fill example, even though both uses the same ratio.
We can also see the zero filling reduce the intensity of the image si it removes spatial frequencies.
:::

## Can we fix it?
In reality to use downsampling we need to use special reconstructing technique that uses phased coil arrays to remove the aliasing present. Methods such as GRAPPA can effectively do this, which allow for accelerated imaging. 