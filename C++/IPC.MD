# C++ Inter-Process Communication - Essentials

## Key Principle

Choose the right IPC mechanism based on performance, complexity, and platform requirements.

## Common IPC Methods

### TCP Sockets
**Best for**: Network communication, cross-platform IPC

```cpp
#include <sys/socket.h>
#include <netinet/in.h>
#include <unistd.h>
#include <iostream>

// Server example
int createServer(int port) {
    int server_fd = socket(AF_INET, SOCK_STREAM, 0);

    struct sockaddr_in address;
    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(port);

    bind(server_fd, (struct sockaddr*)&address, sizeof(address));
    listen(server_fd, 3);

    return server_fd;
}

// Client example
int connectToServer(const char* ip, int port) {
    int sock = socket(AF_INET, SOCK_STREAM, 0);

    struct sockaddr_in serv_addr;
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_port = htons(port);
    inet_pton(AF_INET, ip, &serv_addr.sin_addr);

    connect(sock, (struct sockaddr*)&serv_addr, sizeof(serv_addr));

    return sock;
}
```

### Shared Memory
**Best for**: High-performance local IPC

```cpp
#include <sys/shm.h>
#include <sys/ipc.h>

// Create shared memory segment
int createSharedMemory(size_t size) {
    key_t key = ftok("shmfile", 65);
    int shmid = shmget(key, size, 0666|IPC_CREAT);
    return shmid;
}

// Attach to shared memory
void* attachSharedMemory(int shmid) {
    void* shared_memory = shmat(shmid, nullptr, 0);
    return shared_memory;
}
```

### Message Queues
**Best for**: Asynchronous communication

```cpp
#include <sys/msg.h>
#include <cstring>

struct Message {
    long msg_type;
    char data[256];
};

// Send message
void sendMessage(int msgid, const char* data) {
    Message msg;
    msg.msg_type = 1;
    strcpy(msg.data, data);
    msgsnd(msgid, &msg, sizeof(msg.data), 0);
}

// Receive message
void receiveMessage(int msgid, char* buffer) {
    Message msg;
    msgrcv(msgid, &msg, sizeof(msg.data), 1, 0);
    strcpy(buffer, msg.data);
}
```

## Quick Reference

| Method | Use Case | Platform | Complexity |
|--------|----------|----------|------------|
| TCP Sockets | Network IPC | Cross-platform | Medium |
| Shared Memory | High-performance | Unix/Linux | Medium |
| Message Queues | Async communication | Unix/Linux | Medium |
| Named Pipes | Local IPC | Windows | Low |
| REST APIs | Web services | Cross-platform | Low-Medium |

## Implementation Guidelines

### For Network Communication
1. Handle connection errors gracefully
2. Implement proper buffering
3. Use non-blocking I/O for performance
4. Close connections properly

### For Local IPC
1. Use appropriate synchronization primitives
2. Handle cleanup in signal handlers
3. Validate data integrity
4. Consider security implications

### For Cross-Platform Solutions
1. Use portable libraries like Boost.Asio
2. Abstract platform-specific code
3. Test on all target platforms
4. Handle endianness differences

## Modern C++ Libraries

### REST Client with libcurl
```cpp
#include <curl/curl.h>

size_t WriteCallback(void* contents, size_t size, size_t nmemb, std::string* response) {
    size_t realsize = size * nmemb;
    response->append((char*)contents, realsize);
    return realsize;
}

std::string httpGet(const std::string& url) {
    CURL* curl = curl_easy_init();
    std::string response;

    if (curl) {
        curl_easy_setopt(curl, CURLOPT_URL, url.c_str());
        curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, WriteCallback);
        curl_easy_setopt(curl, CURLOPT_WRITEDATA, &response);
        curl_easy_perform(curl);
        curl_easy_cleanup(curl);
    }

    return response;
}
```

## Best Practices

1. **Error Handling**: Always check return values
2. **Resource Management**: Use RAII for sockets and memory
3. **Security**: Validate all input data
4. **Performance**: Buffer appropriately and avoid unnecessary copies