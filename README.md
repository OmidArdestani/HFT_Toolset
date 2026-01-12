# HFT_Toolset

Small C++23 utilities for high-performance / low-latency work. Currently includes:

- **Latency benchmarking** (`benchmark_p99.hpp`) to capture p99 / p99.9 timings for tiny call sites.
- **Scope timers** (`ScopeTimer.hpp`) with optional compile-time disable and lightweight label management.
- **Toy limit order book** (`src/Market`) with price/time priority helpers and basic types for microstructure simulations.

> Status: This library is under active development; new tools will be added over time.

## Build

Prerequisites: CMake ≥ 3.20 and a C++23-capable compiler.

```bash
mkdir -p build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build .
```

The static library target is `HFT_Toolset` (alias `HFT::Toolset`). Public headers live under `src/`.

## Usage

Latency benchmarking:

```cpp
#include "benchmark_p99.hpp"
#include <array>
#include <span>

int main() {
    std::array<std::int64_t, 100000> buf{};
    auto stats = hft_bench::benchmark_p99(
        [](int a, int b) noexcept { return a + b; },
        1, 2,
        std::span<std::int64_t>(buf),
        50'000,
        1'000
    );
    // stats.p99_ns / stats.p999_ns hold percentile latencies (ns)
}
```

Scope timing (compile-time toggle via `SCOPE_TIMER_DISABLED`):

```cpp
#include "ScopeTimer.hpp"
#include <chrono>

int main() {
    ScopeTimer<std::chrono::microseconds> t{/*raii=*/true};
    // ... work ...
    t.endAndLog();

    NScopeTimers::start("lookup");
    // ... work ...
    NScopeTimers::endAndLog("lookup");
}
```

Order book primitives:

```cpp
#include "Market/order_book.h"

using namespace MarketMicroStructure;

OrderBook book("FOO");
book.addOrder(BookOrder{NewOrder{1, 42, "FOO", Side::Buy, OrderType::Limit, TimeInForce::Day, 100, 10}, 0});
auto trades = book.matchIncoming(BookOrder{NewOrder{2, 43, "FOO", Side::Sell, OrderType::Limit, TimeInForce::IOC, 99, 5}, 0}, 1'000);
```

See `examples/p99_example.hpp` for a full benchmarking snippet.

## License

MIT — see `LICENSE` for details.
