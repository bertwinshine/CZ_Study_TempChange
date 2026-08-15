# CZ melt:temperature study

CFD reference cases for the Czochralski (CZ) silicon melt, meant as ground-truth
data for the surrogate / PINN model. In this study only the hot crucible
temperature changes from case to case; everything else is held fixed. That makes
it a clean single-parameter set: temperature in, field out.

## The shared anchor (read this first)

The anchor of the whole dataset is the **1745 K** case, `cz_(temp 1745).csv`.
It is the same converged field — to floating-point precision — as the +8 rpm
case in the crystal sweep and the −3 rpm case in the crucible sweep. So all
three study axes (temperature, crystal rotation, crucible rotation) pass through
one common state: crystal +8 rpm, crucible −3 rpm, hot wall 1745 K. That shared
point is what lets the three sliders line up.

The 1750 K case (`cz_(temp 1750).csv`) used to be called the baseline. It's just
another point on this axis now — a valid 1750 K field that sits inside the sweep.
The "baseline" name was dropped so nothing implies 1750 K is the anchor; the
anchor is 1745 K.

## The two folders

The melt behaves in two ways depending on how hot it gets, so the data is split:

**steady/** — the main set, 1730 K up to 1785 K. In this range the melt settles
into a single steady field, so each case is one steady-state solution. These are
the certified reference cases.

**transient/** — the higher-temperature set: 1790, 1795, 1800 K. Above roughly
1785 K the melt no longer settles; the flow keeps slowly moving and oscillating,
so there is no single steady answer. These were run time-dependent and then
time-averaged over several oscillation cycles. Each file here is an averaged
field, not a snapshot — keep them separate from the steady set when building a
steady POD basis.

Each folder has its own README with the specifics.

## The case (same for every file)

- 2D axisymmetric silicon melt in a crucible.
- Boundary conditions from Huang et al., AIP Advances (2025), DOI 10.1063/5.0271778.
- Buoyancy via the Boussinesq approximation.
- Crucible radius 0.30 m, melt height 0.15 m (wide, shallow pool).
- Cold crystal fixed at 1685 K.
- Rotation fixed: crystal +8 rpm, crucible −3 rpm.
- Hot crucible wall is the only thing varied, from case to case.
- Solved in ANSYS Fluent 2026 R1 (Student license).

## Why two types of data

Silicon melts near 1685 K, and real CZ growth runs the melt only tens of degrees
above that. Up to ~1785 K the flow is steady and easy to trust — the steady/ set.
Higher up (1790–1800 K) the stronger buoyancy makes the melt restless and weakly
unsteady, so those cases can't be given as one steady field and are time-averaged
instead — the transient/ set. The changeover is around 1785–1790 K, the top edge
of the useful steady range.

## Columns

Same columns in every file, one row per mesh node:

`r, z, u_r, u_z, u_swirl, p, T`

- `r`  — radial position (m), 0 to 0.30
- `z`  — axial position (m), 0 to 0.15
- `u_r` — radial velocity (m/s)
- `u_z` — axial velocity (m/s)
- `u_swirl` — swirl velocity (m/s)
- `p`  — pressure (Pa)
- `T`  — temperature (K)

Same mesh in all files (8181 nodes), so node positions line up between cases.
The hottest point is always at the crucible edge (max r), the coldest at the
crystal.

Note on coordinates: Fluent does not always write the columns in the same order.
If more cases are added, check that r/z (and u_r/u_z) aren't swapped before adding
them. `certify_case.py` in this folder checks that automatically.

## How each case was checked

For every steady case, `certify_case.py` re-derives the boundary conditions from
the temperature in the filename and checks the exported field against them:

- solution converged (steady) / settled into a repeating oscillation (transient),
- hot and cold temperatures come out as set (crystal 1685 K, hot wall = filename),
- the free-surface and crucible-bottom thermal ramps match the prescribed linear
  profile between the 1700 K axis floor and the hot wall,
- domain size correct (r to 0.30, z to 0.15),
- axis symmetry (radial and swirl velocity vanish on the centreline),
- no NaN/inf, no duplicate nodes.

Mesh independence was checked on the baseline mesh; every case uses that same mesh.
The maximum swirl (~0.1476 m/s) is identical across the steady cases simply because
the rotation is fixed and the swirl boundary condition doesn't depend on temperature
— not an inference about laminar vs turbulent flow.

## What this is / isn't

- This is the temperature study — the hot wall temperature is the only thing that
  changes.
- The crystal-rotation and crucible-rotation studies live in their own repos and
  share the 1745 K anchor with this one.
- These are reference CFD fields. Building the ML model from them is the ML side,
  not part of this repo.

## One caveat to know

The baseline field was compared against a COMSOL result from Aditya. It matched
well in the bulk (about 1 K difference, correlation R ≈ 0.91), with about a 5 K
difference at the top corner where the crystal meets the free surface. That's fine
for this data's purpose and applies to all cases.

## Source

- Boundary conditions: Huang et al., AIP Advances (2025), DOI 10.1063/5.0271778.
- Baseline cross-checked against Aditya's COMSOL export.
- Solver: ANSYS Fluent 2026 R1 (Student).

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21955315.svg)](https://doi.org/10.5281/zenodo.21955315)


