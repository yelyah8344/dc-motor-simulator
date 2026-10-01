# DC Motor Dynamic Simulator

A MATLAB App Designer application that simulates the coupled electrical and
mechanical dynamics of a **permanent-magnet (armature-controlled) DC motor**,
solving the governing differential equations with numerical integration methods
implemented from first principles.

Final project for **EEE 212 — Numerical Techniques Laboratory**, Department of
Electrical and Electronic Engineering, Bangladesh University of Engineering and
Technology (BUET).

---

## The model

The motor is modelled as four coupled states — armature current, angular speed,
shaft position and winding temperature:

```
Va(t) = La·dia/dt + Ra·ia + Ke·ω        (armature circuit)
J·dω/dt = Kt·ia − B·ω − TL(t, ω)        (rotor dynamics)
dθ/dt = ω                               (shaft position)
Cth·dT/dt = ia²·Ra − (T − Tamb)/Rth     (winding temperature, optional)
```

Because `Kt` and `Ke` are constants, the field is constant — this is a
permanent-magnet or separately-excited machine, not a shunt, series or
compound one.

## Numerical methods

| Method | Evaluations per step | Global error |
|---|---|---|
| Forward Euler | 1 | O(h) |
| Classical RK4 | 4 | O(h⁴) |
| Adaptive RK45 (Runge–Kutta–Fehlberg) | 6 | variable step |

All three are implemented directly in `motorDerivative` + the solver functions.
`ode45` is used **only** as an independent reference in the time-step study,
never as the project's solver.

A time-step convergence study confirms the measured orders: the error-versus-step-size
line has slope 1 for Euler and slope 4 for RK4 on log–log axes.

## Features

- Voltage waveforms: step, ramp, pulse, imported CSV, user-defined
- Load characteristics: constant, linear in ω, quadratic in ω, constant power, imported
- Optional thermal model (winding temperature raises `Ra`)
- Optional armature current limiting
- Optional Coulomb friction
- Real-time 3D shaft animation
- Euler vs RK4 comparison and time-step convergence study
- CSV / XLSX export of results; import of measured motor data

## Repository layout

```
DCMotorSimulator.mlapp   the application (open in MATLAB App Designer)
src/                     plain-text copy of the source, for browsing and diffing
data/                    motor parameters, test cases, example input profile
docs/                    figures
```

`.mlapp` is a binary container, so GitHub cannot diff it. `src/` holds a
readable copy of the same code so changes are visible in pull requests.
It is a copy for reading only — edit the `.mlapp`.

## Running it

Requires MATLAB R2021a or later (developed on R2025b). No toolboxes beyond
base MATLAB are needed to run it.

```matlab
>> DCMotorSimulator
```

or open `DCMotorSimulator.mlapp` in App Designer and press **Run**.

The app opens on test case M1: Motor A, 24 V step, 0.05 N·m load applied at
t = 1 s, 3 s duration, h = 1e-4 s.

## Test cases

| Case | Motor | Voltage | Load | Duration | h |
|---|---|---|---|---|---|
| M1 | A | 24 V step | 0.05 N·m @ 1.0 s | 3.0 s | 1e-4 |
| M2 | A | 18 V ramp over 0.5 s | 0.08 N·m @ 1.2 s | 3.0 s | 2e-4 |
| M3 | B | 24 V pulse, 0.2–1.5 s | 0.03 N·m @ 0.8 s | 2.5 s | 2e-4 |
| M4 | A | imported profile | imported profile | 3.0 s | 1e-3 |

## Motor presets

| | Ra (Ω) | La (H) | Kt = Ke | J (kg·m²) | B (N·m·s/rad) |
|---|---|---|---|---|---|
| Motor A | 1.2 | 0.015 | 0.08 | 0.002 | 0.002 |
| Motor B | 2.0 | 0.025 | 0.10 | 0.004 | 0.003 |

## Building a standalone Windows application

Requires the MATLAB Compiler toolbox.

```matlab
>> mcc -m DCMotorSimulator.mlapp -o DCMotorSimulator -d build
```

Or in App Designer: **Share → Standalone Desktop App**. The resulting installer
bundles the MATLAB Runtime, so the end user does not need MATLAB.

## Authors

Group 04, Section C2, Level 2 Term 1

- Shuvro Pain — 2406177
- Taseen Intisar — 2406188
- Abdus Samiul Hasan Sun — 2406193
- Nadman Wasit — 2406195

## Licence

MIT — see [LICENSE](LICENSE).
