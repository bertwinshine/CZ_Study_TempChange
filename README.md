# CZ melt — temperature study data

This repo has 5 CFD reference cases for the Czochralski (CZ) silicon melt.
They're meant to be used as ground-truth data for the surrogate/PINN model.

All 5 cases are the same setup, and I only changed the hot temperature each time.
Everything else stays the same, so this is a clean set where only one thing (the
temperature difference) changes.

## The case

- 2D axisymmetric silicon melt in a crucible.
- Boundary conditions taken from Huang et al. (AIP Advances, 2025).
- Laminar, steady-state, buoyancy handled with the Boussinesq approximation.
- Crucible radius 0.30 m, melt height 0.15 m.
- Solved in ANSYS Fluent 2026 R1 (Student license).

## The 5 cases

I only changed the hot crucible temperature. The cold crystal stays at 1685 K and
the rotation stays the same (crystal +8 rpm, crucible −3 rpm) in every case.

| File | Hot side | Cold side | ΔT |
|---|---|---|---|
| cz_dT55_temp_1740_.csv | 1740 K | 1685 K | 55 K |
| cz_dT60_temp_1745_.csv | 1745 K | 1685 K | 60 K |
| cz_baseline.csv        | 1750 K | 1685 K | 65 K |
| cz_dT70_temp_1755_.csv | 1755 K | 1685 K | 70 K |
| cz_dT75_temp_1760_.csv | 1760 K | 1685 K | 75 K |

cz_baseline.csv is the validated baseline (65 K). The others go above and below it
in steps of 5 K.

## Columns in each CSV

Same columns in every file, one row per mesh node:

- r — radial position (m)
- z — axial position (m)
- u_r — radial velocity (m/s)
- u_z — axial velocity (m/s)
- u_swirl — swirl velocity (m/s)
- p — pressure (Pa)
- T — temperature (K)

Same mesh in all 5 files (8181 nodes), so the node positions line up between cases.
r goes 0 to 0.30, z goes 0 to 0.15. In every file the hottest point is at the
crucible edge and the coldest is at the crystal, which is what it should be.

## How I checked each case

For each case I made sure:
- the solution converged (residuals settled),
- the temperatures came out right (hot side and cold side match what I set),
- the domain size is correct,
- the result looks physically sensible (hot at crucible, cold at crystal).

Mesh independence was checked on the baseline mesh, and all 5 cases use that same
mesh.

## One thing to know

The baseline was compared against a COMSOL result from Aditya. It matched well in
the bulk (about 1 K difference, correlation R = 0.91), but there's about a 5 K
difference at the top boundary where the crystal and free surface meet. Aditya said
this is fine for what the data is used for. It applies to all 5 cases, so I'm noting
it here.

## What this is / isn't

- This is the temperature study — 5 cases where only ΔT changes.
- The rotation study (changing rpm) isn't done yet.
- These are reference CFD fields. Building the actual ML model from them is the ML
  side, which isn't part of this repo.

## Source

- Boundary conditions: Huang et al., AIP Advances (2025).
- Baseline compared against Aditya's COMSOL export.
- Solver: ANSYS Fluent 2026 R1 (Student).

— Bertwin
