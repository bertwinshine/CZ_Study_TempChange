# Transient cases, 1790 to 1800 K (time-averaged)

Above about 1785 K the melt no longer settles to a steady field. The flow keeps
oscillating slowly, so these three cases were run as transient and each file is
a time-averaged field, not a snapshot.

| File | Hot wall | Crystal | ΔT |
|---|---:|---:|---:|
| `cz_temp_1790_mean(Unsteady).csv` | 1790 K | 1685 K | 105 K |
| `cz_temp_1795_mean(Unsteady).csv` | 1795 K | 1685 K | 110 K |
| `cz_temp_1800_mean(Unsteady).csv` | 1800 K | 1685 K | 115 K |

## How they were run

For each temperature:

1. Ran transient with a 0.05 s time step, starting from the previous case.
2. Waited until the oscillation was regular and the mean had stopped drifting.
   Hotter cases took longer (1790 K settled fastest, 1800 K slowest).
3. Turned on Data Sampling for Time Statistics once the mean was flat.
4. Sampled over several full oscillation cycles.
5. Exported the mean fields (T, velocities, p).

A monitor point inside the melt was used to watch the oscillation and decide
when to start sampling. At these temperatures it oscillates by about 1.5 to 2 K
peak-to-peak with a period of about 30 s, around a constant mean.

## Data

Same columns and the same 8181 mesh nodes as the steady cases:
`r, z, u_r, u_z, u_swirl, p, T`, all as time averages. The rows are in a
different order from the steady files, so sort by `(r, z)` before comparing or
stacking them with steady cases. Peak swirl is about
0.1468 m/s, close to the steady cases because the rotation is fixed.

These are time-averaged unsteady fields, which is a different kind of data from
the steady set. I have not decided whether a surrogate should mix them with the
steady cases; that is left to the model side.

## Setup

Same as the steady cases (Huang et al., AIP Advances 2025 boundary conditions),
run in ANSYS Fluent 2026 R1 Student: transient, 2D axisymmetric swirl, laminar,
Boussinesq.

Bertwin Kurisinkal Shine
