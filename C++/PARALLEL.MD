# C++ Parallel Programming Guidelines

C++ provides multiple approaches to parallel programming including std::thread, std::async, and parallel algorithms.

## Threading Fundamentals

### Basic Thread Creation
```cpp
#include <thread>
#include <iostream>

void worker(int id) {
    std::cout << "Worker " << id << " running\n";
}

// Create and join threads
std::thread t1(worker, 1);
std::thread t2(worker, 2);
t1.join();
t2.join();
```

### RAII Thread Management
```cpp
class ThreadGuard {
    std::thread& t;
public:
    explicit ThreadGuard(std::thread& t_) : t(t_) {}
    ~ThreadGuard() {
        if (t.joinable()) t.join();
    }
};

// Usage
std::thread t(worker);
ThreadGuard guard(t);
```

## Modern C++ Parallel Features

### std::async and Futures
```cpp
#include <future>

// Fire and forget
auto future = std::async(std::launch::async, computeExpensiveResult);

// Get result (blocks until completion)
auto result = future.get();

// Launch policy control
auto future = std::async(std::launch::deferred | std::launch::async, task);
```

### Parallel Algorithms (C++17)
```cpp
#include <algorithm>
#include <execution>

// Parallel execution policies
std::sort(std::execution::par, vec.begin(), vec.end());
std::for_each(std::execution::par_unseq, vec.begin(), vec.end(), process);

// Reduce operations
auto sum = std::reduce(std::execution::par, vec.begin(), vec.end(), 0);
```

## Thread Safety Mechanisms

### Atomic Operations
```cpp
#include <atomic>

std::atomic<int> counter{0};

// Atomic operations
counter.fetch_add(1);
counter.load();
counter.store(newValue);

// Memory ordering
counter.store(value, std::memory_order_release);
auto val = counter.load(std::memory_order_acquire);
```

### Mutexes and Locks
```cpp
#include <mutex>

std::mutex mtx;
int shared_data = 0;

void safe_increment() {
    std::lock_guard<std::mutex> lock(mtx);
    ++shared_data;
}

// Unique locks for flexible locking
std::unique_lock<std::mutex> lock(mtx);
// ... operations
lock.unlock(); // Explicit unlock
```

### Lock-Free Data Structures
```cpp
#include <shared_mutex>

class ThreadSafeContainer {
    mutable std::shared_mutex mtx;
    std::vector<int> data;

public:
    // Multiple readers, exclusive writer
    int read(size_t index) const {
        std::shared_lock lock(mtx);
        return data.at(index);
    }

    void write(size_t index, int value) {
        std::unique_lock lock(mtx);
        data.at(index) = value;
    }
};
```

## Smart Concurrency Patterns

### Producer-Consumer
```cpp
#include <queue>
#include <condition_variable>

template<typename T>
class ThreadSafeQueue {
private:
    mutable std::mutex mtx;
    std::queue<T> data_queue;
    std::condition_variable data_cond;

public:
    void push(T item) {
        std::lock_guard<std::mutex> lock(mtx);
        data_queue.push(item);
        data_cond.notify_one();
    }

    T pop() {
        std::unique_lock<std::mutex> lock(mtx);
        data_cond.wait(lock, [this]{ return !data_queue.empty(); });
        T item = data_queue.front();
        data_queue.pop();
        return item;
    }
};
```

### Thread Pool
```cpp
class ThreadPool {
private:
    std::vector<std::thread> workers;
    std::queue<std::function<void()>> tasks;
    std::mutex queue_mutex;
    std::condition_variable condition;
    bool stop;

public:
    ThreadPool(size_t num_threads) : stop(false) {
        for (size_t i = 0; i < num_threads; ++i) {
            workers.emplace_back([this] {
                while (true) {
                    std::function<void()> task;
                    {
                        std::unique_lock<std::mutex> lock(queue_mutex);
                        condition.wait(lock, [this]{ return stop || !tasks.empty(); });
                        if (stop && tasks.empty()) return;
                        task = std::move(tasks.front());
                        tasks.pop();
                    }
                    task();
                }
            });
        }
    }

    template<class F>
    auto enqueue(F&& f) -> std::future<typename std::result_of<F()>::type> {
        using return_type = typename std::result_of<F()>::type;

        auto task = std::make_shared<std::packaged_task<return_type()>>(
            std::forward<F>(f)
        );

        std::future<return_type> res = task->get_future();
        {
            std::unique_lock<std::mutex> lock(queue_mutex);
            if (stop) throw std::runtime_error("enqueue on stopped ThreadPool");
            tasks.emplace([task](){ (*task)(); });
        }
        condition.notify_one();
        return res;
    }

    ~ThreadPool() {
        {
            std::unique_lock<std::mutex> lock(queue_mutex);
            stop = true;
        }
        condition.notify_all();
        for (std::thread &worker: workers) worker.join();
    }
};
```

## Best Practices

### Exception Safety
```cpp
void worker_thread() {
    try {
        // Do work
    } catch (...) {
        // Handle or store exception for main thread
        std::current_exception();
    }
}
```

### Resource Management
```cpp
// Use RAII for thread-local resources
thread_local std::unique_ptr<Resource> local_resource;

// Proper cleanup in destructors
~Class() {
    stop_flag = true;
    condition.notify_all();
    if (worker_thread.joinable()) {
        worker_thread.join();
    }
}
```

## Performance Considerations

- Minimize lock contention with fine-grained locking
- Use lock-free data structures when possible
- Consider false sharing in cache lines
- Profile before optimizing parallel code
- Be aware of thread creation overhead

## Testing Parallel Code

- Use sanitizers (ThreadSanitizer)
- Test with varying thread counts
- Verify race condition detection
- Stress test with high concurrency
- Test graceful shutdown scenarios