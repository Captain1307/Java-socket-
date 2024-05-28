# Chat Application / https://blog.csdn.net/qq_62480857/article/details/131813279?spm=1001.2014.3001.5501

## 1. Requirement Analysis

### 1.1 Experiment Requirements

The developed program should have the following features:
1. Point-to-point communication functionality allowing any client to send messages to any other client.
2. Group communication functionality enabling a client to send messages to members of a group without other group members receiving them.
3. Broadcast functionality allowing a client to broadcast messages to all other members.

### 1.2 Implemented Features

1. Point-to-point communication functionality allowing any client to send messages to any other client.
2. Group communication functionality:
   1. Clients can create groups and add or remove group members.
   2. Clients can send messages to group members without other group members receiving them.
3. Broadcast functionality:
   1. Clients can broadcast messages to all other members.
   2. The server can broadcast system messages to all clients.
4. Additional features:
   1. Server:
      1. Can force users to log off.
      2. Real-time refresh of online user lists based on client logins and logoffs.
   2. Client:
      1. Login: Clients can log in with valid account and password credentials. If a user is not registered, the entered credentials are used to register the user, and the same credentials are used for future logins.
      2. Real-time user list refresh: Client logins and logoffs are reflected in real-time on other clients.
      3. Chat object switching: Users can switch chat objects within the same interface without losing chat records.
      4. Message notification: When a new message is received and the sender is not the current chat object, the sender is highlighted to notify the user. The highlight is removed when the user clicks on the friend to chat.
      5. Message encryption and decryption: Messages are protected with simple encryption.

## 2. Overall Design

### 2.1 General Structure

Programming Language: Java

To implement the chat room functionality, a server (Server) and client (Client) architecture was designed. Additionally, a login window (Login) was designed to allow users to input their account and password for login.

**Message Type Determination Mechanism**
Messages are decoded and processed according to their numerical codes at the beginning:
- 1111: User login, add new user
- 2222: Add existing user
- 3333: Force logout
- 4444: User logout
- 1333: Private chat
- 2333: Group chat
- 5555: Send: Create group chat / Receive: Group chat
- 6666: Add group member
- 7777: Remove group member
- 8888: Group chat

### 2.2 Server (Server)

**Functions:**
1. Create a ServerSocket object, listen on a specified port, and wait for client connections.
2. Use multithreading to create a thread for each connected client to handle client requests and messages.
3. Implement the server interface, including a chat record display box, online user list, and system message input box.
4. Maintain a client list to record all clients connected to the server and update it in real-time.
5. Support broadcasting system messages and forcing clients to log off.

### 2.3 Client (Client)

**Functions:**
1. Create a Socket object to connect to the server at the specified IP address and port.
2. Use multithreading to create separate threads for receiving messages from the server and sending user input messages.
3. Implement the user interface, including a chat record display box, friend list, and message input box.
4. Support group chat and private chat functionalities, record chat messages, and synchronize the online user list.

### 2.4 Login Window (Login)

**Functions:**
1. Allow account and password input with validation.
2. Instantiate a client for valid input.

## 3. Detailed Design

### 3.1 Data Structures

1. Server (Server)
   - Online users: `clients` - `Map<String, DataOutputStream>`
   - Online user list: `onlineClient` - `List<String>`
   - User list: `userList` - `JList`
   - System message records: `sysMessageList` - `JList`
   - Groups and members: `groups` - `Map<String, List<String>>`

2. Client (Client)
   - Current chat object: `receiver` - `String`
   - Online users: `onlineUser` - `List<String>`
   - Friend list: `friendList` - `JList`
   - Groups: `groups` - `List<String>`
   - Current interface message records: `message_List` - `JList`
   - Highlighted friend list: `highlightFriend` - `Set<String>`
   - Chat records: `chatRecord` - `Map<String, DefaultListModel>`

### 3.2 Main Functions

1. Server (Server)
   - `start()`: Server startup function, creates a server socket, accepts client connection requests, and creates a new thread (ClientHandler) to handle each client's messages.
   - `ClientHandler`: Inner class responsible for handling each client's requests. Main functions include:
     - `run()`: Continuously reads client messages and executes different operations based on message types, such as user login (1111), broadcast message (2333), private chat message (1333), create group (5555), add group member (6666), remove group member (7777), group message (8888), etc.
     - `broadcast()`: Broadcasts messages to all online users for broadcasting and sending system messages.
     - `broadcastToGroup()`: Broadcasts messages to all members of a group for group chats.
     - `sendTo()`: Sends messages to specific users for private chats.
     - `sendOnlineList()`: Sends the current online user list to specific users for refreshing the user list.
   - `refresh_message()`: Sends system messages from the server to all online users or group chats.
   - `forceDisconnect()`: Forces a user to log off from the server.
   - `broadcast()`: Broadcasts messages to all online users.
   - `encryptWrite()`: Encrypts and writes messages to the output stream.
   - `readDecrypt()`: Reads and decrypts messages from the input stream.
   - `nowDate()`: Returns the current date and time.

2. Client (Client)
   - `init()`: Initializes the interface and event listeners for various components.
   - `refresh_message()`: Sends messages and refreshes the message list.
   - `run()`: The program's execution logic. First, it creates and displays the chat window interface. Then, it creates a new thread to update the chat records or friend list every second. Finally, it creates a new thread to handle communication with the server.
   - `HighlightedListCellRenderer`: Custom renderer for the friend list to highlight friends.
   - `encryptWrite()`: Encrypts and writes messages to the output stream.
   - `readDecrypt()`: Reads and decrypts messages from the input stream.
   - `nowDate()`: Returns the current date and time.

3. Login Window (Login)
   - `Login()`: Constructor, loads account information and adds listeners.
   - `login()`: Checks if the account exists and if the password is correct.
   - `loadAccounts()`: Loads account information from a specified text file.
   - `saveAccounts()`: Saves all account information to a specified text file.
