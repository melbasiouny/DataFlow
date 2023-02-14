# DataFlow

A flexible asynchronous networking framework for C# that provides efficient data transmission through its sophisticated packet architecture. It supports handling numerous connections and provides an easy-to-use API for managing connections between clients and servers.

## Getting Started

DataFlow's API is better illustrated as a running example. In this scenario, we will be creating a simple server that pings newly connected clients.

### Server

```csharp
// Provides the server class.
using DataFlow;

// Creates a new server instance that listens on port 8080.
var server = new Server(8080);

// Invoked when a new client is connected.
server.Connected += async delegate(object? _, Guid guid)
{
    Console.WriteLine($"Established connection with {guid}.");

    // Creates a new ping packet of identifier (1).
    var ping = new Packet(1, out var binaryWriter);
    
    // Packets return a binary writer used to write data to the packet.
    binaryWriter.Write("Ping");

    // Sends the ping packet to the newly connected client using their GUID provided by the 'Connected' event.
    await server.Send(guid, ping);
};

// Starts the server.
server.Start();

// Prevents the server from disconnecting immediately.
Console.ReadLine();

// Shuts down the server disconnecting all connected clients.
server.Shutdown();
```

### Client

```csharp
// Provides the client class.
using DataFlow;

// Creates a new client instance using the provided IP address and port.
var client = new Client("127.0.0.1", 8080);

// Invoked when the client is connected to the server.
client.Connected += delegate { Console.WriteLine("Successfully established connection."); };

// Invoked when the client is disconnected from the server.
client.Disconnected += delegate { Console.WriteLine("Connection ended by server."); };

// Connects the client to the server.
client.Connect();

// Prevents the client from disconnecting immediately.
Console.ReadLine();

// Disconnects the client from the server.
client.Disconnect();

// Packet handler used to identify the method 'HandlePing' as a handler for packets of identifier (1).
// Packet handlers are automatically invoked when a packet with the matching identifier is received.
[PacketHandler(1)]
void HandlePing(Packet packet)
{
    // Deserializes the packet returning a binary reader.
    var ping = packet.Deserialize();

    // Output the data contained in the packet.
    Console.WriteLine(ping.ReadString());
}
```

---

# API

## DataFlow

[Client](./dataflow.client.md)

[Packet](./dataflow.packet.md)

[PacketEventArgs](./dataflow.packeteventargs.md)

[PacketHandlerAttribute](./dataflow.packethandlerattribute.md)

[Server](./dataflow.server.md)

## DataFlow.Utilities

[Logger](./dataflow.utilities.logger.md)

[LogLevel](./dataflow.utilities.loglevel.md)
