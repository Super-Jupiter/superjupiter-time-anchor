# SuperJupiter TimeAnchor

Map device timestamps to UTC with explicit uncertainty and expiry.

**Version 0.1.0: evolving preview. Hardware validation pending.**

Original MIT-licensed portable C++11 by [SuperJupiter](https://www.superjupiter.net).
No mandatory service, hidden clock reads, network calls or heap allocation in the core.

## What it does

An anchor pairs a caller-supplied uint32 monotonic millisecond timestamp with nonnegative int64 UTC milliseconds.
Conversion accepts only forward distances below 2^31 ms and the configured maximum age.
Uncertainty is the anchor uncertainty plus driftPpm times elapsed time, rounded up.
Every setAnchor increments the revision and replaces the mapping; callers must record anchor changes.
No NTP, timezone conversion, clock synchronisation or guarantee of UTC correctness is provided.

## Install

PlatformIO exact release (after registry publication):

```ini
lib_deps = superjupiter/SuperJupiterTimeAnchor@0.1.0
```

Arduino: use the versioned [GitHub release ZIP](https://github.com/Super-Jupiter/superjupiter-time-anchor/releases/tag/v0.1.0).
Library Manager registration and index propagation are separate steps; check the official index before relying on IDE availability.

## Example

```cpp
#include <SuperJupiterTimeAnchor.h>
using namespace superjupiter::time_anchor;
int main() {
TimeAnchor clock(Config(3600000,100));
clock.setAnchor(1000,1700000000000LL,20);
Result result=clock.convert(1500);
(void)result;
return 0;
}

```

For Arduino, open `examples/Minimal`. It uses synthetic data only.
Results are deliberately local so applications can inspect outcomes before adding I/O.

## CMake

```cmake
add_subdirectory(path/to/superjupiter-time-anchor)
target_link_libraries(your_app PRIVATE SuperJupiter::TimeAnchor)
```

For an installed package use `find_package(SuperJupiterTimeAnchor 0.1 CONFIG REQUIRED)`.
Top-level CMake builds tests/examples; a subproject does not enable developer tools by default.

## Documentation and verification

- [API and boundaries](https://github.com/Super-Jupiter/superjupiter-time-anchor/blob/v0.1.0/docs/API.md)
- [Tests](https://github.com/Super-Jupiter/superjupiter-time-anchor/blob/v0.1.0/tests/test.cpp)
- [Validation status](https://github.com/Super-Jupiter/superjupiter-time-anchor/blob/v0.1.0/docs/VALIDATION.md)
- [Shared conventions](https://github.com/Super-Jupiter/superjupiter-time-anchor/blob/v0.1.0/docs/CONVENTIONS.md)
- [Migration policy](https://github.com/Super-Jupiter/superjupiter-time-anchor/blob/v0.1.0/docs/MIGRATION.md)

```sh
cmake -S . -B build/cmake
cmake --build build/cmake
ctest --test-dir build/cmake --output-on-failure
python3 tools/check_release.py --tag v0.1.0
```

Keep raw observations and their provenance in your application. Derived results are not physical truth.
Do not use fast-math or finite-math-only compiler assumptions. AVR double may have float precision.
Software tests, board compilation and physical validation are reported separately.

## Fits the SuperJupiter measurement portfolio

Complements [SensorQuality](https://github.com/Super-Jupiter/superjupiter-sensor-quality) and
[SampleWindow](https://github.com/Super-Jupiter/superjupiter-sample-window) without a required dependency.
Each component can be used independently. Domain models, customer data and proprietary applications are excluded.

## Support and licence

MIT. Report reproducible bugs through repository issues; private security reports: info@superjupiter.net.
Include version, configuration, inputs, expected and observed behaviour. No credentials or private customer data.
