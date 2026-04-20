# Build Orientation Optimization for Additive Manufactured Parts

This repository presents a workflow for **build-orientation optimization in additive manufacturing (AM)** based on **principal stress directions** extracted from finite element analysis (FEA) results. The main idea is to identify printing orientations that better align the anisotropic mechanical behavior of layer-wise manufactured parts with the dominant stress state of the component.

The project is based on the study **"Build orientation optimization of additive manufactured parts for better mechanical performance by utilizing the principal stress directions"** by Márton Tamás Birosz et al., published in the *Journal of Manufacturing Processes* (Vol. 84, 2022, pp. 1094–1102).

---

## Motivation

Mechanical performance in AM parts is strongly influenced by the **relative orientation between the deposited layers and the applied load path**. Since additively manufactured parts are typically stronger in some directions and weaker in others, selecting a poor build orientation can lead to suboptimal tensile or compressive behavior.

This project addresses that problem by:

- reading stress data from FEA,
- determining the **principal stress directions**,
- decomposing the stress state into **components parallel and perpendicular to the layer direction**,
- evaluating candidate build orientations,
- and selecting the orientation that is expected to provide improved mechanical performance.

---

## Core idea

The methodology assumes that the print orientation should be chosen such that the dominant principal stresses are favorably aligned with the layered manufacturing direction.

In practical terms:

- **compression perpendicular to the layers** is generally favorable,
- **tension perpendicular to the layers** is generally unfavorable,
- and stress components parallel to the layer plane are typically mechanically preferable for many AM processes and materials.

The optimization therefore evaluates how the stress field changes when the part orientation is rotated and searches for the most advantageous build direction.

---

## Repository workflow

The overall workflow is illustrated below.

![Workflow of the proposed method](assets/figure_workflow_clean.png)

The pipeline consists of the following major steps:

1. **Read FEA data** from a structural simulation.
2. **Pre-process the stress field** and extract the relevant principal stress directions.
3. **Rotate the coordinate system** using Euler rotations to represent candidate build orientations.
4. **Split stress directions** into components parallel and perpendicular to the build tray / layer plane.
5. **Store the evaluated results** for all tested orientations.
6. **Compare objective values** and determine the optimal build orientation.

The objective functions shown in the schematic correspond to minimizing detrimental tensile effects and maximizing favorable compressive alignment for the rotated stress field.

---

## Figures and interpretation

### 1. Mechanical motivation and example stress fields

![Mechanical concept, loading cases, CAD examples, and principal stress visualization](assets/figure_overview.png)

This composite figure summarizes the mechanical background of the method:

- **Top-left:** comparison of maximum stresses for tensile and compressive loading, with the load applied either **parallel** or **perpendicular** to the printed layers.
- **Top-right:** schematic interpretation of favorable and unfavorable loading relative to the layer arrangement.
- **Bottom-left:** example geometries used for evaluation.
- **Bottom-right:** principal stress visualizations on representative parts, showing how stress directions can be used to guide build-orientation selection.

These plots illustrate why orientation matters: the same geometry may respond very differently depending on whether the load path is aligned with or against the anisotropic layer structure.

### 2. Orientation decomposition concept

![Workflow, Euler rotation, and stress decomposition concept](assets/figure_workflow.png)

This figure shows the conceptual basis of the optimization:

- **Left:** the computational workflow from FEA import to orientation decision.
- **Middle:** Euler rotation used to transform the stress directions into the candidate build frame.
- **Right:** decomposition of a principal stress vector into
  - a component **parallel** to the build tray/layer plane, and
  - a component **perpendicular** to it.

This decomposition is the key step that connects the FEA stress field to AM-specific manufacturability and strength considerations.

---

## What this repository is for

This repository can serve several purposes:

- **Research reproduction** of the published build-orientation optimization method.
- **Educational demonstration** of how stress tensors and principal directions can support AM process planning.
- **Engineering workflow template** for coupling FEA outputs with manufacturing-oriented decision metrics.
- **Starting point for further development**, such as multi-objective optimization, process constraints, or material-specific calibration.

---

## Expected inputs

A typical implementation of this workflow requires:

- nodal or elemental **FEA stress data**,
- principal stresses and/or principal stress directions,
- geometric reference frame of the part,
- candidate build orientations,
- and, optionally, experimentally calibrated material sensitivity to loading direction.

---

## Expected outputs

Typical outputs include:

- evaluated candidate orientations,
- stress-direction components relative to the build tray,
- objective-function values for each orientation,
- and the **recommended build orientation** for improved mechanical performance.

---

## Suggested repository structure

A practical repository layout could look like this:

```text
.
├── README.md
├── assets/
│   ├── figure_overview.png
│   ├── figure_workflow.png
│   └── figure_workflow_clean.png
├── data/
│   └── example_fea_exports/
├── src/
│   ├── io/
│   ├── preprocessing/
│   ├── rotations/
│   ├── optimization/
│   └── visualization/
├── notebooks/
└── results/
```

---

## Citation

If you use this repository in academic work, please cite the underlying article.

```bibtex
@article{birosz2022buildorientation,
  title   = {Build orientation optimization of additive manufactured parts for better mechanical performance by utilizing the principal stress directions},
  author  = {Birosz, M{\'a}rton Tam{\'a}s and co-authors},
  journal = {Journal of Manufacturing Processes},
  volume  = {84},
  pages   = {1094--1102},
  year    = {2022},
  doi     = {10.1016/j.jmapro.2022.10.038}
}
```

---

## Notes

- This README is written to present the project clearly on GitHub.
- The included figures are stored locally in the `assets/` folder and embedded directly in this Markdown file.
- You can adapt this README further once the code, datasets, and scripts are added to the repository.
