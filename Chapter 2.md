# Application Layer
## Principles of Network Applications
- When developing a network application, one must write software that runs on multiple end systems
  - This is *not* the same as writing software that runs on network-core devices
  - Rather, the software is often written in high-level programming languages such as C++, Java, or Python
- In a sense, communication for a network application takes place between end systems *at the application layer*; there is not much of a concern, from the network application-perspective, of the lower layers
### Network Application Architectures
- In a **client-server architecture**, there is an always-on host known as the *server* which services requests from other hosts, known as *clients*
  - E.g. A Web server services requests from browsers running on client hosts
  - In this architecture, clients do not communicate directly with each other but rather communicate *through the server*
  - The server typically has a fixed, well-known *IP address* so that the client can always contact the host by sending a packet to this IP address
  - It is typically the case that there is not one server but rather *multiple servers* to keep with data load - these servers are typically housed in **data centers**
  - Client-server architecture is prevalent with Web, FTP (file transfer protocol), Telnet, and e-mail applications
- In a **peer-to-peer (P2P) architecture**, there are no (or minimal) dedicated servers but instead the application exploits *direct communication* between intermittently connected hosts (known as *peers*)
  - This approach is advantageous because it offers **self-scalability** since each peer adds to the service capacity of the system (in addition to requesting, each peer also performs work by servicing)
  - This type of architecture is also more cost-effective since there is no need to spend money on dedicated servers
  - Despite these benefits, they suffer from issues of security, performance, and reliability due to the inherent decentralization
### Processes Communicating
- Within end-systems, it is not actually *programs* that communicate with each other but rather *processes*
- Under the *same system*, interprocess communication is handled by the operating system
- Between processes running on *different hosts*, processes communicate by exchanging **messages** across the computer network
- In the context of communication, the process that *initiates the communication* is known as a *client* whereas the process that *waits to be contacted* is the *server*
  - In *P2P*, a process can be both a client and a server but in general the process that asks for a file is the client (and the process that is to send this file is the server)
- Processes send messages into and receive messages from **sockets**, which is the *API* between the application layer and transport layer within a host
  - An application developer has control of everything on the application-layer side of the socket but cannot control the transport-layer side - other than being able to set a few parameters such as the choice of transport protocol
- To send a message to a particular destination, an **IP address**, which is a 32-bit (now 64-bit) quantity that uniquely identifies a host, and a **port number**, which identifies the receiving process *within the host device*, is necessary
  - A host could be running *many* network applicatons, hence why indicating a specific port number is important to identify the correct application being communicated with
### Transport Services Available to Applications
- When considering which transport layer protocol to use via the socket interface, there are many factors to be aware of
- One concern is **reliable data transfer** - that is, if a protocol provides a guarantee of data delivery, even in the case of packet loss or corruption
  - Many applications, such as Web document transfer or email, depend on this reliability
  - Some applications, however, are **loss-tolerant** and can stand to deal with some amount of data loss (as with audio or video applications)
- Another concern is **throughput** - whether an application can be guaranteed a certain throughput of $r$ bits per second
  - Some **bandwidth-sensitive** applications need certain throughput requirements - such as internet telephony or video conferencing
    - However, some applications use adaptive encoding techniques to encode digitized voice or video at a rate that matches the currently available throughput
  - **Elastic applications** can make use of as little or as much throughput as is available - examples include Web documents and email
- Yet another concern is **timing guarantees**
  - e.g. A guarantee that every bit sent arrives at the receiver no more than 100ms later
  - Realtime applications such as online games or telephony depend on these timing guarantees
- Another concern is **security**, such as ensuring that data sent is *encrypted*
### Transport Services Provided by the Internet
- The network layer provides two transport protocols - TCP and UDP
- **TCP** is connected-oriented and ensures data reliability
  - The client and the server exchange transport-layer control information *before* the application-level messages flow; this **handshaking process** establishes a TCP connection between the sockets of the two processes
    - The connection goes both ways
  - There is also a congestion-control mechanism that throttles a sending process when the network is congested between the sender and the receiver
  - An extension to TCP is known as **Transport Layer Security (TLS)**, which adds on to TCP security services such as encryption, data integrity, and end point authentication
    - The TLS socket will encrypt data before passing it through the TCP socket, and then the receiver TLS socket will decrypt the data before passing is through the receiver's TCP socket
- **UDP** is more of a lightweight protocol, notably being connectionless and unreliable
- The two mentioned transport protocols can deal with reliability and security, but they give no guarantees in regards to throughput or timing
  - Despite the lack of these guarantees, many services that depend on these guarantees are still able to work properly because they have been designed with these limitations in mind
- ![Transport Protocols Used](./Images/Transport_Protocols_Used.png)
### Application Layer Protocols
- An **application-layer protocol** defines how an *application's processes* pass messages to each other, such as the types of messages exchanged, the syntax of messages, the semantics of fields, rules for how to send and respond to messages
  - A common, publicly available application-layer protocol is HTTP
  - Other applications may have proprietary protocols
- An application layer protocol is only one *piece* of a network application - specifically, a piece that governs how to communicate with other applications