---
title: Downsampling k-space
---
## Motivations

Undersampling k-space is one of the main methods used to accelerate MRI acquisition, since fewer acquired lines means a lower scan time. This is useful since MRI scans are lengthy which matters directly for patient comfort. However, skipping K-space lines can create artefacts, so it is important to build intuition for how downsampling factor and method  trades off. 

## The background

The last demonstration showed us that the k-space sampling interval sets the field of view while the extent of k-space sets the resolution:

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
outside the new smaller one wraps back around into the image, which is called aliasing.

### Zero filling

As in [](./01-central-mask.md), the removed k-space can instead be zero filled: the array
size stays the same, so by [](#eqFOV) the FOV should be unchanged, aliasing might be prevented this way.

### Random line removal

Instead of keeping a regular pattern, we can also zero out lines at random while keeping
the same overall fraction $1/R$ of the lines. The sampling is then no longer periodic, so
the aliasing is no longer made of $R$ exact copies of the object. Instead, the
energy of the missing lines is spread over the whole image as incoherent, noise-like
artefacts. 

## Interactive exploration

:::{figure} #figDownsample
:label: downsampleFig
Downsampling the phase-encode direction (slider: percentage of PE lines removed). Top row:
the skipped PE lines are discarded and the array shrinks. Middle row: keeping the same lines
but zeroing the skipped ones in place instead. Bottom row: keeping the same number of lines,
but chosen at random (the central line $k=+/-5$ is always kept) and zeroing the others. Each
row shows, left to right, the resulting k-space (log magnitude), the reconstructed magnitude
image, and the difference from the full-k-space reference.
:::

:::{tip} Try this
Drag the slider and watch all three rows at once. Compare the three difference panels at the
same percentage of removed lines: are they the same, or does one look worse than the others?
In particular, does the random row still show distinct ghosts of the brain?
:::

## What the interactivity reveals

:::{attention} Observations
Contrary to my initial belief that zero-filling the missing k-space lines would prevent aliasing, it seems that aliasing still occurs. In fact we can see that the FOV stays the same, but a wrapped-around copy of the brain appears anyway. Which is kinda breaking my brain, since the k-space lines should still be at the same spacing. I believe that the effect is still present because a zero is not missing data for the inverse Fourier transform, it is a measurement stating that the amplitude at that spatial frequency is zero. Each phase-encode line corresponds to a stripe pattern across the image, and the image is the weighted sum of all these patterns. When only every second line is kept, only the even patterns remain, and all of them repeat every half FOV.
Interestingly, removing the k-space lines (rather than zero-filling them) creates a new artefact on top of the aliasing. Since the remaining lines are packed together, their spacing is no longer $\Delta k$ but $R\,\Delta k$, so the IFFT has no way of knowing that these samples used to sit further apart. This difference is what gives the "remove" example a different FOV, and more irregular-looking image compared to the zero-fill example, even though both uses the same ratio.
We can also see the zero filling reduce the intensity of the image as it removes spatial frequencies the image gets darker.
Random sampling removal also creat incoherent ghost, thought since the removal is done in one direction only and as line, the ghosting also only appears in the phase encoding direction and is moroe structured then I would have thought.
:::

## Can we fix it?
In reality to use downsampling we need to use special reconstructing technique that uses phased coil arrays to remove the aliasing present. Methods such as GRAPPA can effectively do this, which allow for accelerated imaging. If the sampling is pseudo random, compressed sensing could be use to reconstruct the image without aliasing. 