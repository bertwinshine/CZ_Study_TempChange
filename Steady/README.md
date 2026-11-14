# Steady cases, 1730 to 1785 K

Eleven steady cases. Only the hot crucible wall temperature changes; the
crystal stays at 1685 K and the rotation at crystal +8 rpm, crucible −3 rpm.

| File | Hot wall | Crystal | ΔT | Note |
|---|---:|---:|---:|---|
| `cz_(temp 1730).csv` | 1730 K | 1685 K | 45 K | |
| `cz_(temp 1740).csv` | 1740 K | 1685 K | 55 K | |
| `cz_(temp 1745).csv` | 1745 K | 1685 K | 60 K | shared anchor |
| `cz_(temp 1750).csv` | 1750 K | 1685 K | 65 K | earlier mesh, see below |
| `cz_(temp 1755).csv` | 1755 K | 1685 K | 70 K | |
| `cz_(temp 1760).csv` | 1760 K | 1685 K | 75 K | |
| `cz_(temp 1765).csv` | 1765 K | 1685 K | 80 K | |
| `cz_(temp 1770).csv` | 1770 K | 1685 K | 85 K | |
| `cz_(temp 1775).csv` | 1775 K | 1685 K | 90 K | |
| `cz_(temp 1780).csv` | 1780 K | 1685 K | 95 K | |
| `cz_(temp 1785).csv` | 1785 K | 1685 K | 100 K | |

All files have 8181 nodes and the columns `r, z, u_r, u_z, u_swirl, p, T`.
The 1750 K case is on an earlier mesh with different node positions, so don't
stack it with the others in a POD snapshot matrix. The other ten share one mesh.

To check the cases:

```
python certify_case.py *.csv
```

The script reads the temperature from the file name, so keep the
`cz_(temp NNNN).csv` naming if you add cases.

## Why the sweep stops at 1785 K

I also ran 1790 K as a steady case. The residuals dropped to a minimum and then
rose and oscillated instead of settling. A monitor point in the melt showed the
temperature swinging in a regular wave of about 1.7 K peak-to-peak and never
settling. The melt is unsteady at that temperature, so a steady solver has no
solution to converge to.

At the 1e-3 target, 1785 K was the last case that looked steady. A later rerun
to 1e-6 (in [CZ-Dataset-Doe](https://github.com/bertwinshine/CZ-Dataset-Doe))
showed that the cases from 1765 K upwards do not settle either, so the steady
range at these rotation settings ends between 1760 and 1765 K. The 1790, 1795 and 1800 K
cases were rerun as transient and are in `../transient/`.

<img width="992" height="832" alt="1790 K steady run not converging" src="https://github.com/user-attachments/assets/255fbede-7dc3-496d-9410-46122888abc6" />

Bertwin Kurisinkal Shine
