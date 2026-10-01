---
title: Head rotation during the acquisition
---

## What we do

:::{attention} TODO
Describe the simulation: Cartesian acquisition, one phase-encode line per TR, the patient
rotates suddenly by 20° near the centre of k-space and then stays still. Explain how the
k-space lines acquired after the motion are replaced with lines from the rotated image.
Then introduce your extension: continuous rotation between `motion_start` and `motion_stop`.
:::

Rotating an object rotates its k-space by the same angle, which is why the rotated image
can be used to generate the post-motion lines:

$$
\rho'(\mathbf{r}) = \rho(R_\theta^{-1}\mathbf{r})
\quad \Longleftrightarrow \quad
S'(\mathbf{k}) = S(R_\theta^{-1}\mathbf{k})
$$ (eqRotation)

## Lab 1 recap

:::{attention} TODO
One sentence on the static result from Lab 1.
:::

## Interactive exploration

% TODO: once the notebook cells are tagged, uncomment the figures below and list
% notebooks/03-motion.ipynb in myst.yml's toc with `hidden: true`.
% Reminder: ipywidgets sliders do not work on the static site, so precompute Plotly frames.
%
% :::{figure} #figMotionLine
% :label: motionLineFig
% TODO caption: slider = phase-encode line at which the motion happens (20° fixed).
% :::
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
TODO: tell the reader what to drag and what to look for.
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
# TODO: key snippet (build_kspace)
```
````
