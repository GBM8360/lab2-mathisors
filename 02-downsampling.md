---
title: Downsampling k-space
---

## What we do

:::{attention} TODO
Describe how you downsample k-space by 2 in one direction (which direction, PE or RO; see
[](#tblAxes)), and which method you use (skipping lines, cropping, zero-filling…).
:::

The k-space sampling interval sets the field of view, and the extent of k-space sets the
resolution {cite:p}`Bernstein2004`:

$$
\mathrm{FOV} = \frac{1}{\Delta k}, \qquad \Delta x = \frac{1}{N\,\Delta k}
$$ (eqFOV)

## Lab 1 recap

:::{attention} TODO
One sentence on the static result from Lab 1.
:::

## Interactive exploration

% TODO: once the notebook cell is tagged `#| label: figDownsample`, uncomment the figure below
% and list notebooks/02-downsampling.ipynb in myst.yml's toc with `hidden: true`.
%
% :::{figure} #figDownsample
% :label: downsampleFig
% TODO caption: what the slider controls and what each panel shows.
% :::

:::{tip} Try this
TODO: tell the reader what to drag and what to look for.
:::

## What the interactivity reveals

:::{attention} TODO (your own observations)
Refer to the figure and to [](#eqFOV) in your explanation.
:::

## Can we fix it?

:::{attention} TODO
Your compressed-sensing (ISTA) reconstruction from `figure-demo.ipynb` goes here.
Compare zero-filling with the CS result, and discuss why random undersampling behaves
differently from regular undersampling.
:::

% TODO: optional second figure (label e.g. `figCS`) for the CS reconstruction.

## Code

````{admonition} Click to see the code behind the figure
:class: tip, dropdown

```python
# TODO: key snippet (undersampling + reconstruction)
```
````
