---
{"dg-publish":true,"permalink":"/cpp/cpp-atomic/"}
---

In C++, the `atomic` keyword is used to declare objects that can be accessed and modified by multiple threads in a thread-safe manner. Atomic operations ensure that read and write operations on these objects are indivisible, meaning that they are not interrupted by other threads, preventing race conditions and data races.

The C++ Standard Library provides a set of atomic types and functions for atomic operations on these types. The primary atomic types are:

1. **std::atomic\<T\>**: This template class is used to create atomic objects of type `T`. It supports atomic read and write operations for various data types, such as integers (`int`, `long`, etc.), pointers, and user-defined types when they meet certain requirements.

2. **std::atomic_flag**: This type is specifically designed for atomic flag variables, which can be used for simple synchronization tasks like setting and clearing flags in a thread-safe manner.

Here's an example of using `std::atomic<int>` for a simple counter:

```cpp
#include <atomic>
#include <iostream>
#include <string>
#include <thread>
#include <unordered_map>

std::atomic<int> counter(0);

std::unordered_map<std::thread::id, int> m;

auto print = [](const std::unordered_map<std::thread::id, int> &m) {
  for (auto &kv : m) {
    std::cout << "Thread ID: " << kv.first << ", Hit Count: " << kv.second
              << '\n';
  }
};

void incrementCounter() {
  for (int i = 0; i < 1000000; ++i) {
    counter.fetch_add(1, std::memory_order_relaxed); // Atomic increment
    m[std::this_thread::get_id()]++;
  }
  std::cout << "Thread ID: " << std::this_thread::get_id() << " finished."
            << std::endl;
}

int main() {
  std::thread t1(incrementCounter);
  std::thread t2(incrementCounter);

  std::cout << "Main thread ID: " << std::this_thread::get_id() << std::endl;

  t1.join();
  t2.join();

  std::cout << "Counter: " << counter.load(std::memory_order_relaxed)
            << std::endl;

  print(m);

  return 0;
}

```

In this example:

- We use `std::atomic<int>` to declare an atomic integer `counter`.
- Two threads (`t1` and `t2`) increment the `counter` in parallel using the `fetch_add` operation, which is an atomic increment operation.
- We ensure atomicity by using appropriate memory orderings (`std::memory_order_relaxed` in this case).

The `atomic` operations ensure that the increments are executed atomically and that there are no data races, making it safe for concurrent access by multiple threads.

Please note that proper choice of memory orderings (`std::memory_order`) is essential when using atomic operations, as it determines the visibility and ordering of memory operations between threads. The choice of memory ordering depends on the specific synchronization requirements of your program.

## Understanding Memory order relaxed
The choice of the memory order when using atomic operations with `std::atomic` is crucial for ensuring proper synchronization between threads. If you didn't use `std::memory_order_relaxed` or chose a different memory order, the behavior and performance of your program could change significantly. Here's what might happen with different memory orderings:

1. **`std::memory_order_relaxed`**: This memory order provides the least synchronization guarantees. It ensures atomicity of the operation but doesn't impose any ordering constraints on other memory operations or threads. In the example I provided earlier, this memory order should work fine because the final value of the `counter` is the only result we care about, and the order in which increments happen doesn't matter.

2. **Other Memory Orderings (e.g., `std::memory_order_seq_cst`)**: When using stronger memory orderings, such as `std::memory_order_seq_cst` (sequentially consistent), you introduce stricter synchronization constraints. With `std::memory_order_seq_cst`, the atomic operations are not only atomic but also establish a total order among them, meaning that all threads will observe the operations in the same order. This can potentially lead to more synchronization overhead and slower performance, especially in scenarios where strict ordering is not necessary.

If you use a memory ordering other than `std::memory_order_relaxed`, the behavior of your program may change in the following ways:

- **Visibility and Ordering**: Stronger memory orderings provide guarantees about the visibility and ordering of memory operations. This can be important in scenarios where you need to ensure that certain operations happen before or after others across threads.

- **Synchronization Overhead**: Stronger memory orderings often incur more synchronization overhead, potentially leading to slower performance, especially in scenarios where the strict ordering guarantees are not necessary.

- **Correctness**: Choosing the appropriate memory ordering is essential to ensure the correctness of your program. Using the wrong memory ordering can result in subtle bugs, such as data races or incorrect program behavior.

In summary, the choice of memory ordering in atomic operations should be made carefully based on the specific synchronization requirements of your program. In many cases, `std::memory_order_relaxed` is sufficient and provides better performance, but in scenarios where stronger synchronization guarantees are needed, you should use the appropriate memory order, such as `std::memory_order_seq_cst`.

## Understanding spinlock mutex
A spinlock mutex, often referred to simply as a "spinlock," is a type of synchronization primitive used in multithreaded programming to protect shared resources from concurrent access by multiple threads. Unlike traditional mutexes, such as the `std::mutex` in C++, which put threads to sleep when they attempt to lock a mutex that is already locked by another thread, a spinlock repeatedly checks the mutex in a tight loop (spinning) until it becomes available.

Here's how a spinlock typically works:

1. When a thread wants to access a shared resource protected by a spinlock, it attempts to acquire the spinlock.

2. If the spinlock is already held by another thread, the requesting thread enters a busy-wait loop and repeatedly checks the state of the spinlock until it becomes available.

3. When the spinlock becomes available (i.e., the owning thread releases it), the requesting thread acquires the spinlock and proceeds to access the shared resource.

4. Once the requesting thread is done with the shared resource, it releases the spinlock, allowing other threads to acquire it.

Spinlocks are often used in situations where the expected wait time for the lock to become available is very short. They can be more efficient than traditional mutexes in such cases because they avoid the overhead of putting threads to sleep and waking them up, which can be relatively expensive in terms of context switching.

However, spinlocks have some drawbacks and considerations:

- **Busy-Waiting**: Spinlocks can result in busy-waiting, where a thread consumes CPU resources while repeatedly checking the lock. In situations with many competing threads or long lock-holding times, this can be inefficient and wasteful.

- **Deadlocks**: Spinlocks can lead to deadlocks if not used carefully. For example, if a thread that holds a spinlock tries to acquire the same spinlock again, it will result in a deadlock.

- **Priority Inversion**: Spinlocks can also lead to priority inversion, where a high-priority thread waits for a low-priority thread to release a spinlock. This can impact the real-time behavior of a system.

In C++, you can implement a spinlock using atomic variables, such as `std::atomic_flag`, with appropriate atomic operations like `test_and_set` or `exchange`. However, it's essential to use spinlocks judiciously, considering factors like expected contention, the duration of critical sections, and the characteristics of your target platform. In many cases, other synchronization primitives like mutexes or condition variables may be more appropriate.

