# CZ melt: hot wall temperature sweep

> **Superseded by [CZ-Dataset-Doe](https://github.com/bertwinshine/CZ-Dataset-Doe).** These cases were stopped at Fluent's default residual target of 1e-3. Rerun to 1e-6 with the same settings, the cases at 1765, 1770, 1775, 1780 and 1785 K do not settle to a steady solution, so treat them as not steady. The cases up to 1760 K converge, with velocity fields typically 1 to 5 % different. This repository stays online unchanged as version 1.

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21955315.svg)](https://doi.org/10.5281/zenodo.21955315)

CFD cases of a Czochralski (CZ) silicon melt where only the hot crucible wall
temperature changes from case to case. I made them as reference data for a
surrogate / PINN model.

At the original 1e-3 residual target the melt looked steady up to 1785 K and
was clearly unsteady above that, so the data is split into two folders (see the
note above about 1765 to 1785 K):

| Folder | Range | Cases | Type |
|---|---|---:|---|
| `Steady/` | 1730 to 1785 K | 11 | steady runs to 1e-3, certified |
| `transient/` | 1790 to 1800 K | 3 | transient runs, time-averaged |

Each folder has its own README. Keep the transient files separate when building
a steady POD basis, since they are a different kind of data.

## Shared anchor

The 1745 K case is the shared anchor of the three sweeps. It is the same field
as the 8 rpm case in [CZ_Crystal_Sweep](https://github.com/bertwinshine/CZ_Crystal_Sweep)
and the −3 rpm case in [CZ_study_Crucible_Sweep](https://github.com/bertwinshine/CZ_study_Crucible_Sweep):
crystal +8 rpm, crucible −3 rpm, hot wall 1745 K.

The 1750 K case used to be called the baseline. It is now just one point in the
sweep. It was run on an earlier mesh: same node count (8181) but different node
positions, so it cannot be stacked with the other cases in a snapshot matrix. It
is not part of [CZ_Silicon_Melt_CFD_Dataset](https://github.com/bertwinshine/CZ_Silicon_Melt_CFD_Dataset)
or the surrogate.

## Setup (same for every case)

- 2D axisymmetric silicon melt, crucible radius 0.30 m, melt height 0.15 m.
- Boundary conditions from Huang et al., AIP Advances 15, 105202 (2025),
  https://doi.org/10.1063/5.0271778.
- Laminar, Boussinesq buoyancy.
- Crystal face fixed at 1685 K.
- Rotation fixed: crystal +8 rpm, crucible −3 rpm.
- Only the hot crucible wall temperature changes.
- ANSYS Fluent 2026 R1 (Student licence).

## Columns

One row per mesh node, same columns in every file:

| Column | Meaning | Unit |
|---|---|---|
| `r` | radial position, 0 to 0.30 | m |
| `z` | axial position, 0 to 0.15 | m |
| `u_r` | radial velocity | m/s |
| `u_z` | axial velocity | m/s |
| `u_swirl` | swirl velocity | m/s |
| `p` | pressure | Pa |
| `T` | temperature | K |

Fluent does not always write the columns in the same order. If you export more
cases, check that `r`/`z` and `u_r`/`u_z` are not swapped. `certify_case.py`
catches this.

## Checks

For every steady case, `Steady/certify_case.py` reads the temperature from the
file name, works out the thermal boundary conditions from it and checks:

- crystal face at 1685 K and hot wall at the file-name temperature,
- free-surface and crucible-bottom thermal ramps match the prescribed profiles,
- domain size (r to 0.30, z to 0.15),
- radial and swirl velocity are zero on the axis,
- swirl matches the fixed rotation,
- no NaN/inf and no duplicate nodes.

```
cd Steady
python certify_case.py *.csv
```

All 11 steady cases pass.

Mesh independence was checked on the baseline mesh. Peak swirl is about
0.1476 m/s in every steady case because the rotation is fixed and the swirl
boundary condition does not depend on temperature.

## Comparison with COMSOL

The baseline field was compared with a COMSOL result from Aditya Seshaditya
(Quasi project). It matches in the bulk to about 1 K (correlation R ≈ 0.91),
with about 5 K difference at the top corner where the crystal meets the free
surface. This is acceptable for the intended use and applies to all cases.

## Limitations

- Only the hot wall temperature changes; rotation is fixed.
- The free surface is a prescribed wall. No turbulence model, radiation,
  Marangoni convection or magnetic field.
- Student-licence mesh, 8181 nodes.
- The ML model built on this data is not part of this repo. See
  [CZ_Surrogate_App](https://github.com/bertwinshine/CZ_Surrogate_App).

## Credit

The ANSYS setup, sweep design and validation are my work. I wrote the Python
with help from an AI assistant and have checked and understand all of it.

Bertwin Kurisinkal Shine, TU Chemnitz
