## 1. Introduction
- Sockets are another form of IPC
- We’ve seen pipes and signals
	- We also talked about shared memory
- Previous IPC assume that the processes are on the same physical machine
- Sockets enable IPC between physical machines, typically over the network
## 2. How to Use Sockets
- Servers follow 4 steps to use sockets
- These are all system calls, and have the usual C wrappers:
	1. `socket`
		- Create the socket
	2. `bind`
		- Attach the socket to some location (a file, IP:port, etc.)
	3. `listen`
		- Indicate you’re accepting connections, and set the queue limit
	4. `accept`
		- Return the next incoming connection for you to handle
- Clients follow 2 steps to use sockets
- Clients have a much easier time, they use one socket per connection:
	1. `socket`
		- Create the socket  
	2. `connect`
		- Connect to some location, the socket can now send/receive data
## 3. Socket API
- The `socket` system call sets the protocol and type of socket
```c
int socket(int domain, int type, int protocol);
```

- `domain` is the general protocol
	- `AF_UNIX`: for local communication (on the same physical machine)
	- `AF_INET`: for IPv4 protocol using your network interface
	- `AF_INET6`: for IPv6 protocol using your network interface
- `type` is usually one of two options:
	- stream sockets
	- datagram sockets
### 3.1 `type`
#### 3.1.1 Stream Sockets 
- Stream Sockets use TCP
- All data sent by a client appears in the same order on the server
- Forms a persistent connection between client and server
- Reliable, but may be slow
#### 3.1.2 Datagram Sockets 
- Datagram Sockets use UDP
- Sends messages between the client and server
- No persistent connection between client and server
- Fast but messages may be reordered, or dropped
### 3.2 `bind`
```c
int bind(int socket, const struct sockaddr *address, socklen_t address_len);
```
- The `bind` system call sets a socket to an address
- `socket` is the file descriptor returned from the socket system call
- There are different `sockaddr` structures for different protocols:
	- `sockaddr_un`: for local communication (just a path)
	- `sockaddr_in`: for IPv4 address (e.g. 8.8.8.8)
	- `sockaddr_in6`: for IPv6 address (e.g. 2001:4860:4860::8888)
### 3.3 `listen`
```c
int listen(int socket, int backlog);
```
- The `listen` system call sets queue limits for incoming connections
- `socket`: the file descriptor returned from `socket`
- `backlog`: limit of outstanding (not accepted) connections
- The kernel manages this queue and blocks new connections when full
- A value of 0 uses the default kernel queue size
### 3.4 `accept`
```c
int accept(int socket, struct sockaddr *restrict
```
- The `accept` system call blocks until there's a connection
- `socket`: file descriptor from the `socket` system call
- `address` and `address_len`: locations to write the connecting address (optional)
	- Acts as an optional return value, set both to `NULL` to ignore
- Returns a new file descriptor, which we can `read` or `write` to as usual
### 3.5 `connect`
```c
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```
- The `connect` system call allows a client to connect an address
- `sockfd`: file descriptor from the `socket` system call
	- The client would need to be using the same protocol and type as the server
- `addr` and `addrlen`: the address to connect to, exactly like `bind`
- On success, the socket can be used like a normal file descriptor
### 3.6 `send` and `recv`
- `send` and `recv` system calls are alternatives to `read` and `write`
- Same functionality with additional flags:
	- `MSG_OOB`: Out-of-band data
	- `MSG_PEEK`: Peek at data without reading
	- `MSG_DONTROUTE`: Send data without routing
- `sendto`/`recvfrom`: take an additional address
	- The kernel ignores the address for stream sockets (there’s a connection)
