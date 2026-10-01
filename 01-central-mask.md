---
title: Masking the centre of k-space
---

## The demonstration
Kspace is a 2D frequency spectrum of an image.
Each data point of k-space represent one spatial frequency of the image. Samples near the centre describe slowly varying features, like the overall intensity and contrast between
tissues, while data points at the edges describe high frequency pattern like edges and fine details {cite:p}`Larson2023`.

In this demo a square mask is applied to keep a smaller area of the k-space and
reconstruct the image with an inverse FFT. The rest of the kspace can either be removed or filled with zeroes, this will create two different effects.

$$
M(k_x, k_y) =
\begin{cases}
1 & |k_x| \le a \ \text{and} \ |k_y| \le a \\
0 & \text{else}
\end{cases}
$$ (eqMask)
### Cropping

To crop k-space, the mask is applied directly and the values outside of it are simply
discarded, shrinking the array itself. This changes the image characteristics.

When we crop the outer half of k-space, keeping only a centre rectangle, we shrink the
extent of k-space. Since spatial resolution is set by the maximum extent of k-space,
cutting away the high spatial frequencies directly reduces that extent and therefore the
resolution {cite:p}`Larson2023`:

$$
\Delta x = \frac{1}{\gamma G_x T_\mathrm{read}}
$$ (eqResolution)

where $T_\mathrm{read}$ is the readout (DAQ) time.

The FOV, on the other hand, is set by the *spacing* between k-space samples, not by the
extent of k-space, so it is unaffected by cropping. This can be seen from
{cite:p}`Larson2023`:

$$
\mathrm{FOV}_{x,y} = N_{x,y}\, \Delta x_{x,y}
$$ (eqFOVmask)

Halving the number of samples $N_{x,y}$ while the resolution $\Delta x_{x,y}$ is also
halved leaves the FOV unchanged.

### Zero filling

Applying a mask with zero filling is equivalent to multiplying k-space by the mask, or, by
the convolution theorem, convolving the image with the inverse Fourier transform of the
mask:

$$
\hat{s}(x, y) = s(x, y) \circledast \mathcal{F}^{-1}\{M\}
$$ (eqMaskConv)

Unlike cropping, zero filling keeps the array size $N_{x,y}$ the same: the discarded
samples are set to zero instead of being removed. By [](#eqFOVmask), the FOV is therefore
unchanged, and so is the pixel grid — the image looks the same size and just as sharp at
first glance. But no new high-frequency information was added, so the *true* resolution,
set by the extent of non-zero k-space, is identical to the cropped case
{cite:p}`Larson2023`. What zero filling does add is the ringing described by
[](#eqMaskConv): because the mask fourrier transform to a cardinal sinus shape that is convolved with the image creating a visible
Gibbs artefact in the high frequency patterns.

## Interactive exploration

:::{figure} #figMask
:label: maskFig
Masking the centre of k-space with [](#eqMaskConv): the slider sets the fraction of
k-space kept by the mask, and the buttons switch between zero filling and cropping. Each
row of panels shows, from left to right, the masked k-space (log magnitude), the
reconstructed magnitude image, and the difference from the full-k-space reference image.
:::

:::{tip} Try this
Drag the slider to change the fraction of k-space kept by the mask. Then switch between
the "Zero filling" and "Cropping" buttons at the same fraction, and compare the image
size and the difference panel between the two.
:::

## What the interactivity reveals

:::{attention} TODO (your own observations)
Looking at the zero filling reconstruction one thing that strike me is that the edge of the brain contains much more of low frequency then I previously thought. It is still visible at around 20% of the kspace kept. Another interesting thing is how the Gibbs artefact manifest it self. At around 70-80% the sinc convulution starts being visible though the ondulation are at high frequency and very close together. As I decreased the kspace kept we actually see the ondulation getting bigger and slower meaning that the sinc artefact frequency may also be being slower or since we are cropping higher frequencies the covlution of the sinc generate at lower frequencies. 
:::

## Can we fix it?

:::{attention} TODO
Speculate: can the artefact be reduced retrospectively on this dataset? Would that work
in a real acquisition?
:::
