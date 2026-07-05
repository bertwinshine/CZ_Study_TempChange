# CZ melt — temperature study data

This repo has CFD reference cases for the Czochralski (CZ) silicon melt. They are
meant to be used as ground-truth data for the surrogate/PINN model.

All cases use the same setup — a 2D axisymmetric silicon melt in a crucible. Only the
hot crucible temperature is changed from case to case; everything else stays the same.
This makes it a clean set where only one thing (the temperature) varies.

## The two folders

The data is split into two folders, because the melt behaves in two different ways
depending on how hot it gets.

**`steady/`** — the main set. From 1740 K up to 1785 K in steps of 5 K. In this range
the melt settles into one steady picture, so each case is a single steady-state field.
These are the certified reference cases.

**`transient/`** — the higher-temperature set: 1790 K, 1795 K, and 1800 K. Above about
1785 K the melt no longer settles — the flow keeps slowly moving and oscillating, so
there is no single steady answer. These cases were run as time-dependent (transient)
simulations and then time-averaged over several oscillation cycles. So each file here
is an averaged field, not a single snapshot.

Each folder has its own README with the details of that set.

## The case (same for all)

- 2D axisymmetric silicon melt in a crucible.
- Boundary conditions taken from Huang et al. (AIP Advances, 2025).
- Buoyancy handled with the Boussinesq approximation.
- Crucible radius 0.30 m, melt height 0.15 m (wide, shallow pool).
- Cold crystal fixed at 1685 K; rotation fixed (crystal +8 rpm, crucible -3 rpm).
- Solved in ANSYS Fluent 2026 R1 (Student license).

## Why two types of data

Silicon melts at about 1685 K. Real CZ crystal growth runs the melt only tens of
degrees above that. In this range (up to ~1785 K) the flow is steady and easy to
trust — that's the `steady/` set.

As the temperature goes higher (1790-1800 K), the stronger buoyancy makes the melt
restless, so the flow becomes weakly unsteady. Those cases can't be given as a single
steady field, so they are time-averaged instead — that's the `transient/` set.

The changeover happens around 1785-1790 K, which is the top edge of the useful steady
range.

## Columns in each CSV

Same columns in every file, one row per mesh node:

- r — radial position (m)
- z — axial position (m)
- u_r — radial velocity (m/s)
- u_z — axial velocity (m/s)
- u_swirl — swirl velocity (m/s)
- p — pressure (Pa)
- T — temperature (K)

Same mesh in all files (8181 nodes), so the node positions line up between cases.
r goes 0 to 0.30, z goes 0 to 0.15. The hottest point is always at the crucible edge
(max r) and the coldest at the crystal.

Note on coordinates: all files use the same convention — r is radial (0 to 0.30) and
z is axial (0 to 0.15). Fluent doesn't always write the columns in the same order, so
if more cases are added, check that r and z (and u_r/u_z) aren't swapped before adding
them.

## How each case was checked

For every case:

- the solution converged (steady cases) or reached a settled, repeating oscillation
  (transient cases),
- the temperatures came out right (hot and cold sides match what was set),
- the domain size is correct (r to 0.30, z to 0.15),
- the result looks physically sensible (hot at crucible, cold at crystal).

Mesh independence was checked on the baseline mesh, and every case uses that same mesh.
The maximum swirl velocity is the same (about 0.1476 m/s) across the steady cases,
because the rotation is fixed and only the temperature changes — a sign the flow stays
laminar as the hot side gets hotter.

## What this is / isn't

- This is the temperature study — the hot temperature is the only thing that changes.
- The rotation study (changing rpm) is not done yet.
- These are reference CFD fields. Building the actual ML model from them is the ML side,
  which is not part of this repo.

## One thing to know

The baseline was compared against a COMSOL result from Aditya. It matched well in the
bulk (about 1 K difference, correlation R = 0.91), but there is about a 5 K difference
at the top boundary where the crystal and free surface meet. This is fine for what the
data is used for, and it applies to all cases.

## Source

- Boundary conditions: Huang et al., AIP Advances (2025).
- Baseline compared against Aditya's COMSOL export.
- Solver: ANSYS Fluent 2026 R1 (Student).

— Bertwin Shine
