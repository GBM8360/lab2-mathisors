---
title: Head motion during the acquisition
---
## Motivations

Patient motion during acquisition is one of the most common sources of image degradation in clinical MRI, since even small movements partway through a scan can corrupt the final reconstruction in ways that are not obvious from looking at k-space alone. Real motion is also rarely a single steady drift in one direction: it can be more cyclical, like motion from by breathing. So this demo models translation as an oscillation rather than a one-off ramp to better reflect reality. Studying this simplified, line-by-line motion model is therefore a type of stepping stone to understanding real motion-correction used in clinical scanners.

## What we do

The acquisition is assumed to be made line by line, so if the patient moves partway through the scan, the lines acquired
before the motion still describe the old position while the lines acquired after it
describe the new one. The motion also is applied line per line instead of being instatanious The whole chapter is built around simulating this splice: take the
lines up to some `motion_start`, and replace the remaining lines with lines coming from a
moved version of the image.

The simplest version of "moved" is a pure in-plane translation. By the Fourier shift
theorem, shifting an image only multiplies its k-space by a linear phase ramp — the
*magnitude* of k-space is completely untouched:

$$
\rho'(\mathbf{r}) = \rho(\mathbf{r} - \mathbf{d})
\quad \Longleftrightarrow \quad
S'(\mathbf{k}) = S(\mathbf{k})\, e^{-2\pi i\, \mathbf{k}\cdot\mathbf{d}}
$$ (eqShift)

The first figure below uses exactly this, but rather than a one-off linear drift, the
displacement oscillates sinusoidally along the readout (frequency-encode) direction over
the course of the scan, line by line. Real patient motion is rarely a single steady drift
in one direction: it is much more often quasi-periodic, driven by breathing or the
heartbeat, so a cyclical translation is a more realistic stand-in for that kind of motion
than a constant-velocity ramp.

Rotation is the more interesting case for this chapter, because it is not just a phase
term — rotating an object rotates its k-space by the same angle, which means there is no
shortcut: to get the post-motion lines, the image itself has to be rotated and
re-sampled on the Cartesian grid:

$$
\rho'(\mathbf{r}) = \rho(R_\theta^{-1}\mathbf{r})
\quad \Longleftrightarrow \quad
S'(\mathbf{k}) = S(R_\theta^{-1}\mathbf{k})
$$ (eqRotation)

The base case required by the lab is a sudden 20° rotation near the centre of k-space
(the patient jerks, then holds still): everything before `motion_start` comes from the
original image, everything from `motion_start` onward comes from the image rotated by
20°. The extension explored here is a *continuous* rotation, ramped linearly between
`motion_start` and `motion_stop` instead of happening on a single line.

## Interactive exploration

:::{figure} #figMotionReadout
:label: motionReadoutFig
Cyclical translation along the readout (frequency-encode) direction (slider = oscillation
amplitude, in pixels), mimicking quasi-periodic motion such as breathing or the heartbeat.
Top row, left to right: the displacement profile over acquisition order, the resulting
phase added to each k-space sample ([](#eqShift)), and the k-space magnitude. Bottom row:
the reconstructed image with motion, next to the static reference.
:::

% TODO: once tagged, uncomment and add to myst.yml's toc with `hidden: true`.
% Reminder: ipywidgets sliders do not work on the static site, so precompute Plotly frames.
%
% :::{figure} #figMotionAngle
% :label: motionAngleFig
% TODO caption: slider = rotation angle, motion at a fixed line near the centre.
% :::
%
% :::{figure} #figMotionSweep
% :label: motionSweepFig
% TODO caption: image error as a function of where the motion window sits in k-space.
% :::

:::{tip} Try this
Drag the slider from 0 up to its maximum. Watch the k-space magnitude panel: it never
changes, exactly as [](#eqShift) predicts. All the information about the motion is
instead packed into the phase panel, where it shows up as a ripple that oscillates at the
same frequency as the displacement and grows taller as the amplitude increases. Then look
at the reconstructed image: even though no k-space magnitude was altered, the periodic
phase produces discrete ghost copies of the object, displaced along the readout direction,
rather than the simple blurring a one-off drift would cause.
:::

## What the interactivity reveals

:::{attention} TODO (your own observations)
Refer to the figures and to [](#eqRotation) in your explanation.
:::

## Can we fix it?

:::{attention} TODO
- Known angle and timing: what happens if you counter-rotate the post-motion lines?
- Unknown angle: your entropy test (autofocus).
- Would this work in a real experiment? Why or why not?
:::

## Code

````{admonition} Click to see the code behind the figure
:class: tip, dropdown

```python
kx = np.fft.fftshift(np.fft.fftfreq(ny))[None, :]   # cycles/pixel, readout / frequency encode (axis 1)
t = np.linspace(0, 1, nx)[:, None]                  # when each PE line is acquired (0 -> 1)
N_CYCLES = 8                                        # motion periods during the whole scan

def readout_translation(amount):
    """Object oscillates along readout during the scan: line i is acquired
    with the object shifted by amount * sin(2*pi*N_CYCLES*t_i) px. By the Fourier shift
    theorem this is only a phase ramp -- the magnitude is untouched."""
    d = amount * np.sin(2 * np.pi * N_CYCLES * t)
    k_m = kspace * np.exp(-2j * np.pi * kx * d)
    return d[:, 0], k_m
```
````
