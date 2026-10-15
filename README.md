# Brownian Motion in an Optical Tweezer

A numerical study of a Brownian particle in a finite-depth Gaussian-beam optical trap,
modelled as overdamped Langevin dynamics. Two questions are asked in every setting.

- **Entry, or loading.** The particle starts uniformly distributed in a closed domain with
  reflecting walls. How fast does the trap load, and to what steady-state fraction?
- **Exit, or escape.** The particle starts from the steady state with absorbing walls. What
  is the mean escape time?

Every scenario is solved two independent ways. One route is a Fokker-Planck finite-volume
PDE solve using the Chang-Cooper scheme. The other is a direct Langevin (Euler-Maruyama)
particle ensemble. Each route is cross-checked against the other, and against closed-form
results wherever they exist. Every script is self-contained.

**[Read the report (PDF)](report.pdf)**. It covers the model, the methods and the results.

## Running it

Python 3.12 or newer is required. Install the three dependencies first with [uv](https://docs.astral.sh/uv/).

```sh
uv sync
```

Run each script from the repository root, since the figure output path is relative to the
working directory.

```sh
python entry_problem_1d.py
python exit_problem_1d.py
python entry_problem_3d.py
python exit_problem_3d.py
```

Each script prints its comparison table, hard-asserts its cross-validation tolerances, and
writes its figures into `figures/`. A script that runs to completion without an
`AssertionError` has reproduced every number it reports.

## Layout

**`common.py`** holds everything shared. That covers the physical constants, the `Trap`
dataclass and its constructors, the Chang-Cooper exponentially-fitted generator (whose
discrete steady state is exactly Boltzmann for gradient drift), `restrict_generator` for
carving non-rectangular domains out of the tensor grid, the PDE solvers, the SDE
helpers, the spectral-analysis helpers and the shared figure style.

**`entry_problem_1d.py`** and **`exit_problem_1d.py`** handle the 1D box, which is the
analytic anchor. It is the only setting with a closed-form answer, namely the exit MFPT
double quadrature. That answer certifies the discretisation and every escape-time route.

**`entry_problem_3d.py`** and **`exit_problem_3d.py`** treat the 3D production case, a
focused Gaussian-beam trap in cylindrical (rho, z) coordinates on the cylinder
rho < L, |z| < 1.5 L. The waist is 500 nm and the Rayleigh range 982 nm, for a 1064 nm
laser in water. Rotational symmetry about the beam axis folds the 3D problem onto a 2D
solve for the azimuthal marginal q = 2 pi rho P, with entropic drift +D/rho and effective
potential U - kB T ln rho. This produces 3D physics at 2D cost.

