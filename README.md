---

![logo](docs/copper_logo_small.png)

---

**Copper** is a C++ library of a powerful queue object for communication between threads. It is based on Go's channels and follows the quote:

>Don't communicate by sharing memory; share memory by communicating.

See [**docs/motivation.adoc**](docs/motivation.adoc) for a high-level description of what Copper is
capable of and how it stands out from previous C / C++ implementations of queues and Go-like channels.

Copper has...
* only a single header to include.
* no deadlocks; no race conditions; no undefined behavior; no polling.
* support for multiple producers and multiple consumers.
* an API based on that of `std` to avoid style clashes.

---

## Quick Example
```c++
#include <future>
#include <iostream>
#include "copper.h"

copper::buffered_channel<int> channel_1;
copper::buffered_channel<int> channel_2;

void producer_1() {
    for (auto i = 0; i < 10; ++i) {
        (void) channel_1.push(i);
    }
    channel_1.close();
}

void producer_2() {
    for (auto i = 0; i < 10; ++i) {
        (void) channel_2.push(i);
    }
    channel_2.close();
}

void consumer() {
    copper::loop_select(
        channel_1 >> [](int x) { std::cout << "Message from producer 1: " << x << std::endl; },
        channel_2 >> [](int x) { std::cout << "Message from producer 2: " << x << std::endl; }
    );
}

int main() {
    const auto p1 = std::async(producer_1);
    const auto p2 = std::async(producer_2);
    const auto c = std::async(consumer);
    return 0;
}
```

---

##  More Information

### API Reference

See [docs/reference.adoc](docs/reference.adoc) for a detailed reference of the public code interface.

### Efficiency & Benchmarks

See [docs/benchmark.adoc](docs/benchmark.adoc) for technical information about efficiency
and some benchmarks.

---

## Conan, CPM, vcpkg

Not yet, but I'll see what I can do.