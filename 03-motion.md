---
title: Head motion during the acquisition
---
## Motivations

Patient motion during acquisition is one of the most common sources of image degradation in clinical MRI, since even small movements partway through a scan can corrupt the final reconstruction in ways that are not obvious from looking at k-space alone. Real motion is also rarely a single steady drift in one direction; it can be more cyclical, like motion from breathing. So this demo models motion as an oscillation rather than continuous motion to better reflect reality.

## The background

The acquisition is assumed to be made line by line, so if the patient moves partway through the scan, the lines acquired before the motion still describe the old position while the lines acquired after it
describe the new one. The motion is also applied line per line instead of being instantaneous.

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
amplitude, in pixels), imitating a periodic motion such as breathing.
Top row, left to right: the displacement profile over acquisition order, the resulting
phase added to each k-space sample ([](#eqShift)), and the k-space magnitude. Bottom row:
the reconstructed image with motion, next to the static reference.
:::

:::{tip} Try this
Drag the slider from 0 up to its maximum. Watch the k-space magnitude panel and phase difference panel. None of the information about the motion is in the magnitude;
it is all packed into the phase panel, where it shows up as a ripple that oscillates at the
same frequency as the displacement and grows taller as the amplitude increases. Then look
at the reconstructed image: even though no k-space magnitude was altered, the periodic
phase produces discrete ghost copies of the object, displaced along the phase-encode direction.
:::

## What the interactivity reveals

:::{attention} Observation
In the phase difference panel we see a phase ramp appearing in a cyclic way with a slope proportional to the displacement at the moment that line was acquired ([](#eqShift)). Since the displacement oscillates, the slope goes up, back through zero, and down again from line to line, giving the repeating pattern of the panel. Interestingly, the center of the phase difference stays close to zero no matter how much oscillation is present. This would mean there is almost no phase difference at the center of k-space. My intuition is that the phase difference is related to $k_x$: since it is close to 0 around the center, no phase difference appears. In fact, we do see the difference increasing with $k_x$.

As we increase the amplitude, the slope grows and the phase starts to wrap in the $k_x$ direction too. This creates an interesting pattern with wrapping in both directions.

We can see that the k-space magnitude is unchanged, as predicted. In the reconstructed image, however, the magnitude looks ghosted along the phase-encode direction. The motion is along the readout direction, but it is the periodic modulation from one phase encode line to the next that creates the ghosts, so the copies are spaced out along the phase encode axis.
:::

## Can we fix it?

:::{attention} Correcting motion
Fixing this could be possible, but we would have to have a reference of how big is the movement and what type of movement appear. This could be done by acquiring reference kspace lines such as navigators to retrospectively correct the phase.
:::

