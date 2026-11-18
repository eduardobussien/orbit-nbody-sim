# Orbit — Nightly Roadmap

Each step is sized for **30–45 minutes**, ends with **something that builds and runs**, and ends with **one commit**. At home with more time, do 2–3 steps in one sitting. Don't skip steps.

**Rules for every night**
1. Before you code: read the "Learn first" item (5–10 min max).
2. Code until "Done when" is true. It has to build without warnings.
3. Commit with the message shown, written in the imperative mood ("Add X", not "Added X").
4. If you run out of time, commit what builds with `WIP:` at the front of the message, and finish the step next night.

Commit messages are suggestions; write your own if they fit better.

---

## Milestone 1: Setup, `Vec3`, two bodies with Euler (≈11 nights)

| # | Tonight | Learn first | Done when | Commit |
|---|---|---|---|---|
| 1.1 | Create repo, README, LICENSE, .gitignore, .gitattributes, push | Git: staging vs committing | Repo is on GitHub with the README rendered | `Add README, license, and git config files` |
| 1.2 | CMake hello world in `src/main.cpp` | What CMake *is* (it generates builds, it doesn't build) | `./build/orbit` prints a greeting; `build/` doesn't show in `git status` | `Add CMake hello world` |
| 1.3 | Turn on warnings (`-Wall -Wextra -Wpedantic` / MSVC `/W4`); add an unused variable to see a warning, then remove it; add build steps to README | learncpp: compiler warnings and build configurations | You've seen a warning and fixed it; README has build steps | `Enable compiler warnings` |
| 1.4 | `struct Vec3` in `src/vec3.h`; create one in main and print it | learncpp: structs, header files, header guards | A `Vec3` prints as `(1, 2, 3)` | `Add Vec3 struct` |
| 1.5 | Operators `+`, `-`, `* scalar`, `/ scalar`; check them with `assert` in main | learncpp: operator overloading (arithmetic) | All asserts pass | `Add Vec3 arithmetic operators` |
| 1.6 | `length()`, `dot()`, `cross()` | learncpp: `const` member functions; free vs member functions | Asserts pass, including `length({3,4,0}) == 5` | `Add Vec3 length, dot, cross` |
| 1.7 | `struct Body { position, velocity, mass }`; Sun and Earth in AU / day / solar-mass units; `constexpr` G | learncpp: `constexpr` | Prints both bodies' starting state | `Add Body struct and Sun-Earth initial state` |
| 1.8 | Function that returns the gravitational acceleration on body A from body B | learncpp: pass by value vs const reference | Earth's acceleration ≈ 2.96e-4 AU/day² (check by hand first!) | `Add gravitational acceleration function` |
| 1.9 | Euler loop: 365 steps of 1 day; print Earth every 30 days | learncpp: `for` loops (review) | Earth goes roughly around the Sun (and drifts) | `Add explicit Euler time loop` |
| 1.10 | Move to `std::vector<Body>` with a double loop over all pairs | learncpp: `std::vector` intro; range-for with `auto&` vs `auto` | Same output as 1.9, but works for any N; add Mars to check | `Generalize to N bodies with std::vector` |
| 1.11 | Write positions to CSV with `std::ofstream`; plot in Python | learncpp: basic file I/O | A plot of Euler's orbit spiraling outward | `Add CSV output and orbit plot script` → tag `v0.1` |

## Milestone 2: Integrators and conservation (≈8 nights)

| # | Tonight | Learn first | Done when | Commit |
|---|---|---|---|---|
| 2.1 | `totalEnergy()`: kinetic + potential | Physics: KE = ½mv², PE = −G·m₁·m₂/r per *pair* | Prints energy at start and end; they differ | `Add total energy diagnostic` |
| 2.2 | Log the relative energy error each step; plot it | Relative vs absolute error | Plot of |ΔE/E₀| over time | `Log energy error to CSV` |
| 2.3 | Semi-implicit (symplectic) Euler | Why update order matters | Both methods plotted together; spot the difference | `Add semi-implicit Euler` |
| 2.4 | `computeAccelerations(bodies)` → `std::vector<Vec3>`; Velocity Verlet | Verlet derivation (kick–drift–kick) | Verlet's energy error stays bounded | `Add velocity Verlet integrator` |
| 2.5 | RK4, part 1: a `State` type and a `derivative(state)` function | RK4 as "four slope samples" | Derivative function prints sane values | `Add state derivative for RK4` |
| 2.6 | RK4, part 2: the full step | Order of accuracy (1st vs 2nd vs 4th) | RK4 is very accurate over short runs but drifts over long ones | `Add RK4 integrator` |
| 2.7 | Angular momentum diagnostic | Cross product meaning | L stays constant for the good methods | `Add angular momentum diagnostic` |
| 2.8 | Choose integrator and dt from the command line (`argc/argv`); run the experiment grid; write `RESULTS.md` | learncpp: command line arguments | RESULTS.md has plots and 3 sentences of conclusions | `Add integrator comparison results` → tag `v0.2` |

## Milestone 3: Clean design and tests (≈8 nights)

| # | Tonight | Learn first | Done when | Commit |
|---|---|---|---|---|
| 3.1 | Split into `.h`/`.cpp` files; CMake `add_library` (core) + `add_executable` linking it | Compile vs link; translation units | Same output, cleaner tree | `Split physics into core library` |
| 3.2 | `class System` with private bodies and a public interface | learncpp: classes, access specifiers, constructors | main only talks to `System` | `Introduce System class` |
| 3.3 | Abstract `Integrator` with `virtual step()`; one subclass per method | learncpp: virtual functions, `override`, virtual destructors | Switching integrators means changing one line | `Add Integrator interface` |
| 3.4 | Own the integrator via `std::unique_ptr` | learncpp: smart pointers, RAII, move semantics intro | No raw `new`/`delete` anywhere | `Use unique_ptr for integrator ownership` |
| 3.5 | Catch2 via CMake `FetchContent`; first `Vec3` tests | What a unit test is; `ctest` | `ctest` passes | `Add Catch2 and Vec3 tests` |
| 3.6 | Physics tests: Verlet energy within tolerance; Earth returns near its start after one Kepler period | Choosing tolerances | Physics tests pass | `Add physics conservation tests` |
| 3.7 | Build with AddressSanitizer + UBSan; fix anything found | What undefined behavior is | Clean sanitizer run | `Enable sanitizers in debug builds` |
| 3.8 | GitHub Actions CI: build + test on macOS, Windows, Ubuntu | CI basics; YAML | Green checkmark on all 3 OSes | `Add cross-platform CI` → tag `v0.3` |

## Milestone 4: Real solar system from JPL Horizons (≈7 nights)

| # | Tonight | Learn first | Done when | Commit |
|---|---|---|---|---|
| 4.1 | Use the Horizons web app to get Earth's vectors by hand; save to `data/` | Barycentric frame, ICRF, AU/day | You can explain every column | `Add sample Horizons vector file` |
| 4.2 | Python script that fetches the Sun, planets and Moon via the Horizons API → one CSV | Horizons API parameters (`EPHEM_TYPE`, `CENTER`) | `data/initial_state.csv` exists | `Add Horizons fetch script` |
| 4.3 | C++ CSV loader (`std::getline`, `std::stod`) → `System` | Error handling for bad input | Loads all bodies; prints a count | `Load initial state from CSV` |
| 4.4 | Masses from JPL GM values; units sanity check | JPL physical parameters page | Sun GM matches k² | `Add JPL GM values` |
| 4.5 | Simulate 1 year with Verlet; compare to Horizons at +1 year | Position error in km | Table of per-body errors | `Add one-year validation against Horizons` |
| 4.6 | dt experiment: error vs timestep (watch the Moon) | Why the fastest orbit sets dt | Error-vs-dt plot | `Add timestep convergence study` |
| 4.7 | Validation section in README | Communicating results | README states the error clearly | `Document validation results` → tag `v0.4` |

## Milestone 5: Real-time graphics with raylib (≈9 nights)

| # | Tonight | Learn first | Done when | Commit |
|---|---|---|---|---|
| 5.1 | Fetch raylib via CMake; open an empty window | raylib "basic window" example | Window opens and closes cleanly | `Add raylib window` |
| 5.2 | 2D top-down: draw the Sun and Earth as circles (AU → pixels) | World vs screen coordinates | Static picture of current state | `Draw bodies in 2D` |
| 5.3 | Fixed-timestep loop with an accumulator | "Fix Your Timestep" (Gaffer on Games) | Orbits animate at a steady rate | `Add fixed-timestep simulation loop` |
| 5.4 | `Renderer` class; the core library has zero raylib includes | Dependency direction | Core still builds without raylib | `Extract Renderer class` |
| 5.5 | Orbit trails with a ring buffer | Ring buffers | Trails fade behind planets | `Add orbit trails` |
| 5.6 | Controls: pause, time-warp, switch integrator | Input handling | Keys work | `Add simulation controls` |
| 5.7 | Switch to 3D: `Camera3D`, spheres, orbit camera | raylib 3D camera example | Rotatable 3D solar system | `Switch to 3D rendering` |
| 5.8 | Display scaling, labels, HUD showing energy error | Why true scale is invisible | Readable, informative view | `Add HUD and display scaling` |
| 5.9 | Record a demo GIF; add to README | — | GIF at top of README | `Add demo GIF` → tag `v0.5` |

## Milestone 6: Many-body performance (≈8 nights)

| # | Tonight | Learn first | Done when | Commit |
|---|---|---|---|---|
| 6.1 | Random star-cluster generator (`std::mt19937`) with softening | Why softening exists | 1,000 bodies render | `Add star cluster generator` |
| 6.2 | Time steps with `std::chrono`; measure N = 100, 1k, 5k | Big-O in practice | Plot confirms ~N² scaling | `Add step timing benchmark` |
| 6.3 | Debug vs Release builds, measured | What `-O2`/`-O3` do | Numbers for both | `Document build-type performance` |
| 6.4 | Profile (Instruments on Mac / VS profiler on Windows) | Reading a profile | You can name the hotspot | `Add profiling notes` |
| 6.5 | Use Newton's 3rd law to halve the pair loop | — | ~2× speedup measured | `Halve force pairs with Newton's third law` |
| 6.6 | AoS → SoA experiment | Cache lines, memory layout | Before/after numbers | `Experiment with SoA layout` |
| 6.7 | Google Benchmark micro-benchmarks; `-march=native` | Benchmarking pitfalls | Reproducible benchmark output | `Add Google Benchmark suite` |
| 6.8 | Performance section in README | — | Table of before/after results | `Document performance results` → tag `v0.6` |

## Milestone 7 (stretch): pick one path (≈6–8 nights)

**Path A, multithreading:** `std::jthread` basics → split the force loop by index range → discover why the Newton's-third-law trick causes a **data race** → measure speedup vs cores → Amdahl's law write-up.

**Path B, Barnes–Hut:** octree data structure → tree build → force traversal with opening angle θ → accuracy vs speed plot against direct summation.

**Finish either path with:** final README polish, résumé bullets, tag `v1.0`.
