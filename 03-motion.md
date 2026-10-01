---
title: Head motion during the acquisition
---
## Motivations

Patient motion during acquisition is one of the most common sources of image degradation in clinical MRI, since even small movements partway through a scan can corrupt the final reconstruction in ways that are not obvious from looking at k-space alone. Real motion is also rarely a single steady drift in one direction, it can be more cyclical, like motion from by breathing. So this demo models motion as an oscillation rather than continous motion to better reflect reality. 

## The background

The acquisition is assumed to be made line by line, so if the patient moves partway through the scan, the lines acquired before the motion still describe the old position while the lines acquired after it
describe the new one. The motion also is applied line per line instead of being instatanious.

The simplest version of motion is a pure in-plane translation. By the Fourier shift
theorem, shifting an image only multiplies its k-space by a linear phase ramp — the
*magnitude* of k-space is completely untouched:

$$
s'(\mathbf{r}) = s(\mathbf{r} - \mathbf{d})
\quad \Longleftrightarrow \quad
S'(\mathbf{k}) = S(\mathbf{k})\, e^{-2\pi i\, \mathbf{k}\cdot\mathbf{d}}
$$ (eqShift)

## Interactive exploration

:::{figure} #figMotionReadout
:label: motionReadoutFig
Cyclical translation along the readout (frequency-encode) direction (slider = oscillation
amplitude, in pixels), imitating a periodic motion such as breathing .
Top row, left to right: the displacement profile over acquisition order, the resulting
phase added to each k-space sample ([](#eqShift)), and the k-space magnitude. Bottom row:
the reconstructed image with motion, next to the static reference.
:::

<!--
TODO: once tagged, uncomment and add to myst.yml's toc with `hidden: true`.
Reminder: ipywidgets sliders do not work on the static site, so precompute Plotly frames.

:::{figure} #figMotionAngle
:label: motionAngleFig
TODO caption: slider = rotation angle, motion at a fixed line near the centre.
:::

:::{figure} #figMotionSweep
:label: motionSweepFig
TODO caption: image error as a function of where the motion window sits in k-space.
:::
-->

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

