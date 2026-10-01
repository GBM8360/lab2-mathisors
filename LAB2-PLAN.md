# Lab 2 — working plan & page templates

> Personal planning file. **Not part of the book**: add `LAB2-PLAN.md` to `exclude:` in
> `myst.yml` (next to `README.md`) so it never gets built.
>
> Legend: `TODO` = you must write/do it · 💡 = suggestion · ✅ = already done in the repo
>
> Remember the lab rule: questions about **your observations / intuition** must be written
> by you (no LLM). The templates below only give you *prompts*, not answers.

---

## 0. Grading → what satisfies it

| Component (points) | What the grader looks for | Where in this plan |
|---|---|---|
| MyST book & formatting (/7) | index + 3 part pages, customized `myst.yml`, toc, abbreviations, bib, build passes | §1, §2, §3 |
| Interactive figures (/6) | Plotly figures **embedded via labels**, colour rules respected, work on the static site | §4, pages §5–7 |
| MyST/Markdown explanations (/4) | equations, cross-refs, `{cite:p}`, admonitions, dropdown code, tables | every page skeleton |
| Quality (ranked, /3) | insight *beyond* the Lab 1 static figures, clarity, originality, "can we fix it?" speculation | "Discussion" section of each page |

---

## 1. Repository restructuring

Target layout (💡 suggestion — rename freely):

```
index.md                        TODO rewrite: what the book is, dataset, how to read it
01-central-mask.md              Part 1 page
02-downsampling.md              Part 2 page
03-motion.md                    Part 3 page   (✅ most of the computation already exists)
notebooks/
  kspace_utils.py               💡 shared loader + to_k/to_img (you copy-paste them 3× today)
  01-central-mask.ipynb         TODO
  02-downsampling.ipynb         TODO (✅ CS reconstruction cell can move here)
  03-motion.ipynb               ✅ cells 1–2 of figure-demo.ipynb move here
bibliography/references.bib     TODO add your real references (§3)
data/                           ✅ mag + phase NIfTI already in repo
```

- [ ] Delete / hide the template pages `01-getting-started.md` and `02-interactive-figures.md`
      (template text, not your content). 💡 You can keep one "How this book was built"
      appendix page if you want to show off the MyST tooling.
- [ ] Delete `notebooks/figure-demo.ipynb` once its cells have been split out.
- [ ] Remove `notebooks/.ipynb_checkpoints` and `.ipynb_checkpoints` from git; add them to `.gitignore`.
- [ ] Remove `GBM8360E - Lab 2 (1).pdf:Zone.Identifier` (Windows artefact) — don't commit it.

---

## 2. `myst.yml` — things to customize

- [ ] `title:` (still `'TODO'`) and `subtitle:` → e.g. "Re-exploring k-space manipulations interactively"
- [ ] `authors:` ✅ name/affiliation done — 💡 add `github:` and `email:` fields
- [ ] `date:` → submission date
- [ ] `keywords:` → k-space, Gibbs ringing, aliasing, motion artefacts…
- [ ] `abbreviations:` ✅ good base — 💡 add `PSF`, `CS`, `ISTA`, `RMSE`, `ky`/`kx` if you use them
- [ ] `toc:` → index + your 3 pages + each notebook with `hidden: true`
- [ ] `exports: articles:` → the same 4 pages (the PDF needs them listed **separately**)
- [ ] `exports: output:` → rename `my-myst-book.pdf`
- [ ] `site: options: logo_text:` → your title
- [ ] `site: actions: url:` → `https://github.com/GBM8360/lab2-mathisors`
- [ ] `exclude:` → add `LAB2-PLAN.md`

---

## 3. Bibliography (`bibliography/references.bib`)

The current entries are template examples. Keep the ones you actually cite, then add yours.
💡 Candidates (check each entry against the source before citing it):

| Topic | Suggested reference |
|---|---|
| Truncation / Gibbs ringing, FOV = 1/Δk | Nishimura 2010 (already in the bib); Bernstein 2004 (already in the bib) |
| Compressed sensing (your ISTA cell) | Lustig, Donoho & Pauly, *Sparse MRI*, MRM 2007 |
| Motion artefacts overview | Zaitsev, Maclaren & Herbst, JMRI 2015 |
| Entropy-based autofocus (retrospective motion correction) | Atkinson et al., IEEE TMI 1997 |
| Dataset | the GBM8360 `laboratory_1` release v0.1 (`@misc`, with its URL) |
| Tools | MyST, Plotly |

---

## 4. ⚠️ Technical issues found in the current work (fix these before anything else)

1. **`ipywidgets.interact` won't work on the published site.** The site has *no kernel*
   (see `02-interactive-figures.md`, "Your slider is not computing. It's choosing.").
   Your rotation figure (cell 2 of `figure-demo.ipynb`) must be rebuilt as a **Plotly
   slider or `animation_frame` over precomputed frames**. Pick 1–2 variables per figure
   (the frame count grows multiplicatively with each extra slider).
2. **`requirements.txt` is missing imports**, so the CI build will fail:
   `scipy`, `nibabel`, `PyWavelets` (for `pywt`), and `ipywidgets` if you keep it.
3. **No `#| label:` on your cells** → nothing can be embedded with `:::{figure} #label`.
   `02-interactive-figures.md` still embeds `#figDemo`, which no longer exists.
4. **Set the renderer at the top of each notebook:**
   `pio.renderers.default = "plotly_mimetype+png"`, or the PDF export gets empty figures.
5. **Fix your colour scales across frames** (`zmin`/`zmax`), or every frame auto-scales and
   hides the artefact you want to show. Lab rules: magnitude in grey with the contrast
   adjusted, **phase in a diverging map from −π to π with a colour bar** ✅ (you already
   use RdBu/bwr).
6. **Axis convention:** per the NIfTI JSON, array shape = (88, 128); the **readout runs along
   axis 1** and **phase-encode lines run along axis 0**. ✅ Your motion code replaces
   `kspace[i, :]` (whole PE lines), which is correct. State this explicitly on the pages; it
   matters for Part 2 (which direction you downsample) and Part 3.
7. `cell 1` uses an unshifted FFT and `cell 2` a shifted one — use one convention everywhere
   (💡 put `to_k`/`to_img` in `kspace_utils.py`).
8. **Page weight:** 88×128 complex → show `uint8`/rounded magnitude and phase; aim for about 20
   frames per slider, not 180.

---

## 5. Page template — `index.md`

```markdown
---
title: <Book title>
---

## About this book
TODO — 1 paragraph: follow-up of Lab 1 Ex. 3, three k-space manipulations revisited
with interactive figures. What the reader will get that a static figure can't give.

## The dataset
TODO — subject-02, 2D SPGR, MT-on, 88 × 128 at 2 × 2 × 5 mm, 3 T (from the JSON sidecar).
Link to the release (not Google Drive). Explain how k-space is obtained here
(FFT of mag·e^{iφ}) and which axis is PE vs RO → a small table.

## Recap: image ↔ k-space
TODO — the Fourier relation as a labelled equation, e.g. (eqFT), and 2–3 lines on
centre = contrast, periphery = edges/detail.

## How to read this book
- [](#01-central-mask) … one line per page
:::{tip} How to use the sliders
TODO
:::
```

---

## 6. Page templates — one per part

Each part page follows the same skeleton (graders like consistency):

```markdown
---
title: Part N — <name>
---

## What we do                ← 2–3 sentences + labelled equation of the operation
## Lab 1 recap               ← the static result, 1 sentence (optional small static figure)
## Interactive exploration   ← :::{figure} #label  +  caption  +  "try this:" admonition
## What the interactivity reveals   ← YOUR observations (no LLM)
## Can we fix it?            ← speculation: retrospective fix on this dataset? in a real scan?
## Code                      ← dropdown admonition with the key snippet
```

### 6.1 `01-central-mask.md` — masking the centre of k-space

- Operation: multiply k-space by a mask *M* → image = original ⊛ 𝔉⁻¹{M} (convolution
  theorem). 💡 Write it as a labelled equation and cross-reference it from the discussion.
- 💡 Interactive ideas (pick one or two):
  - Slider: **fraction of k-space kept** (e.g. 5 % → 100 %). Panels: masked k-space | magnitude |
    difference from original.
  - Toggle / second trace: **keep centre (low-pass)** vs **remove centre (high-pass)** — the
    wording "mask the central region" is ambiguous; showing both is a cheap win.
  - Shape: **square vs circle** (the lab explicitly allows circular).
  - 💡 High-insight extra: a **1D line profile** across an edge (e.g. the skull/CSF boundary)
    under the image → the ringing (Gibbs) becomes measurable, not just visible.
  - 💡 Can we fix it? Slider over apodisation windows (none / Hamming / Tukey α) → trade-off between
    ringing and blur.
- Prompts for YOUR text: At which fraction does the anatomy become recognisable?
  Where does the energy of this image live? What does the PSF look like?

### 6.2 `02-downsampling.md` — downsampling by 2 in one direction

- Relations to show as equations: FOV = 1/Δk, Δx = 1/(N·Δk) → cross-ref them.
- 💡 Interactive ideas:
  - Slider **R = 1…4** × toggle **direction (PE vs RO)**.
  - Compare three ways to "downsample": **skip every R-th line** (aliasing/wrap-around),
    **crop the outer k-space** (lower resolution, same FOV), **zero-fill** the cropped data
    (the lab allows it).
  - 💡 Overlay the FOV box on the image to make the fold-over obvious.
- ✅ Existing work to reuse: **cell 3 of figure-demo.ipynb (variable-density mask + ISTA CS
  reconstruction)** → put it in "Can we fix it?". 💡 Make it interactive with a
  **sampling fraction** or **ISTA iteration** slider, and show the zero-fill result next to the CS
  result, with an error value in the title.
  - ⚠️ Point to discuss: your mask is random in **2D**, which is not achievable in a 2D
    Cartesian acquisition (the readout is always fully sampled). 💡 Try a 1D variable-density mask
    along PE only and compare. That comparison is itself a good "insight" paragraph.
  - Why does CS work with random undersampling but not with regular R = 2? (your words)

### 6.3 `03-motion.md` — sudden 20° head rotation

- ✅ Existing work: `build_kspace()` (continuous rotation between `motion_start` and
  `motion_stop`, then held), overlay of moving/rotated lines, and the **error vs motion-window
  position sweep** — that sweep is already a strong "beyond Lab 1" result; make it the core
  of the page.
- TODO to cover the required case: the lab asks for a **sudden** 20° rotation near the centre
  (± 5 lines). 💡 Your code already handles it: `motion_stop = motion_start + 1`. Show the sudden
  case first, then the continuous rotation as the extension.
- 💡 Converting to static sliders (see §4.1):
  - Figure A: slider = **PE line at which motion occurs** (e.g. every 4 lines), fixed 20°.
  - Figure B: slider = **rotation angle** (0–40°) at a fixed line near the centre.
  - Figure C (static, cheap): your error-vs-line curve, 💡 with one curve per angle.
- Equation to show: the rotation property of the FT (rotating the image rotates k-space by the same
  angle) → explains why rotating the image and re-sampling the Cartesian lines is valid.
- Prompts for YOUR text: Why is the error curve shaped the way it is? What changes
  between motion early, at the centre, or late? Ghosting direction vs PE/RO?
- "Can we fix it?" 💡 directions:
  - Known angle and line → counter-rotate the post-motion lines: but the rotated samples fall
    **off the Cartesian grid** and leave gaps (show it!).
  - Unknown angle → search for the angle that minimises **image entropy** (autofocus; your
    commit message mentions an entropy test — present it here with an entropy-vs-angle plot).
  - Real experiment: through-plane motion, spin history, B0/coil-sensitivity changes, timing not
    known → navigators / prospective correction (cite Zaitsev 2015).

---

## 7. MyST feature checklist (each ticked at least once somewhere)

- [ ] Frontmatter title on every page
- [ ] Labelled equations `$$ … $$ (eqLabel)` + `[](#eqLabel)` in text
- [ ] Figures embedded from notebooks `:::{figure} #cellLabel` with `:label:` + caption
- [ ] Cross-refs to figures/tables/pages `[](#label)`
- [ ] A table with a label (e.g. dataset parameters) `:::{table}`
- [ ] `{cite:p}` on every page
- [ ] Admonitions: `tip` (how to use the slider), `note`, `warning` (limitations)
- [ ] Dropdown code `:class: dropdown`
- [ ] Abbreviations (auto from `myst.yml`) — use them in the text
- [ ] External links (dataset release, Plotly docs)
- [ ] 💡 Extras for the "quality" ranking: `{margin}` asides, a `{mermaid}` or `{figure}` diagram
      of the acquisition timeline for Part 3, a closing "Open questions" section in the index

---

## 8. Before submitting

- [ ] `myst start` locally → every page renders, every slider moves
- [ ] `myst build --html --execute` locally with no errors/warnings about missing targets or citations
- [ ] Push → **Actions green** on the last commit before the deadline
- [ ] Open `https://gbm8360.github.io/lab2-mathisors/` and test it in a private window
- [ ] Check the PDF artifact (`book-pdf`): static snapshots present
- [ ] Moodle: submit **only** the 2 links (repo + site)
