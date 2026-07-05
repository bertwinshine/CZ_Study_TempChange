# CZ melt — temperature study data

This repo has 10 CFD reference cases for the Czochralski (CZ) silicon melt.
They're meant to be used as ground-truth data for the surrogate/PINN model.
All cases are the same setup, and I only changed the hot temperature each time.
Everything else stays the same, so this is a clean set where only one thing (the
temperature difference) changes.

The set was extended after the original five: it now runs from 1740 K up to 1785 K
in steps of 5 K. I also tried 1790 K, which behaved differently.
## The case

- 2D axisymmetric silicon melt in a crucible.
- Boundary conditions taken from Huang et al. (AIP Advances, 2025).
- Laminar, steady-state, buoyancy handled with the Boussinesq approximation.
- Crucible radius 0.30 m, melt height 0.15 m (wide, shallow pool).
- Solved in ANSYS Fluent 2026 R1 (Student license).

## The cases

I only changed the hot crucible temperature. The cold crystal stays at 1685 K and
the rotation stays the same (crystal +8 rpm, crucible −3 rpm) in every case.

| File | Hot side | Cold side | ΔT |
|---|---|---|---|
| cz_temp_1740_.csv | 1740 K | 1685 K | 55 K |
| cz_temp_1745_.csv | 1745 K | 1685 K | 60 K |
| cz_baseline.csv        | 1750 K | 1685 K | 65 K |
| cz_temp_1755_.csv | 1755 K | 1685 K | 70 K |
| cz_temp_1760_.csv | 1760 K | 1685 K | 75 K |
| cz_temp_1765_.csv      | 1765 K | 1685 K | 80 K |
| cz_temp_1770_.csv      | 1770 K | 1685 K | 85 K |
| cz_temp_1775_.csv      | 1775 K | 1685 K | 90 K |
| cz_temp_1780_.csv      | 1780 K | 1685 K | 95 K |
| cz_temp_1785_.csv      | 1785 K | 1685 K | 100 K |

cz_baseline.csv is the validated baseline (65 K). The others step above and below it
in 5 K steps. The certified band runs 1740–1785 K.

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
r goes 0 to 0.30, z goes 0 to 0.15. In every file the hottest point is at the
crucible edge (max r) and the coldest is at the crystal, which is what it should be.

Note on coordinates: all files use the same convention — r is the radial direction
(0 to 0.30, measured from the axis to the crucible wall) and z is the axial direction
(0 to 0.15). If you export more cases from Fluent, check the column order matches this
before adding them — Fluent doesn't always write columns in the same order, so it's
worth confirming r and z (and u_r/u_z) aren't swapped.

## How I checked each case

For each case I made sure:

- the solution converged (residuals settled),
- the temperatures came out right (hot side and cold side match what I set),
- the domain size is correct (r to 0.30, z to 0.15),
- the result looks physically sensible (hot at crucible, cold at crystal),
- the coordinate convention matches the other files (r and u_r point the same way in
  every case).

Mesh independence was checked on the baseline mesh, and all cases use that same mesh.
The maximum swirl velocity is the same (0.1476 m/s) in every case, because the rotation
is fixed and only the temperature changes — a good sign the flow stays laminar as the
hot side gets hotter.

## The 1790 K case (not included)

I also ran 1790 K (ΔT = 105 K). This one did not converge like the others. The
residuals dropped to a minimum and then rose and started oscillating instead of
settling.

To find out why, I put a monitor point inside the melt and watched the temperature
there as the run went on. Instead of settling to a steady value, it kept swinging up
and down in a regular wave (about 1.7 K peak-to-peak) and never settled. That means
the melt has become genuinely unsteady at this temperature — the buoyancy is strong
enough that the flow keeps moving and there is no single steady answer for a
steady-state solver to find. The rising residual was the solver telling me there's no
steady solution, not a mistake in the setup.

So 1790 K is left out of the certified set. Solving it properly would need a transient
(time-dependent) run, which gives a different kind of data. Whether that's useful for
the ML model is a question for the ML side, not decided here.

This is actually a useful result on its own: it shows the steady-state data can be
trusted up to 1785 K, and 1790 K is where that stops. It marks the top edge of the
usable range.

<img width="992" height="832" alt="Screenshot 2026-07-05 161535" src="https://github.com/user-attachments/assets/255fbede-7dc3-496d-9410-46122888abc6" />


## What this is / isn't

- This is the temperature study — 10 cases where only ΔT changes (1740–1785 K), plus
  the 1790 K boundary finding.
- The rotation study (changing rpm) isn't done yet.
- These are reference CFD fields. Building the actual ML model from them is the ML
  side, which isn't part of this repo.

## One thing to know

The baseline was compared against a COMSOL result from Aditya. It matched well in the
bulk (about 1 K difference, correlation R = 0.91), but there's about a 5 K difference
at the top boundary where the crystal and free surface meet. Aditya said this is fine
for what the data is used for. It applies to all cases, so I'm noting it here.

## Source

- Boundary conditions: Huang et al., AIP Advances (2025).
- Baseline compared against Aditya's COMSOL export.
- Solver: ANSYS Fluent 2026 R1 (Student).
- Ai helper Claude
Bertwin Shine
