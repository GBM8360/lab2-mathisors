---
title: Introduction
---
 

:::{attention} About this book
This book is a follow-up to Exercise 3 of Lab 1 of the course GBM8360. It revisits three
manipulations of raw k-space with interactive figures. Interacting with the figures will help provide additionnal insight that static figure just cannot. The goal of this myst book is to gain additionnal intuition on how kspace work related to MRI and to show cool and interesting stuff!
:::
## About the author

My name is Mathis Ors, as of 2026 I am a first year master student in biomedical engeneering at Polytechnique Montreal. I'm currently conducting research in the NeuroPoly laboratory as part of my studies. I work on B0 shimming and antennas for MRI.

- GitHub: [mathisors](https://github.com/mathisors)
- LinkedIn: [Mathis Ors](https://www.linkedin.com/in/TODO)

## The dataset

The data come from subject-02 of the GBM8360
[`laboratory_1` dataset release](https://github.com/GBM8360/laboratory_1/releases/tag/v0.1):
It is a 2D spoiled gradient echo (SPGR/FLASH), acquired at 2.89 T with TR = 25 ms, TE = 4 ms and a 7° FA, at 2 × 2 × 5 mm resolution over an 88 × 128 matrix.

The magnitude and phase images are each stored separately. k-space is obtained here by
recombining them into a complex image, $s = \text{mag} \cdot e^{i \cdot \text{phase}}$,
and taking its 2D FFT:

$$
S(k_x, k_y) = \mathcal{F}\{s(x, y)\}
= \sum_{m=0}^{N_x-1} \sum_{n=0}^{N_y-1} s(m, n)\, e^{-i 2\pi \left(\frac{k_x m}{N_x} + \frac{k_y n}{N_y}\right)}
$$ (eqFT)
## How to read this book

- 01 [](./01-central-mask.md): Exploring Kspace cropping, FOV and resolution.
- 02 [](./02-downsampling.md): 02 Exploring downsampling and compressed sensing MRI.
- 03 [](./03-motion.md): 03 Exploring how duration affect kspace.

:::{tip} How to use the interactive figures
Read the introduction of every interactive plot and play with the sliders to gain intuition on the different Kspace mecanism.
:::
