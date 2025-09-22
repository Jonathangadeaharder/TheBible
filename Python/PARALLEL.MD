# Python Parallel Processing Guidelines

This document outlines parallel processing techniques and best practices for Python development to improve performance and handle concurrent operations effectively.

## Concurrency vs Parallelism

### Threading (Concurrency)
- Best for I/O-bound tasks
- Limited by Global Interpreter Lock (GIL)
- Lightweight context switching

### Multiprocessing (Parallelism)
- Best for CPU-bound tasks
- True parallel execution across cores
- Higher memory overhead

### Async/Await (Concurrency)
- Best for I/O-bound tasks with many concurrent operations
- Single-threaded cooperative multitasking
- Low memory overhead

## Threading for I/O-Bound Tasks

### Basic Threading
```python
import threading
import time
import requests

def fetch_url(url):
    """Simulate I/O-bound task."""
    response = requests.get(url)
    print(f"Fetched {url}: {len(response.content)} bytes")

# Sequential execution
start_time = time.time()
urls = ["http://httpbin.org/delay/1"] * 5
for url in urls:
    fetch_url(url)
print(f"Sequential: {time.time() - start_time:.2f}s")

# Threaded execution
start_time = time.time()
threads = []
for url in urls:
    thread = threading.Thread(target=fetch_url, args=(url,))
    threads.append(thread)
    thread.start()

for thread in threads:
    thread.join()
print(f"Threaded: {time.time() - start_time:.2f}s")
```

### ThreadPoolExecutor
```python
from concurrent.futures import ThreadPoolExecutor, as_completed
import requests

def fetch_url(url):
    response = requests.get(url)
    return url, len(response.content)

urls = ["http://httpbin.org/delay/1"] * 10

# Using ThreadPoolExecutor
with ThreadPoolExecutor(max_workers=5) as executor:
    # Submit all tasks
    future_to_url = {executor.submit(fetch_url, url): url for url in urls}

    # Process completed tasks
    for future in as_completed(future_to_url):
        url = future_to_url[future]
        try:
            url, size = future.result()
            print(f"{url}: {size} bytes")
        except Exception as exc:
            print(f"{url} generated an exception: {exc}")
```

### Thread Safety
```python
import threading
import time

# Thread-safe counter
class SafeCounter:
    def __init__(self):
        self._value = 0
        self._lock = threading.Lock()

    def increment(self):
        with self._lock:
            self._value += 1

    def get_value(self):
        with self._lock:
            return self._value

# Thread-local storage
thread_local_data = threading.local()

def process_data(data):
    thread_local_data.value = data
    # Each thread has its own copy of thread_local_data.value
    print(f"Thread {threading.current_thread().name}: {thread_local_data.value}")
```

## Multiprocessing for CPU-Bound Tasks

### Basic Multiprocessing
```python
import multiprocessing
import time

def cpu_intensive_task(n):
    """Simulate CPU-bound task."""
    result = sum(i * i for i in range(n))
    return result

# Sequential execution
start_time = time.time()
results = [cpu_intensive_task(1000000) for _ in range(4)]
print(f"Sequential: {time.time() - start_time:.2f}s")

# Parallel execution
if __name__ == "__main__":
    start_time = time.time()
    with multiprocessing.Pool(processes=4) as pool:
        results = pool.map(cpu_intensive_task, [1000000] * 4)
    print(f"Multiprocessing: {time.time() - start_time:.2f}s")
```

### ProcessPoolExecutor
```python
from concurrent.futures import ProcessPoolExecutor
import time

def factorial(n):
    if n <= 1:
        return 1
    return n * factorial(n - 1)

numbers = [1000, 1001, 1002, 1003, 1004]

if __name__ == "__main__":
    # Sequential
    start_time = time.time()
    seq_results = [factorial(n) for n in numbers]
    print(f"Sequential: {time.time() - start_time:.2f}s")

    # Parallel
    start_time = time.time()
    with ProcessPoolExecutor(max_workers=4) as executor:
        par_results = list(executor.map(factorial, numbers))
    print(f"Parallel: {time.time() - start_time:.2f}s")
```

### Inter-Process Communication
```python
import multiprocessing
import time

def producer(queue):
    """Producer process."""
    for i in range(5):
        item = f"item_{i}"
        queue.put(item)
        print(f"Produced: {item}")
        time.sleep(0.5)
    queue.put(None)  # Sentinel value

def consumer(queue):
    """Consumer process."""
    while True:
        item = queue.get()
        if item is None:
            break
        print(f"Consumed: {item}")
        time.sleep(0.3)

if __name__ == "__main__":
    # Create shared queue
    queue = multiprocessing.Queue()

    # Create processes
    prod_process = multiprocessing.Process(target=producer, args=(queue,))
    cons_process = multiprocessing.Process(target=consumer, args=(queue,))

    # Start processes
    prod_process.start()
    cons_process.start()

    # Wait for completion
    prod_process.join()
    cons_process.join()
```

## Async/Await for I/O-Bound Tasks

### Basic Async Operations
```python
import asyncio
import aiohttp
import time

async def fetch_url(session, url):
    """Async fetch URL."""
    async with session.get(url) as response:
        content = await response.text()
        return url, len(content)

async def main():
    urls = ["http://httpbin.org/delay/1"] * 10

    # Sequential async (still sequential!)
    start_time = time.time()
    async with aiohttp.ClientSession() as session:
        for url in urls:
            await fetch_url(session, url)
    print(f"Sequential async: {time.time() - start_time:.2f}s")

    # Concurrent async
    start_time = time.time()
    async with aiohttp.ClientSession() as session:
        tasks = [fetch_url(session, url) for url in urls]
        results = await asyncio.gather(*tasks)
    print(f"Concurrent async: {time.time() - start_time:.2f}s")

# Run async code
asyncio.run(main())
```

### Async Producer-Consumer
```python
import asyncio
import random

async def producer(queue):
    """Async producer."""
    for i in range(5):
        item = f"item_{i}"
        await queue.put(item)
        print(f"Produced: {item}")
        await asyncio.sleep(random.uniform(0.1, 0.5))

    # Send sentinel values
    for _ in range(3):  # Number of consumers
        await queue.put(None)

async def consumer(queue, name):
    """Async consumer."""
    while True:
        item = await queue.get()
        if item is None:
            break
        print(f"Consumer {name} consumed: {item}")
        await asyncio.sleep(random.uniform(0.1, 0.3))
        queue.task_done()

async def main():
    queue = asyncio.Queue(maxsize=10)

    # Create tasks
    producers = [asyncio.create_task(producer(queue))]
    consumers = [asyncio.create_task(consumer(queue, f"C{i}")) for i in range(3)]

    # Wait for completion
    await asyncio.gather(*producers)
    await asyncio.gather(*consumers)

asyncio.run(main())
```

## Choosing the Right Approach

### Decision Matrix
| Task Type | Best Approach | Reason |
|-----------|---------------|---------|
| I/O-bound, few concurrent operations | Threading | Simple, lightweight |
| I/O-bound, many concurrent operations | Async/Await | Efficient, scalable |
| CPU-bound | Multiprocessing | True parallelism |
| Mixed I/O and CPU | Combination | Use appropriate tool for each part |

### Performance Comparison Example
```python
import time
import threading
import multiprocessing
import asyncio
import aiohttp

# I/O-bound task simulation
def io_bound_task_sync(delay):
    time.sleep(delay)
    return f"Completed after {delay}s"

async def io_bound_task_async(delay):
    await asyncio.sleep(delay)
    return f"Completed after {delay}s"

# CPU-bound task simulation
def cpu_bound_task(n):
    return sum(i * i for i in range(n))

# Benchmark different approaches
def benchmark_io_sync(tasks):
    start = time.time()
    results = [io_bound_task_sync(delay) for delay in tasks]
    return time.time() - start

def benchmark_io_threading(tasks):
    start = time.time()
    with threading.ThreadPoolExecutor() as executor:
        results = list(executor.map(io_bound_task_sync, tasks))
    return time.time() - start

async def benchmark_io_async(tasks):
    start = time.time()
    async with aiohttp.ClientSession() as session:
        coroutines = [io_bound_task_async(delay) for delay in tasks]
        results = await asyncio.gather(*coroutines)
    return time.time() - start

def benchmark_cpu_multiprocessing(tasks):
    start = time.time()
    with multiprocessing.Pool() as pool:
        results = pool.map(cpu_bound_task, tasks)
    return time.time() - start
```

## Best Practices

### 1. Resource Management
```python
# Proper resource cleanup
from concurrent.futures import ThreadPoolExecutor

def process_with_cleanup():
    executor = ThreadPoolExecutor(max_workers=4)
    try:
        # Do work
        pass
    finally:
        executor.shutdown(wait=True)

# Better: Use context manager
with ThreadPoolExecutor(max_workers=4) as executor:
    # Do work
    pass
# Executor automatically shut down
```

### 2. Error Handling
```python
from concurrent.futures import ThreadPoolExecutor, as_completed

def risky_task(task_id):
    if task_id == 3:
        raise ValueError(f"Task {task_id} failed")
    return f"Task {task_id} completed"

with ThreadPoolExecutor(max_workers=3) as executor:
    futures = {executor.submit(risky_task, i): i for i in range(5)}

    for future in as_completed(futures):
        task_id = futures[future]
        try:
            result = future.result()
            print(result)
        except Exception as exc:
            print(f"Task {task_id} generated an exception: {exc}")
```

### 3. Performance Tuning
```python
import multiprocessing
import os

# Optimal worker count
def get_optimal_workers():
    # For I/O-bound: more workers than CPUs
    # For CPU-bound: equal to number of CPUs
    cpu_count = os.cpu_count()
    return {
        'io_bound': cpu_count * 2,
        'cpu_bound': cpu_count
    }

# Monitor performance
import time
from functools import wraps

def timing_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.2f}s")
        return result
    return wrapper
```

## Common Pitfalls and Solutions

### 1. Global Interpreter Lock (GIL)
```python
# Problem: Threading doesn't help CPU-bound tasks
import threading
import time

def cpu_task():
    # This won't benefit from threading due to GIL
    result = sum(i * i for i in range(1000000))
    return result

# Solution: Use multiprocessing
import multiprocessing

if __name__ == "__main__":
    with multiprocessing.Pool() as pool:
        results = pool.map(cpu_task, range(4))
```

### 2. Race Conditions
```python
import threading

# Problem: Race condition
counter = 0

def increment():
    global counter
    for _ in range(100000):
        counter += 1  # Not atomic!

# Solution: Use locks
counter = 0
lock = threading.Lock()

def safe_increment():
    global counter
    for _ in range(100000):
        with lock:
            counter += 1
```

### 3. Deadlocks
```python
import threading

lock1 = threading.Lock()
lock2 = threading.Lock()

# Problem: Potential deadlock
def task1():
    with lock1:
        with lock2:  # May deadlock if task2 holds lock2
            pass

def task2():
    with lock2:
        with lock1:  # May deadlock if task1 holds lock1
            pass

# Solution: Consistent lock ordering
def task1_safe():
    with lock1:
        with lock2:
            pass

def task2_safe():
    with lock1:  # Same order
        with lock2:
            pass
```

## Monitoring and Debugging

### 1. Profiling Parallel Code
```python
import cProfile
import pstats
from concurrent.futures import ProcessPoolExecutor

def profile_parallel_function():
    profiler = cProfile.Profile()
    profiler.enable()

    # Your parallel code here
    with ProcessPoolExecutor() as executor:
        results = list(executor.map(some_function, data))

    profiler.disable()
    stats = pstats.Stats(profiler)
    stats.sort_stats('cumulative')
    stats.print_stats()
```

### 2. Monitoring Resource Usage
```python
import psutil
import threading
import time

def monitor_resources():
    process = psutil.Process()
    while True:
        cpu_percent = process.cpu_percent()
        memory_info = process.memory_info()
        print(f"CPU: {cpu_percent}%, Memory: {memory_info.rss / 1024 / 1024:.2f}MB")
        time.sleep(1)

# Start monitoring in background
monitor_thread = threading.Thread(target=monitor_resources, daemon=True)
monitor_thread.start()
```

## Quick Reference

| Approach | Use Case | Example |
|----------|----------|---------|
| Threading | I/O-bound, few operations | `ThreadPoolExecutor` |
| Multiprocessing | CPU-bound | `ProcessPoolExecutor` |
| Async/Await | I/O-bound, many operations | `asyncio.gather()` |
| Concurrent.futures | Simple parallelism | `submit()` and `map()` |

| Best Practice | Implementation |
|---------------|----------------|
| Resource Cleanup | Use context managers |
| Error Handling | Wrap futures in try/except |
| Performance Tuning | Profile and measure |
| Thread Safety | Use locks for shared data |
| Process Safety | Use multiprocessing primitives |