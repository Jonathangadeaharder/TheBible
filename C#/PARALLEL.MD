# C# Parallel Programming Guidelines

C# provides rich parallel programming capabilities through Task Parallel Library (TPL), async/await, and PLINQ.

## Task-Based Parallelism

### Task Creation
```csharp
// Prefer Task.Run for CPU-bound work
var task = Task.Run(() => ComputeExpensiveResult());

// Use async/await for I/O-bound operations
public async Task<string> FetchDataAsync()
{
    using var client = new HttpClient();
    return await client.GetStringAsync("https://api.example.com/data");
}
```

### Task Coordination
```csharp
// Use WhenAll for independent tasks
var tasks = urls.Select(url => DownloadAsync(url)).ToArray();
var results = await Task.WhenAll(tasks);

// Use WhenAny for racing operations
var winner = await Task.WhenAny(task1, task2, task3);
```

## Data Parallelism with PLINQ

```csharp
// Parallel LINQ operations
var results = data
    .AsParallel()
    .Where(x => IsPrime(x))
    .Select(x => Transform(x))
    .ToArray();

// Specify degree of parallelism
var results = data
    .AsParallel()
    .WithDegreeOfParallelism(Environment.ProcessorCount)
    .Select(ProcessItem)
    .ToArray();
```

## Thread Safety

### Immutable Objects
```csharp
public record User(string Name, int Age); // Thread-safe by default

// Use readonly collections
public static ReadOnlyCollection<Item> Items { get; } =
    new List<Item>(initialItems).AsReadOnly();
```

### Synchronization Primitives
```csharp
// Use Concurrent collections
private readonly ConcurrentDictionary<string, User> _users =
    new ConcurrentDictionary<string, User>();

// Lock-free operations with Interlocked
private int _counter = 0;
public void Increment() => Interlocked.Increment(ref _counter);

// ReaderWriterLockSlim for read-heavy scenarios
private readonly ReaderWriterLockSlim _lock = new ReaderWriterLockSlim();
```

## Async/Await Best Practices

### Avoid Async Void
```csharp
// Good - returns Task
public async Task ProcessAsync() { /* ... */ }

// Bad - no way to await or catch exceptions
public async void ProcessAsync() { /* ... */ }
```

### ConfigureAwait Usage
```csharp
// Library code - avoid context capture
await task.ConfigureAwait(false);

// UI code - preserve context for UI updates
await task.ConfigureAwait(true); // or just await task
```

## Common Pitfalls

### Blocking on Async Code
```csharp
// Bad - can cause deadlocks
var result = asyncMethod().Result;

// Good - stay async all the way
var result = await asyncMethod();
```

### Capturing ExecutionContext
```csharp
// Be careful with ambient context
using (ExecutionContext.SuppressFlow())
{
    // Operations that shouldn't inherit context
}
```

## Performance Guidelines

- Use `ValueTask` for synchronous fast-paths
- Prefer `Task.WhenAll` over sequential awaits
- Use `CancellationToken` for cooperative cancellation
- Profile to identify actual bottlenecks
- Consider `IAsyncEnumerable` for streaming data

## Testing Parallel Code

- Use `Task.Delay` instead of `Thread.Sleep`
- Test cancellation scenarios
- Verify thread safety with concurrent access
- Stress test under various loads