# Orbit. an N-body gravity simulator in C++

A from-scratch simulator of stars, planets and moons pulling on each other under Newtonian gravity. It starts with two bodies in the terminal and grows into a real-time 3D view of the solar system, started from NASA JPL ephemeris data and checked against it.

## Goal

Learn modern C++, numerical integration and performance work by building a physics engine I understand and can explain line by line, and **measure** how good it is instead of just making it look right.

## What it does

- Simulates N bodies under mutual gravity (direct O(N²) summation, with Newton's third law used to halve the work).
- Offers several integrators behind one interface: **Explicit Euler, Semi-implicit Euler, Velocity Verlet / Leapfrog, RK4**.
- Tracks conserved quantities (total energy, angular momentum, momentum) and reports their drift over time.
- Loads real initial conditions for the Sun, the planets and major moons from **JPL Horizons**.
- Validates itself: after a simulated year, compares predicted positions to Horizons' ephemeris and reports the error.
- Renders in real time with **raylib**: 3D camera, orbit trails, pause, time-warp and integrator switching.
- Scales to thousands of bodies (a star cluster demo) with profiled, benchmarked optimizations.

## Tech stack

| Area | Choice |
|---|---|
| Language | C++20 (GCC 12+ / Clang 15+ / MSVC 2022) |
| Build | CMake 3.20+ |
| Graphics | raylib (C API, wrapped in my own C++ renderer class) |
| Tests | Catch2 v3 |
| Benchmarks | Google Benchmark, `perf` / Instruments / VS Profiler |
| Analysis | Python + matplotlib for energy-drift and error plots |
| Quality | `-Wall -Wextra -Wpedantic`, AddressSanitizer / UBSan, clang-format |

## Data sources

- **JPL Horizons API**, Cartesian state vectors (`EPHEM_TYPE='VECTORS'`, `CENTER='500@0'` = solar-system barycenter): https://ssd-api.jpl.nasa.gov/doc/horizons.html
- **JPL planetary physical parameters** (GM values): https://ssd.jpl.nasa.gov/planets/phys_par.html
- Units: AU, days, solar masses, with G = k² ≈ 2.9591220828559e-4 AU³ / (M☉·day²), where k is the Gaussian gravitational constant.

## What I'll learn

- **C++:** value types, operator overloading, const-correctness, RAII, move semantics, interfaces (virtual vs templates), `std::vector` and memory layout, CMake, testing.
- **Numerics:** truncation vs round-off error, integrator order, stability, symplectic integrators (and why Verlet beats RK4 over long runs), timestep choice, softening.
- **Performance:** profiling before optimizing, cache behavior, AoS vs SoA layouts, compiler optimization flags, benchmarking honestly, and (stretch) multithreading or Barnes–Hut O(N log N).
- **Design:** separating simulation from rendering, the fixed-timestep game loop, a testable core library.

## Why it's impressive

- **Validated against NASA data**, with a stated error ("Earth within X km of Horizons after one year"), not just a pretty animation.
- **An integrator study with plots** showing energy drift for each method: real numerical-methods reasoning.
- **A measured performance story:** "From N to M bodies at 60 FPS: profiled, found the bottleneck, here are the before/after numbers."
- **Clean architecture:** a physics core with unit tests and no graphics dependency, and a thin renderer on top.

## Roadmap

1. Setup + `Vec3` + two bodies with Euler (terminal)
2. Integrators + conservation diagnostics + plots
3. Refactor into a clean, tested core
4. Real solar system from JPL Horizons + validation
5. Real-time 3D rendering with raylib
6. Many-body performance: profile, optimize, benchmark
7. Stretch: Barnes–Hut or multithreading, plus polish and demo
