---
title: Masking the centre of k-space
---
## Motivations

In practice, no MRI acquisition can sample an infinite k-space, so every real scan is a finite version of the underlying signal. Understanding what happens when only a portion of k-space is kept is essential to interpreting real images. This demo also looks at two ways you show a reduced k-space. Cropping by removing kspace lines and cropping using zero filling, which have very effects on FOV and resolution despite both discarding the same amount of data. 

## The background
Kspace is a 2D frequency spectrum of an image.
Each data point of k-space represent one spatial frequency of the image. Samples near the centre describe slowly varying features, like the overall intensity and contrast between
tissues, while data points at the edges describe high frequency pattern like edges and fine details {cite:p}`Larson2023`.

In this demo a square mask is applied to keep a smaller area of the k-space and
reconstruct the image with an inverse FFT. The rest of the kspace can either be removed or filled with zeroes, this will create two different effects.

$$
M_{zeroFill}(k_x, k_y) =
\begin{cases}
1 & |k_x| \le a \ \text{and} \ |k_y| \le a \\
0 & \text{else}
\end{cases}
$$ (eqMask)
$$
M_{crop}(k_x, k_y) =
\begin{cases}
1 & |k_x| \le a \ \text{and} \ |k_y| \le a \\
nan & \text{else}
\end{cases}
$$ (eqMask_nan)
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
Gibbs artefact.

## Interactive exploration

:::{figure} #figMask
:label: maskFig
Masking the centre of k-space with [](#eqMaskConv): the slider sets the fraction of
k-space kept by the mask. Each
row of panels shows, from left to right, the masked k-space (log magnitude), the
reconstructed magnitude image, and the difference from the full-k-space reference image.
:::

:::{tip} Try this
Drag the slider to change the fraction of k-space kept by the mask. Compare the images
size and the difference panel between the two.
:::

## What the interactivity reveals

:::{attention} Observations
Looking at the zero-filling reconstruction, one thing that strikes me is that the edge of the brain, which contains a small amount of fat is much more low frequency than I previously thought. It is still visible at around 20% of the k-space kept. Another interesting thing is how the Gibbs artefact manifests itself. At around 70-80% the sinc convolution starts being visible, though the undulations are at high frequency and very close together. As I decreased the k-space kept, we actually see the undulations getting bigger and slower, meaning that the sinc artefact frequency may also be getting slower, or, since we are cropping higher frequencies, the convolution of the sinc is generated on lower frequencies structure.
Regular cropping is the least interesting of the two demos, because I don't really see why this would be done for a real MRI acquisition. Zero-filling, corrected with an apodisation window, seems the far superior choice to me, since with only 20% removed, severe artefacts appear and the difference is much higher than with zero-filling. The only interesting artefact here is that streaks appear along the edges of the brain, as the reduced resolution spreads signal toward the sides of the image. I suppose these lines come from the reduction of resolution which spread intensity across pixel, but I'm not too sure.
:::

## Uses for real MRI sequence

In real MRI application cropping the kspace technicly always happens as it is impossible to sample the infinite kspace. In fact in a normal MRI image there should be some level of Gibbs artefact always present. In fact even at 100% of the kspace kept looking carefully we can kinda see some undulations present. This is  mitiguated by the fact that the intensity of high frequecy data point is much lower then lower frequency, which is hard to see when showing log(K-space). Cropping Kspace could also be used to reduce acquistion time, and therefore zero filling + appodisation should be added.
