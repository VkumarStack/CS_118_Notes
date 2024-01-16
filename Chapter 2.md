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
## The Web and HTTP
### Overview of HTTP
- The **HyperText Transfer Protocol (HTTP)** is implemented via two programs: a client program and a server program
	- The two talk to each other via HTTP messages, with the protocol itself defining the structure of the messages and how they are to be exchanged
	- **Web browsers** implement the **client side** of HTTP whereas **Web servers** implement the **server side** of HTTP (they actually host the data referenced by the URLs)
- The Web consists of **Web pages**, which consist of **objects** that are simply files (HTML, JPEG, .JS, .CSS, etc.) and are addressable via URL
	- Web pages typically consist of a **base HTML file** and several referenced objects from that file
- When a user requests a Web page, they send an HTTP request for the object to the Web server, which then responds with an HTTP response message containing the object corresponding to the Web page
- HTTP relies in TCP as its underlying transport layer protocol - this means that an HTTP client must first initiate a TCP connection with the server before proceeding further
	- After the handshaking process, the browser and server send HTTP messages through the socket interface
	- The main advantage to using TCP as the foundation for HTTP is the guarantee of reliability - the HTTP protocol itself does not need to concern itself with the potential of data loss since TCP covers this
- HTTP is a **stateless protocol**, meaning it does not maintain any information about its clients
### Non-Persistent and Persistent Connections
- **Non-persistent** HTTP connections send each request/response pair over *separate* TCP connections
	- To make a request for an object, the client must first *initiate* a TCP connection to the server (port 80) and *then* (after the connection is established) send the HTTP request (via its socket) and wait for the response (received via its socket)
	- Under non-persistent connection, after a response is received, the client *closes* the TCP connection, and thus further HTTP requests must *reopen* the connection before making the request
	- The **round-trip time (RTT)** (time for a packet to travel from client to server and from server back to client) associated with this approach is *double*
		- There is one round-trip to handshake the TCP connection, as the client must wait for the server's acknowledgement of the connection
		- There is another round-trip for the actual request itself (client sends the request, and then the server returns the request)
- **Persistent** HTTP connections *leave the TCP connection open* after sending a response
	- Multiple requests can be sent back-to-back by the client, without needing to wait for a response
	- HTTP servers will typically close a connection if it has not been used for a certain period of time
### HTTP Message Format
- HTTP Request:
	- Example:
		-      GET /somedir/page.html HTTP/1.1
			   Host: www.someschool.edu
			   Connection: close
			   User-agent: Mozilla/5.0
			   Accept-language: fr
   - The first line is known as the **request line**, and it specifies the request method (GET, POST, HEAD - similar to GET but leaves out object, PUT, DELETE), the URL, and the HTTP version
	   - The URL is relative to the host
   - There are subsequent **header lines**
	   - The host line indicates the host at which the object resides (which is necessary despite there being a TCP connection in the case of web proxies)
	   - The connection line indicates that the connection should be closed after the response
   - In addition to header lines, there may also be an **entity body** which contains the data to be sent to the server (such as a form)
	   - The body is typically empty with the GET method and is populated with POST or PUT
		   - The GET method can still send data, though, by encoding such data in the URL - e.g. `/animalsearch?monkeys&bananas`
 - HTTP Response:
	 - Example:
		 -     HTTP/1.1 200 OK
			   Connection: close
			   Date: Tue, 18 Aug 2015 15:44:04 GMT
			   Server: Apache/2.2.3 (CentOS)
			   Last-Modified: Tue, 18 Aug 2015 15:11:03 GMT
			   Content-Length: 6821
			   Content-Type: text/html
			   (data data data data data ...)
	- The first line is an **initial status line**, which specifies the protocol version, a status code, and the corresponding status message
		- Common Codes: 200 OK, 301 Moved Permanently (new URL is specified in location and the client automatically re-requests), 400 Bad Request, 404 Not Found, 505 HTTP Version Not Supported
	- There are still header lines
		- Last-Modified is relevant for determining stale caches
### User-Server Interaction: Cookies
- Although HTTP is stateless, **cookies** can be used to keep track of users and therefore serve content as a function of the user
- Typically, there is a cookie header line in the HTTP response message, which is sent by the server to the *client*
	- This cookie is stored by the client's system, managed by the browser
		- It is also stored by the server via a database (associating a user with a cookie)
		- The cookie is generated by the server and given to the client typically when there is a request *without a cookie* (so the server knows that the user has not been associated with one yet)
	- Subsequent responses by the client to that specific server includes this cookie in the header of the HTTP request message
	- ![Cookies](./Images/Cookies.png)
- Cookies can be used to track users, as a server can determine which web pages a user visits based on the requests associated with their cookie identifier
	- If a user registers to the website, then their personal information (name, email, etc.) can be used to even further identify the user in relation to their cookie identifier
	- For this reason, cookies are controversial since there are concerns regarding selling the data associated with the users and their online activity
### Web Caching
- A **Web cache** or **proxy server** is a network entity that satisfies an HTTP request on behalf of an origin Web server
	- The Web cache keeps a disk storage of recently requested objects
- A user's browser is typically configured so that a request for an object is first directed to the Web cache
	- The browser establishes a TCP connection with the Web cache and then sends an HTTP request for the object to the Web cache
	- The Web cache checks if it has the object in its local storage - if so (and it is not stale), it sends an HTTP response with the object
		- If it does not have the object, it opens a TCP connection with the origin server, sends an HTTP request for the object, and finally sends this object back  after receiving it
			- The Web cache also stores a copy of this object in its local storage
- Web caching can substantially reduce the response time for a client request, especially in cases where the bottleneck bandwidth between the client and the origin server is much less (worse) than the bottleneck bandwidth between the client and the proxy
	- This is because the Web cache is able to allow for more *hits* that are quickly delivered - thus, the remaining misses overall result in less connections to the origin and thus serves to reduce traffic
- To account for the potential issue of stale caches, the **conditional GET** mechanism is utilized
	- This involves a GET message with a `If-Modified-Since` header
	- The Web cache, when storing objects, also stores the last-modified date, and, upon future request of an object, sends a conditional GET with the stored last-modified date
		- If the origin server responds to the cache that the object has not been modified, then it goes ahead and sends its cached copy to the requester
		- Otherwise, if the object has been modified, the cache will fetch a fresh copy, store it, and return that copy to the requester
### HTTP/2
- HTTP/2 was introduced with the goals of enabling request and response multiplexing over a *single* TCP connection, providing request prioritization and server push, and provide efficient compression of header fields
	- HTTP/1 typically uses persistent TCP connections, but most Web pages suffered from an issue known as **head of line (HOL) blocking**
		- Under a single TCP connection, if there is a bottleneck link, then a request for a large object (such as video clip) followed by requests for small objects would result in the small objects being delayed 
		- Under HTTP/1, browsers circumvented this issue by opening multiple, *parallel* TCP connections
	- TCP's congestion control mechanism, which aims to give each TCP connection sharing a bottleneck link *equal share* of the available bandwidth of the link, also resulted in many browsers running HTTP/1 to "cheat" more bandwidth by opening more parallel connections
- The HTTP/2 solution to the HOL issue is to break each message into *frames* and then interleave response and request messages on the same TCP connection
	- This allows for the small objects to be interleaved *alongside* the larger object rather than delaying them until the larger object fully sent
	- Framing is done by a sublayer of HTTP/2 - the server's framing sublayer breaks down the response into frames, which are sent and interleaved with other frames via the single TCP connection, and when the client receives the frames, its framing sublayer reassembles the original response
- HTTP/2 also contains *message prioritization*, so developers can customize the relative priority of messages so that the server can send first the frames with higher priority
	- The client can also state the dependency of messages (on other messages) so that these dependencies are accounted when the server sends frames
- HTTP/2 also implements *server push*, where a server can send *multiple* responses for a single client request (without needing to wait for the client to send multiple other requests)
- HTTP/3 is currently being discussed, with the intention to replace the underlying transport layer protocol with UDP rather than TCP
## Electronic Mail in the Internet
- Email makes use of the **Simple Mail Transfer Protocol (SMTP)**
	- **User agents** (such as Outlook, Gmail, etc.) allow users to read, reply to, forward, and send messages to the **mail server**, which are placed in the server's outgoing mail queue to be sent *directly* to another mail server - from which the recipient user retrieves the message from their corresponding mailbox
- If the mail servers cannot immediately deliver mail to the recipient server, the message is held in the **message queue**, where it is periodically attempted to be resent (for a certain number of times)
- SMTP utilizes TCP connections to transfer from the sender's mail server to the receiver's mail server
### SMTP
- SMTP is a old protocol, so it is bound by certain legacy restrictions - such as requiring all mail messages to be simple 7-bit ASCII
	- Today, this requires binary multimedia data (e.g. images) to be encoded into ASCII before being sent, and then decoded back into binary upon being received
- In general, the SMTP process involves the user instructing the user agent to send a message, which then sends this message to the mail server where it is placed in message queue
	- The mail server sees the message in the queue and opens a TCP connection to the recipient SMTP server
	- After the handshaking process, the SMTP client sends the message, which is received by server
	- The recipient of the message will receive the message in their mail box and, on opening their user agent, is able to read the message
- SMTP typically does not use intermediary mail servers - the TCP is a direct connection between the two mail servers
- Mail servers are necessary rather than directly connecting users in the case where a user's device (agent) is down
	- The mail servers are always online, so they can maintain missed mails or failed TCP connections via message queues
- A user agent can retrieve mail from the mail server typically through a *HTTP* interface
	- There is an HTTP interface for agents to retrieve mail from their mail servers, but there is a SMTP interface for mail servers to send mail to other mail servers
	- The interface can allow users to manage messages into folders and so forth
	- An alternative to the HTTP interface is the **Internet Mail Access Protocol (IMAP)**
