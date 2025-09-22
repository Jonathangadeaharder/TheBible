# C# Design Patterns - Essentials

## Key Principle

Focus on understanding patterns conceptually rather than memorizing complex implementations.

## Essential Patterns

### Strategy Pattern
**When to use**: Switch between different algorithms at runtime

```csharp
public interface IPaymentStrategy
{
    bool ProcessPayment(decimal amount);
}

public class CreditCardPayment : IPaymentStrategy
{
    public bool ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing credit card payment of ${amount}");
        return true;
    }
}

public class PaymentProcessor
{
    private IPaymentStrategy _strategy;

    public void SetStrategy(IPaymentStrategy strategy)
    {
        _strategy = strategy;
    }

    public bool ExecutePayment(decimal amount)
    {
        return _strategy.ProcessPayment(amount);
    }
}
```

### Observer Pattern
**When to use**: Notify multiple objects of state changes

```csharp
public interface IObserver
{
    void Update(string message);
}

public class Subject
{
    private List<IObserver> _observers = new List<IObserver>();

    public void Attach(IObserver observer)
    {
        _observers.Add(observer);
    }

    public void Notify(string message)
    {
        foreach (var observer in _observers)
        {
            observer.Update(message);
        }
    }
}
```

## Quick Reference

| Pattern | Problem | When to Use |
|---------|---------|-------------|
| Strategy | Switch algorithms | Multiple approaches for same task |
| Observer | Notifications | One-to-many dependencies |
| Factory | Object creation | Complex instantiation logic |
| Singleton | Single instance | Shared resource (use sparingly) |

## Learning Approach

1. Understand the problem each pattern solves
2. Recognize common scenarios where patterns apply
3. Practice with simple examples
4. Apply selectively - don't force patterns where not needed