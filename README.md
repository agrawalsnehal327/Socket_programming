# Socket_programming
# Multi-threaded Chat Server with Group Messaging

A TCP-based chat server implementation supporting multiple concurrent clients, private messaging, group chats, and user authentication.

# Group Details

## Members

- **Snehal** - 218161043
- **Amrit** - 210127


## Features

- Multiple concurrent client connections using threads
- User authentication with username/password
- Private messaging between users
- Group creation and management
- Broadcast messages to all users
- Secure user credential storage

## File Structure

- `server_grp.cpp`: Contains the core functionality for the server, including user authentication, group management, and messaging.
- `client_grp.cpp`: This file contains the client-side implementation (not detailed in the provided code).
- `main.cpp`: The entry point of the server, responsible for setting up the server and accepting client connections.
- `server.h`: Header file that declares all functions and includes necessary libraries.
- `Makefile`: A file for building the server and client applications.
- `users.txt`: A text file containing user credentials in the format `username:password`.

## Building and Running

### Prerequisites
- C++ compiler with C++11 support
- POSIX-compliant system
- Make build system

### Compilation
```bash
make
```

This generates two executables:
- `server_grp`: The chat server
- `client_grp`: The client application

### Running the Server
```bash
./server_grp
```
The server listens on port 12345 by default.

### Running Clients
```bash
./client_grp
```

## Usage Commands

- `/msg <username> <message>`: Send private message
- `/broadcast <message>`: Send to all users
- `/create_group <group name>`: Create new group
- `/join_group <group name>`: Join existing group
- `/leave_group <group name>`: Leave group
- `/group_msg <group name> <message>`: Send group message
- `/exit`: Disconnect from server

## Implementation Details

### Core Components

1. **User Authentication**
   - Credentials stored in `users.txt` (format: `username:password`)
   - `parse()` function loads credentials on server startup
   - `valid()` function verifies login attempts

2. **Connection Management**
   - `std::map<std::string, int> online`: Tracks active users and their sockets
   - `handle_client()`: Dedicated thread for each client
   - Thread-safe operations using mutex locks

3. **Group Management**
   - `std::map<std::string, std::multiset<std::string>> g`: Maps group names to member sets
   - Create/join/leave operations with proper synchronization
   - Group message broadcasting to all members



# Code Explanation

## `server_grp.cpp`

### Global Variables
- **`online`**: Tracks online users with their associated socket descriptors.
- **`g`**: Stores groups and their respective members.
- **`mtx`**: Mutex for synchronizing access to shared resources.
- **`mp`**: Map of user credentials for authentication.

### Functions
- **`valid(const char* user, const char* password)`**: Authenticates a user by comparing provided credentials with stored values in `mp`.

- **`inform(std::string username, std::map<std::string, int>& online, int flag)`**: Notifies online users when someone joins or leaves the chat.

- **`parse()`**: Parses `users.txt` to populate the `mp` map with user credentials.

- **`create_group(int new_socket, std::string username, const std::string& group_name)`**: Creates a new chat group and adds the user to it.

- **`leave_group(int new_socket, std::string username, const std::string& group_name)`**: Removes a user from a group.

- **`join_group(int new_socket, std::string username, const std::string& group_name)`**: Adds a user to an existing group.

- **`broadcast(std::string username, const std::string& message)`**: Sends a broadcast message to all online users.

- **`send_private_message(int new_socket, std::string username, const std::string& target_user, const std::string& message)`**: Sends a private message to a specific user.

- **`send_group_message(int new_socket, std::string username, const std::string& group, const std::string& message)`**: Sends a message to a specific group.

- **`exit_chat(int new_socket, std::string username)`**: Handles user disconnection and updates the online users list.

- **`parse_one_params(const std::string &message)`**: Helper function that parses a single parameter (e.g., group name or message).

- **`parse_two_params(const std::string &message)`**: Helper function that parses two parameters (e.g., target user and private message).

- **`handle_client(int new_socket)`**: Main function for handling client communication, including authentication, receiving commands, and invoking corresponding actions like creating/joining groups, broadcasting, etc.

## `main.cpp`

- **`main()`**: Sets up the server socket, binds it to port 12345, listens for incoming client connections, and creates a new thread for each client.



### Message Processing

1. **Command Parsing**
   - `parse_one_params()`: Single argument commands
   - `parse_two_params()`: Two argument commands
   - Command validation and routing

2. **Message Distribution**
   - Private messages checked for recipient existence
   - Group messages verified for membership
   - Broadcast messages sent to all active clients

## Error Handling

- Socket creation/binding failures
- Authentication errors
- Invalid commands
- Non-existent users/groups
- Network disconnections


### Thread Safety

- Mutex protection for shared resources:
  - User credentials map
  - Online users map
  - Group membership data
- Thread-safe message broadcasting
- Atomic client disconnection handling
