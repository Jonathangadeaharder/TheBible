# C# Inter-Process Communication - Essentials

## Key Principle

Choose the simplest communication method that meets your needs.

## Common IPC Methods

### HTTP/REST APIs
**Best for**: Web services, microservices, external integrations

```csharp
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/[controller]")]
public class UserController : ControllerBase
{
    [HttpGet("{id}")]
    public ActionResult<User> GetUser(int id)
    {
        // Get user logic
        return Ok(user);
    }

    [HttpPost]
    public ActionResult<User> CreateUser(CreateUserRequest request)
    {
        // Create user logic
        return CreatedAtAction(nameof(GetUser), new { id = user.Id }, user);
    }
}
```

### SignalR for Real-time Communication
**Best for**: Real-time notifications, chat applications

```csharp
using Microsoft.AspNetCore.SignalR;

public class ChatHub : Hub
{
    public async Task SendMessage(string user, string message)
    {
        await Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}

// In Startup.cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR();
}
```

### Named Pipes
**Best for**: Windows inter-process communication

```csharp
using System.IO.Pipes;

// Server
using var server = new NamedPipeServerStream("mypipe");
server.WaitForConnection();
using var writer = new StreamWriter(server);
writer.WriteLine("Hello from server");

// Client
using var client = new NamedPipeClientStream(".", "mypipe", PipeDirection.InOut);
client.Connect();
using var reader = new StreamReader(client);
string response = reader.ReadLine();
```

## Quick Reference

| Method | Use Case | Complexity |
|--------|----------|------------|
| HTTP/REST | Web services | Low |
| SignalR | Real-time | Medium |
| Named Pipes | Windows IPC | Low |
| Message Queues | Async processing | Medium-High |
| gRPC | High-performance | Medium |

## Implementation Guidelines

### For Web Services
1. Use ASP.NET Core Web API
2. Implement proper error handling
3. Use middleware for cross-cutting concerns
4. Secure endpoints with authentication

### For Real-time Features
1. Use SignalR for .NET applications
2. Handle connection lifecycle events
3. Implement reconnection logic
4. Scale with Redis backplane if needed

### For Internal Communication
1. Prefer direct method calls within same process
2. Use interfaces for loose coupling
3. Apply dependency injection
4. Consider performance implications

## Security Considerations

1. **Authentication**: Validate all requests
2. **Authorization**: Check permissions
3. **Encryption**: Use HTTPS/TLS
4. **Input Validation**: Sanitize all inputs