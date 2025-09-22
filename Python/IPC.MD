# Python Inter-Process Communication - Essentials

## Key Principle

Use Python's rich ecosystem of libraries to implement the right IPC mechanism for your needs.

## Common IPC Methods

### HTTP/REST with requests
**Best for**: Web services, APIs, microservices

```python
import requests
import json

# GET request
def get_user(user_id):
    response = requests.get(f'https://api.example.com/users/{user_id}')
    response.raise_for_status()
    return response.json()

# POST request
def create_user(user_data):
    response = requests.post(
        'https://api.example.com/users',
        json=user_data,
        headers={'Content-Type': 'application/json'}
    )
    response.raise_for_status()
    return response.json()

# With session for multiple requests
session = requests.Session()
session.headers.update({'Authorization': 'Bearer token'})
```

### WebSockets with websockets
**Best for**: Real-time communication

```python
import asyncio
import websockets

# WebSocket client
async def websocket_client():
    uri = "ws://localhost:8765"
    async with websockets.connect(uri) as websocket:
        await websocket.send("Hello server!")
        response = await websocket.recv()
        print(f"Received: {response}")

# WebSocket server
async def websocket_handler(websocket, path):
    async for message in websocket:
        await websocket.send(f"Echo: {message}")

start_server = websockets.serve(websocket_handler, "localhost", 8765)
```

### Multiprocessing for Parallel Processing
**Best for**: CPU-intensive tasks, parallel execution

```python
from multiprocessing import Process, Queue, Pool
import os

# Using Process
def worker_function(name):
    print(f'Worker {name} PID: {os.getpid()}')

if __name__ == '__main__':
    processes = []
    for i in range(4):
        p = Process(target=worker_function, args=(i,))
        processes.append(p)
        p.start()

    for p in processes:
        p.join()

# Using Pool
def square_number(n):
    return n * n

if __name__ == '__main__':
    with Pool(4) as pool:
        results = pool.map(square_number, [1, 2, 3, 4, 5])
    print(results)
```

## Quick Reference

| Method | Use Case | Complexity |
|--------|----------|------------|
| requests | HTTP APIs | Low |
| websockets | Real-time | Medium |
| multiprocessing | Parallel tasks | Medium |
| ZeroMQ | Flexible messaging | Medium-High |
| gRPC | High-performance | Medium |

## Implementation Guidelines

### For Web Services
1. Use requests or aiohttp for HTTP clients
2. Handle timeouts and retries appropriately
3. Use sessions for connection pooling
4. Validate and sanitize API responses

### For Real-time Communication
1. Handle connection lifecycle events
2. Implement proper error handling
3. Use asyncio for concurrent connections
4. Consider message queuing for reliability

### For Parallel Processing
1. Use multiprocessing for CPU-bound tasks
2. Use threading for I/O-bound tasks
3. Prefer Pool for map/reduce operations
4. Use Queue for inter-process communication

## Modern Python Libraries

### Async HTTP with aiohttp
```python
import aiohttp
import asyncio

async def fetch(session, url):
    async with session.get(url) as response:
        return await response.text()

async def main():
    async with aiohttp.ClientSession() as session:
        html = await fetch(session, 'http://python.org')
        print(html)

# Run the async function
asyncio.run(main())
```

### Message Queues with Redis
```python
import redis

# Connect to Redis
r = redis.Redis(host='localhost', port=6379, db=0)

# Publish message
r.publish('channel', 'Hello, subscribers!')

# Subscribe to channel
pubsub = r.pubsub()
pubsub.subscribe('channel')

for message in pubsub.listen():
    if message['type'] == 'message':
        print(f"Received: {message['data']}")
```

## Best Practices

1. **Error Handling**: Always handle network errors and timeouts
2. **Resource Management**: Use context managers for connections
3. **Security**: Validate inputs and use secure connections
4. **Performance**: Use connection pooling and async when appropriate