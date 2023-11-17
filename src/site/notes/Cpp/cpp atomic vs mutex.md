---
{"dg-publish":true,"permalink":"/cpp/cpp-atomic-vs-mutex/"}
---

`std::atomic` and `std::mutex` serve different purposes and are not necessarily interchangeable. They are both used for synchronization, but they are suitable for different scenarios.

**`std::atomic`** is primarily used for atomic operations on individual variables, ensuring that operations on those variables are thread-safe without the need for explicit locks. It's suitable for situations where you want to perform simple operations (like increment, decrement, compare-and-swap) on shared data without the overhead of locking entire sections of code. Here's a simple example:

```cpp
#include <iostream>
#include <thread>
#include <atomic>

std::atomic<int> counter(0);

void incrementCounter() {
    for (int i = 0; i < 10000; ++i) {
        counter.fetch_add(1, std::memory_order_relaxed);
    }
}

int main() {
    const int numThreads = 5;
    std::thread threads[numThreads];

    for (int i = 0; i < numThreads; ++i) {
        threads[i] = std::thread(incrementCounter);
    }

    for (int i = 0; i < numThreads; ++i) {
        threads[i].join();
    }

    std::cout << "Final counter value: " << counter.load(std::memory_order_relaxed) << std::endl;

    return 0;
}
```

In this example, `std::atomic` is used to safely increment a shared counter from multiple threads.

**`std::mutex` and `std::lock_guard`**, on the other hand, are used when you need to protect larger sections of code or multiple operations that involve multiple shared variables and require mutual exclusion. Mutexes provide exclusive access to a critical section of code, ensuring that only one thread can execute it at a time. Here's an example where `std::mutex` is more appropriate:

```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx;
int sharedData = 0;

void updateSharedData() {
    std::lock_guard<std::mutex> lock(mtx);
    sharedData++;
}

int main() {
    const int numThreads = 5;
    std::thread threads[numThreads];

    for (int i = 0; i < numThreads; ++i) {
        threads[i] = std::thread(updateSharedData);
    }

    for (int i = 0; i < numThreads; ++i) {
        threads[i].join();
    }

    std::cout << "Final sharedData value: " << sharedData << std::endl;

    return 0;
}
```

In this example, we protect access to the `sharedData` variable using a `std::mutex` and `std::lock_guard`. This ensures that no two threads can simultaneously update the shared data, preventing data corruption.

In summary, use `std::atomic` when you need to perform atomic operations on single variables, and use `std::mutex` and `std::lock_guard` when you need to protect larger sections of code or multiple operations involving multiple shared variables that require mutual exclusion. The choice depends on the specific synchronization requirements of your code.