# Transient cases (1790–1800 K) — weakly unsteady, time-averaged

These are the higher-temperature CZ melt cases. Above about 1785 K the melt no longer
settles into one steady picture — the flow keeps slowly moving and oscillating. So these
three cases were run differently from the steady set, and the data here is time-averaged.

| File | Hot side | Cold side | ΔT | Type |
|---|---|---|---|---|
| cz_temp_1790_mean_Unsteady_.csv | 1790 K | 1685 K | 105 K | time-averaged |
| cz_temp_1795_mean_Unsteady_.csv | 1795 K | 1685 K | 110 K | time-averaged |
| cz_temp_1800_mean_Unsteady_.csv | 1800 K | 1685 K | 115 K | time-averaged |

## Why these are separate from the steady set

In the steady set (1740–1785 K) the melt settles to one unchanging field, so each case
is a single steady solution. From about 1790 K upward the stronger buoyancy makes the
melt restless: the flow oscillates gently and never settles to one fixed answer. A
steady-state solver cannot converge such a case, because there is no single steady
field to find.

So these cases were run as time-dependent (transient) simulations instead. The flow was
allowed to develop until the oscillation settled around a constant level (a
statistically-steady state), and then the fields were averaged over several full
oscillation cycles. Each CSV here is that time-averaged (mean) field — not a single
snapshot.

## How each case was made

For each temperature (1790, 1795, 1800 K):

1. Ran the case as transient (time step 0.05 s), continuing from the previous case.
2. Let the melt pass through its startup and settle into a regular oscillation, with the
   baseline no longer drifting. The hotter cases took longer to settle (1790 K settled
   fastest, 1800 K slowest), because stronger buoyancy takes more time to reach a
   statistically-steady state.
3. Turned on Data Sampling for Time Statistics only after the baseline was flat.
4. Sampled over several full oscillation cycles.
5. Exported the mean field (mean temperature, mean velocities, mean pressure).

A monitor point inside the melt was used to watch the oscillation and confirm the flow
was settled before sampling.

## The oscillation

At these temperatures the melt point oscillates by roughly 1.5–2 K peak-to-peak, with a
period of about 30 s. The oscillation is regular and small, riding on a constant mean —
so the time-average is a meaningful, stable field.

## Columns

Same as the steady set, one row per node:

- r — radial position (m), 0 to 0.30
- z — axial position (m), 0 to 0.15
- u_r — mean radial velocity (m/s)
- u_z — mean axial velocity (m/s)
- u_swirl — mean swirl velocity (m/s)
- p — mean pressure (Pa)
- T — mean temperature (K)

Same mesh as the steady cases (8181 nodes), so node positions line up across all cases.
The maximum swirl velocity is about 0.1468 m/s, essentially the same as the steady cases
(rotation is fixed), so the flow is still laminar — just unsteady.

## Important note on data type

These are time-averaged unsteady fields, which is a different kind of data from the
steady cases. They are kept in this separate folder for that reason. Whether and how a
surrogate model should use time-averaged unsteady data alongside steady data is an ML-side
decision, not made here.

## Where these sit in the study

- 1740–1785 K → steady, in the `steady/` folder.
- 1790–1800 K → weakly unsteady, time-averaged, here.
- The transition from steady to unsteady happens around 1785–1790 K. This marks the top
  edge of the steady range, and is itself a useful result: it shows where the steady
  reference data can be trusted, and where time-dependent behaviour begins.

## Source

- Boundary conditions: Huang et al., AIP Advances (2025).
- Solver: ANSYS Fluent 2026 R1 (Student), transient, axisymmetric swirl, Boussinesq buoyancy.

— Bertwin Shine
