# Build Orientation Optimization for Additive Manufactured Parts

This repository presents a methodology for **optimizing the build orientation of additively manufactured parts** by using **principal stress directions** extracted from finite element analysis (FEA). The central idea is that the structural performance of a printed component is not only a function of its geometry and material, but also of how the part is oriented during fabrication. Because layer-based manufacturing introduces directional mechanical behavior, the same part can exhibit markedly different tensile and compressive response depending on the relation between the applied stresses and the deposited layers.

The project focuses on connecting **simulation-driven stress analysis** with **manufacturing-aware orientation selection**. Instead of choosing the build direction only from geometric or support-generation considerations, the method evaluates how the internal stress state of the part aligns with the layer structure and uses that information to identify more favorable printing orientations.

This repository is based on the article **“Build orientation optimization of additive manufactured parts for better mechanical performance by utilizing the principal stress directions”** published in the *Journal of Manufacturing Processes* in 2022.

---

## Project overview

In additive manufacturing, anisotropy is an inherent consequence of the layer-wise fabrication process. The bond strength between neighboring layers, the response to tensile loading across layer boundaries, and the resistance to compressive loading in different directions can all vary substantially. As a result, build orientation becomes a decisive design variable for structural performance.

This project proposes a computational workflow that:

- reads stress results from an FEA model,
- extracts the **principal stress magnitudes and directions**,
- rotates the stress field into candidate build orientations,
- decomposes each stress direction into **components parallel and perpendicular to the build tray / layer plane**,
- evaluates orientation-dependent objective functions,
- and identifies the orientation that is expected to provide improved mechanical behavior.

The approach is especially relevant for engineering parts whose functional loading is known in advance and where the final print orientation can be selected during process planning.

---

## Why build orientation matters

For conventionally manufactured isotropic parts, orientation during production is often irrelevant to the final bulk material response. In contrast, for many additively manufactured components, the mechanical behavior depends strongly on how the load is applied relative to the printed layers.

The first figure, **`preview_1.png`**, summarizes this motivation.

![preview_1.png](assets/preview_1.png)

In **`preview_1.png`**:

- the **upper-left chart** compares maximum stresses for tensile and compressive loading when the external load is applied either **parallel** or **perpendicular** to the layer arrangement,
- the **upper-right schematic** illustrates preferred and non-preferred loading configurations relative to the layers,
- the **lower-left images** show representative component geometries used for investigation,
- and the **lower-right visualizations** present principal stress directions in sample geometries.

The engineering interpretation behind **`preview_1.png`** is that the orientation of the part should be selected so that the stress field interacts with the anisotropic material structure in a mechanically advantageous way. In general, tensile loading across weak inter-layer interfaces is undesirable, while other loading configurations may be considerably more favorable. This observation forms the foundation of the optimization strategy implemented in the project.

---

## Main idea of the method

The method assumes that a mechanically beneficial build orientation can be found by examining how the **principal stress directions** are arranged with respect to the printing plane and build direction.

Rather than using only stress magnitudes, the workflow considers the **directionality of the stress state**. For each candidate orientation, the principal stress vectors are transformed into the coordinate system of the build tray. Their components are then separated into:

- a **parallel component**, lying in the plane of the layers, and
- a **perpendicular component**, acting through the layer stacking direction.

This distinction is important because the layer-normal direction often governs weaker mechanical response in many AM processes. By measuring how much of the stress field acts in more favorable versus less favorable directions, the method can score candidate orientations and compare them systematically.

The conceptual decomposition is illustrated in **`preview_2.png`**.

![preview_2.png](assets/preview_2.png)

In **`preview_2.png`**:

- the **left panel** shows the overall computational sequence from FEA data import to the determination of the optimal build orientation,
- the **middle panel** introduces the Euler-rotation-based representation of candidate part orientations,
- and the **right panel** demonstrates how a principal stress direction is split into a component parallel to the build tray and a component normal to it.

This figure is the core conceptual summary of the repository: stress information from simulation is transformed into manufacturing-relevant directional measures, which are then used for orientation optimization.

---

## Computational workflow

The workflow implemented in this project can be described as a sequence of analysis and decision steps.

### 1. Reading FEA results
The process starts with stress results exported from a finite element model. These data may be associated with nodes or elements, depending on the solver and post-processing strategy.

### 2. Pre-processing
The raw simulation output is cleaned and organized so that the relevant stress quantities can be evaluated consistently. At this stage, the principal stresses and their associated directions are extracted or reconstructed.

### 3. Definition of candidate orientations
The part is virtually rotated with respect to the build platform using an Euler-angle-based parameterization. Each orientation represents a possible manufacturing setup.

### 4. Directional decomposition of stresses
For every candidate orientation, the principal stress directions are expressed in the rotated coordinate frame. Their components relative to the build tray are separated into parallel and perpendicular contributions.

### 5. Objective-function evaluation
The transformed stress information is aggregated into objective values that quantify the suitability of a given build orientation. The objective functions are designed to favor mechanically advantageous stress-layer relationships and penalize unfavorable ones.

### 6. Orientation selection
After evaluating all candidate orientations, the results are compared to determine the most suitable build direction for the selected preference scheme.

The logic of this pipeline is visible again in **`preview_2.png`**, where the sequence of reading, rotating, decomposing, saving, and evaluating data is summarized explicitly.

---

## Optimization results and orientation comparison

The third figure, **`preview_3.png`**, presents the optimization outcome and example preferred orientations.

![preview_3.png](assets/preview_3.png)

In **`preview_3.png`**:

- the **left-hand plots** show distributions of feasible solutions in the objective space,
- the **highlighted points** indicate selected optimal solutions under different preference schemes,
- and the **right-hand renderings** compare the original part orientation with the orientations obtained from no-preference and weighted optimization settings.

This figure demonstrates that the optimization is not merely theoretical. It produces a set of candidate solutions and allows the user to identify specific build orientations depending on the decision strategy. In other words, the workflow does not only analyze stress directions; it converts them into practical manufacturing recommendations.

---

## What the repository contains conceptually

This repository is intended to document and support a simulation-driven AM orientation optimization framework built around the following concepts:

- **stress-aware process planning**,
- **principal-direction-based evaluation**,
- **orientation search using rotational parameterization**,
- **multi-criteria or weighted decision making**,
- and **mechanical-performance-oriented additive manufacturing preparation**.

The repository can therefore serve as:

- a **research implementation** of the published methodology,
- a **reference workflow** for integrating FEA with AM build-direction decisions,
- an **educational example** for principal stress direction analysis,
- or a **base for future development** toward process-specific, material-specific, or multi-objective extensions.

---

## Expected inputs

A practical implementation of the method typically requires:

- finite element stress results for the part under the intended service loading,
- principal stress magnitudes and directions, or enough data to derive them,
- the global coordinate system of the model,
- a definition of the build tray and candidate orientation space,
- and optional weighting or preference parameters for the optimization objectives.

Depending on the solver and material model, additional calibration data may also be incorporated to reflect experimentally measured directional behavior of the printed material.

---

## Expected outputs

Typical outputs of the workflow include:

- rotated stress-direction datasets,
- parallel and perpendicular directional stress components,
- objective-function values for all evaluated orientations,
- feasible and Pareto-optimal solutions,
- and a recommended build orientation for the part.

These outputs can support both engineering interpretation and downstream manufacturing decisions.

---

## Practical significance

The practical significance of the project lies in shifting the build-orientation decision from a purely geometric problem to a **mechanics-informed optimization problem**. In many industrial workflows, build orientation is chosen primarily from support volume, print time, or surface quality considerations. While these remain important, structural performance can be equally critical for functional parts.

By embedding principal stress direction analysis into orientation planning, this project provides a route toward more reliable and better-performing AM components. The methodology is especially useful when the part is expected to carry significant service loads and when anisotropic strength differences cannot be neglected.

---

## Repository structure

A recommended repository layout is the following:

```text
.
├── README.md
├── assets/
│   ├── preview_1.png
│   ├── preview_2.png
│   └── preview_3.png
├── data/
│   └── example_fea_exports/
├── src/
│   ├── io/
│   ├── preprocessing/
│   ├── rotations/
│   ├── objectives/
│   ├── optimization/
│   └── visualization/
├── notebooks/
└── results/
```

---

## Citation

If you use this repository or build upon the methodology in academic work, please cite the underlying publication.

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

## Reference

The methodology described in this repository is based on the article metadata provided in the uploaded source file.
