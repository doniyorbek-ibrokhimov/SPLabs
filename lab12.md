# Lab 12: System Programming
# Interprocess Communication using UNIX Domain Sockets in C

**Muhammad Bilal Qureshi**  
Department of Computer Science  
School of Engineering  

**Report Submission Date:** In next lab

## Objective

- To understand and implement local interprocess communication (IPC) using UNIX domain sockets in C programming.
- To differentiate between the types of UNIX domain sockets: Stream, Datagram, and Raw sockets.
- To build a client-server application using UNIX domain sockets for communication.
- To practice socket creation, binding, listening, accepting, and sending/receiving messages between processes on the same machine.

## Task 1: Chatting between Server and Client using UNIX Domain Sockets

Write a C program that creates a client-server application using UNIX domain sockets. The server should:
- Bind to a UNIX socket file.
- Listen for incoming client connections.
- Accept connections from the client.
- Exchange messages with the client (e.g., echoing messages sent by the client).

The client should:
- Connect to the UNIX socket of the server.
- Send messages to the server.
- Display the server's responses.

The server and client should continue communicating until the client sends a "bye" message, at which point the connection should be closed.

**Sample Output:**
```
$ ./Server
Server started, waiting for client connection...
$ ./Client
Enter message: Hello, Server!
Server Response: Hello, Client!
Enter message: bye
Server Response: bye
Connection closed.
```

**Actual Output:**

Terminal 1 (Server):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro task1 % ./server
Server started, waiting for client connection...
Client connected
Client: Hello, Server!
Client: bye
Closing connection
d.ibrokhimov@Doniyorbeks-MacBook-Pro task1 %
```

Terminal 2 (Client):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro task1 % ./client
Enter message: Hello, Server!
Server Response: Hello, Client!
Enter message: bye
Server Response: bye
Connection closed.
d.ibrokhimov@Doniyorbeks-MacBook-Pro task1 %
```

## Task 2: Real-Life Scenario - Restaurant Order System using UNIX Domain Sockets

Imagine you are building a simple restaurant order system. The server manages incoming orders, while the client places an order. Use UNIX domain sockets for communication between the client and server.

**Scenario:** The server program should:
- Listen for incoming order requests from clients.
- Receive order details (e.g., food item and quantity) from the client.
- Acknowledge the order by sending a confirmation message.

The client program should:
- Connect to the server.
- Send an order with food item and quantity.
- Wait for the server's acknowledgment and print the confirmation message.

Your task is to simulate a simple restaurant order process between the client and the server using UNIX domain sockets.

**Sample Output:**
```
$ ./Server
Server is ready to accept orders...
$ ./Client
Enter food item: Pizza
Enter quantity: 2
Order Confirmation: Pizza x 2 has been received.
Enter food item: Salad
Enter quantity: 1
Order Confirmation: Salad x 1 has been received.
```

**Actual Output:**

Terminal 1 (Server):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro task2 % ./restaurant_server
Server is ready to accept orders...
Customer connected
Received order: Pizza x 2
Received order: Salad x 1
Customer left the restaurant
Server closed
d.ibrokhimov@Doniyorbeks-MacBook-Pro task2 %
```

Terminal 2 (Client):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro task2 % ./restaurant_client
Enter food item: Enter quantity: Order Confirmation: Pizza x 2 has been received.
Enter food item: Enter quantity: Order Confirmation: Salad x 1 has been received.
Enter food item: d.ibrokhimov@Doniyorbeks-MacBook-Pro task2 %
```

## Conclusion

In this lab, you have learned how to implement local interprocess communication (IPC) using UNIX domain sockets in C. You practiced socket creation, binding, listening, accepting connections, and sending/receiving messages between server and client processes on the same machine. Additionally, you explored a real-life scenario by building a restaurant order system using UNIX domain sockets for communication. By the end of this lab, you will be able to create client-server applications using UNIX domain sockets for local communication.

## When to Use send()/recv() Over read()/write()

- **More Control over Socket Behavior:** If you need to control socket-level options such as non-blocking behavior or message flags (e.g., to send out-of-band data), send() and recv() are more appropriate.
- **Managing Larger Messages:** If you are sending discrete messages where boundary handling is important, send() and recv() are more convenient.
- **Stream Semantics:** When using UNIX domain sockets, especially for stream communication (like TCP sockets), send() and recv() provide more flexibility and handle scenarios where data is sent and received in chunks.

## When to Use read()/write()

- **Simplicity:** If you just need basic read and write functionality without dealing with message boundaries or socket-specific features, read() and write() can be simpler.
- **Compatibility with File Descriptors:** If you're working with other types of file descriptors, read() and write() provide a more general interface. They are also used for working with regular files, pipes, and sockets, making them more generic.
