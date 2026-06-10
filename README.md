# mppi-path-tracking

Path-tracking MPPI examples, cost functions, and analysis tooling built on top of [MPPI-Generic](https://github.com/ACDSLab/MPPI-Generic).

## Layout

```
lib/MPPI-Generic/     git submodule (upstream MPPI library)
include/mppi/         project extensions (costs, dynamics, viz, data logging)
examples/             CUDA path-tracking demos
scripts/mppi/         rollout / lambda analysis plots
src/                  reserved for future compiled project code
```

Project headers under `include/mppi/` are on the include path **before** `lib/MPPI-Generic/include`, so local overrides (e.g. tuned `racer_dubins.cuh` parameters) take precedence.

## Build

```bash
git submodule update --init --recursive

cmake -S . -B build \
  -DMPT_BUILD_EXAMPLES=ON \
  -DMPPI_BUILD_EXAMPLES=OFF

cmake --build build --target first_order_dubins_two_lane_double_park_example -j
```

`cmake/MPPIGenericToolsConfig.cmake` is a patched copy of MPPI-Generic's CUDA arch helper (fixes `75` → `7.5` normalization). The root `CMakeLists.txt` applies those flags at the top level and sets `CMAKE_CUDA_ARCHITECTURES=OFF` so CMake does not add a conflicting default `sm_52` codegen.

Binaries land in `build/examples/`.

### Dependencies

Same as MPPI-Generic plus OpenCV 4.5.4+ for visualization examples: CUDA, Eigen3, yaml-cpp (pulled in transitively by MPPI examples infrastructure), cnpy (submodule of MPPI-Generic).

## Run an example

```bash
./build/examples/first_order_dubins_two_lane_double_park_example [seed] [log.csv]
```

Rollout dumps are written next to the log file (e.g. `*_rollouts/`).

## Analysis scripts

From the repo root:

```bash
python3 scripts/mppi/plot_mppi_lambda_retune.py first_order_dubins_two_lane_double_park_log.csv --step 42
python3 scripts/mppi/plot_mppi_rollouts_at_step.py first_order_dubins_two_lane_double_park_log.csv --step 42
```

## Submodule updates

```bash
cd lib/MPPI-Generic
git fetch origin
git checkout <tag-or-commit>
cd ../..
git add lib/MPPI-Generic
git commit -m "Bump MPPI-Generic submodule"
```
