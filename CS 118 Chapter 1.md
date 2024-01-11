# Computer Networks and the Internet
## What is the Internet?
### A Nuts-and-Bolts Description
- Traditional devices connected to the internet are known as **hosts** or **end systems**
- These end systems are connected together by a network of **communication links** and **packet switches**
- Links can transmit at data at different rates, known as the **transmission rate** (measured in bits/second)
	- Data being sent is segmented, with the segments also containing header bytes - the resulting **packets** of information are sent through the network from the source to to destination, where it is reassembled
- Packet switches takes an arriving packet and forwards it to an outgoing communication link - most prominent types of packet switches are **routers** and **link-layer switches**
- End systems access the Internet through **Internet Service Providers (ISPs)**, which are networks of packet switches and communication links
	- ISPs themselves are interconnected - typically, lower tier ISPs are connected through national and international upper-tier ISPs, which themselves are directly connected to each other
- All pieces of the Internet run **protocols** to control the sending and receiving of information, with the most important protocols being the **Transmission Control Protocol (TCP)** and the **Internet Protocol (IP)**, which specifies the formats of packets that are sent and received among routers and end systems
	- Internet standards that develop these protocols over time are managed by the Internet Engineering Task Force (IETF)
### A Services Description
- The Internet can be thought of as an infrastructure that provides services to applications 
	- Internet applications are **distributed applications** since they involve multiple end systems that exchange data with each other
	- These applications run on the end systems themselves and not on the packet switches in the network core - these switches just allow for the exchange of data
- End systems make use of a **socket interface** that specifies how a program running on one end system can deliver data to a specific destination program running on another end system 
### Protocols
- The Internet is built upon various protocols, which can allow for congestion control, packet routing control, and so forth
- A **protocol** defines the format and the order of messages exchanged between two or more communicating entities as well as the actions taken on the transmission and/or receipt of a message or other event
## The Network Edge
- Computers and other devices are known as **end systems** because they lie on the **edge** of the Internet
	- They are also known as *hosts* because they host Web applications
### Access Networks
- **Access networks** physically connect an end system to the first router (edge router) on a path from the end system to any end system
#### Home Access
- One of the most prevalent types of residential access to the Internet is through the **digital subscriber line (DSL)**, which makes use of existing telephone company infrastructure for wired local phone access
	- The DSL modem uses the telephone line to exchange data with a digital subscriber line access multiplexer (DSLAM), which is located in the telephone company's local central office
	- Thus, the home DSL modem takes digital data and translates it to a high frequency tone to transmit over the telephone wires to the local central office, where the analog signals are translated back into digital format
	- The telephone line carries both data and telephone signals simultaneously, encoding both at different frequencies; a splitter initially separates the data and telephone signals before forwarding to the DSL modem, which then separates the data and phone signals upon receiving them
		- High-speed downstream (download) channel in 50 kHz to 1 MHz band
		- Medium-speed upstream (upload) channel in 4 kHz to 50 kHz band
		- Two-way telephone channel in 0 to 4 kHz band
- Another prevalent type of residential access is **cable Internet access**, which makes use of existing television infrastructure 
	- Neighborhoods and houses utilize coaxial cables, which are connected to fiber nodes that connect to the cable company via high speed fiber cables 
	- Cable access makes use of cable modems, which typically connect to the home PC through an Ethernet port
	- The cable head end (on the company side) acts similarly to the DSLAM in that it turns the analog signal sent from the cable modems into digital format
	- Cable modems divide the network into two channels - upstream and downstream, with downstream being allocated a higher transmission rate (since typically users receive more data than they send)
	- Cable is a shared broadcast medium, meaning that every packet sent by the head end travels downstream on *every link* to *every home*, which can affect performance
- **Fiber to home (FTTH)** is an upcoming technology that provides an optical fiber path from the company central office directly to the residential home, at a significantly faster rate
	- Passive optical networks (PONs) provide each home with an optical network terminator, which is connected by a dedicated optical fiber to a neighborhood splitter that combines a number of homes into a single optical fiber that connects via an optical online terminator to the central office
- **5G fixed wireless** is also becoming more prevalent, which provides high-speed residential access without any cable
	- Data is sent wirelessly from a provider's base station to the modem in the home, which is connected to a WiFi wireless router
#### Enterprise Access
- Corporate and university settings tend to use local area networks (LAN), with the most prevalent technology being ethernet
	- Twisted-pair copper wires connect to an Ethernet switch, which is then in turn connected into the larger Internet
- In a wireless LAN setting, wireless users receive or transmit packets via an access point that is connected into the enterprise network (likely through ethernet), which is then connected to the wired Internet
	- Wide-area wireless access has developed to provide such an infrastructure at even larger distances from a base station - e.g. 4G and 5G
### Physical Media
- **Twisted-pair copper wire** is a guided transmission medium that is relatively inexpensive and commonly used
	- Insulated pairs of copper wires are twisted together (to reduce electrical interference) and wrapped in a protective shield, although **unshielded twister pair (UTP)** is also commonly used for LANs
	- Twisted pair is common for residential access via DSL and can range in data rates from 10 Mbps to 10 Gbps
- **Coaxial cable** consists of two copper conductors but concentric rather than parallel, allowing for high data transmission rates
	- Coaxial cable is common for cable Internet access, and tends to involve a transmitter shifting the digital signal to a specific frequency band to send to one or more receivers
	- Coaxial cable is a **shared medium**, as a number of end systems can receive what is sent by the other end systems since they are connected directly to the cable
- **Fiber optics** are thin, flexible mediums that conduct pulses of light (representing bits)
	- They can support very high bit rates and are immune to electromagnetic interference but are very expensive
	- As a result of their expense, they are not typically used for short-distance transport but mostly for long distance situations
- **Terrestrial radio channels** are an unguided medium that carry signal long distances via radio waves, though they are sensitive to environmental interferences such as interference
	- There are radio channels that operate over short distances, over local areas, and over wide areas
- **Satellite radio channels** links two or more Earth-based microwave transmitters/receivers, known as ground stations and then regenerate the signal to transmit on another frequency
	- **Geostationary satellites** and **low-earth orbiting satellites** are the most common, with the latter being closer to Earth and the former being 36,000 kilometers above the Earth's surface (which incurs substantial propagation delay)
## The Network core
### Packet Switching
- Messages between end systems are broken down into chunks of data known as **packets**, which travel through communication links and **packet switches**
	- These packets are transmitted over communication links at the full specified transmission rate of the link (so a rate of R bits per second can transmit a packet of L bits over L / R seconds)
- In **store-and-forward transmission**, the packet switch must receive the *entire* packet before it can begin to transmit it (meaning it cannot just transmit the bits as they are received) to an outgoing link
	- This incurs a delay due to the need to receive, store, and process the entire packet before forwarding
	- In general, sending one packet from source to destination over a path of $N$ links each of rate $R$ results in an end-to-end delay of $N \frac{L}{R}$
- A packet switch may have multiple links attached to it, and each of these links have an **output buffer** (**output queue**) that stores the packets that the router is about to send into that link
	- If the packet must be transmitted onto a link but the link is busy, then it waits in the queue, resulting in potential **queuing delays**
	- If the buffer space is exceeded, then **packet loss** can occur since incoming packets must be dropped
- The communication link that a router forwards to is determined via a **forwarding table**; the router examines the destination IP address of the sent packet and searches the forwarding table for an appropriate outbound link based on this destination
	- There are various **routing protocols** that set these forwarding tables
### Circuit Switching
- An alternative to packet switching is **circuit switching**, *reserves* resources needed along a path to provide for communication between end systems
	- Packet-switching, on the other hand, is *on-demand*
- To send information, a network must establish a *connection* between the sender and the receiver, known as a **circuit**
	- On establishing this circuit, the network reserves a constant transmission rate in the network's links for the duration of the connection - this constant rate allows for the sender to transfer the data to the receiver at a guaranteed pacing
- A circuit switch may have *multiple* links, allowing for simultaneous connections 
	- Establishing an **end-to-end** connection between two hosts requires reserving circuits on each of the links between the two ends
- A circuit in a link utilizes either **frequency-division multiplexing (FDM)** or **time-division multiplexing(TDM)**
	- In FDM, the link dedicates a frequency band to each connection for the duration of the connection 
	- In TDM, time is divided into frames of fixed duration and each frame is divided into a fixed number of time slots
		- On establishing a connection, the network dedicates one time slot in every frame to the connection (think of this as similar to process scheduling)
### Packet Switching versus Circuit Switching
- Arguments against circuit switching primarily indicate that dedicated circuits are idle during **silent periods**, resulting in the approach being wasteful
	- If an individual stops using the connection for a moment, then the resources (frequency bands or time slots) cannot be used by other connections
	- On the other hand, packet switching's *on demand* nature mitigates wasteful idle time
### Network of Networks
- End systems connect to ISPs, but these ISPs themselves must interconnect - a *network of networks*
- Rather than directly connect ISPs to each other, a tiered approach is instead used (there is a **customer-provider** relationship at each tier)
	- ISP's connect to a **regional ISP**, which then connects to a possibly larger regional ISP, which then connects to a **tier-1 ISP** (which is usually global)
- **Points of presence (PoPs)** exist at levels beyond the bottom ISP and are a group of one or more routers (at the same location) in the provider's network where customer ISPs can connect into the provider ISP
	- ISPs generally lease a high-speed link and connect one of its routers to a router at the PoP
- Any ISP except for the tier-1 ISP may also **multi-home**, where it connects to two or more provider ISPs in order to deal with potential failure of any of its providers
- ISPs pay their provider ISPs is based on the amount of traffic it exchanges with the provider, but these costs can be reduced by **peering**
	- Nearby ISPs at the same tier can directly connect their networks together so that traffic between them passes over the direct connection rather than through intermediaries
	- A third party company can create an **Internet Exchange Point (IXP)** where multiple ISPs can peer together  
- Additionally, today's internet also involves **content-provider networks**, with a notable example being Google
	- Companies such as Google have interconnected, private data centers that are separate from the Internet
	- In doing so, the private network can "bypass" some upper tiers of the Internet by peering with lower-tier ISPs via IXPs (settlement-free), but in instances where ISPs can be reached through tier-1 networks the private network will still connect to tier-1 ISPs and pay the traffic
		- Nonetheless, the amount it must pay to upper-tier ISPs is still reduced and also allows for greater control over the content delivered## Delay, Loss, and Throughput in Packet-Switched Networks
- **Processing delay** refers to the delay associated with the time it takes to examine a packet's header and determine where to direct it
	- It may also involve checking any bit-level errors
	- This type of delay is typically miniscule - on the order of microseconds or less
- **Queuing delay** refers to the delay associated with a packet waiting in queue as it waits to be transmitted on a link
	- This type of delay is dependent on the number of earlier-arriving packets that are also queued - so if there are no earlier packets then this delay is zero but if there is a heavy traffic of packets all waiting in queue then this delay while be significantly larger
- **Transmission delay** refers to the delay associated with waiting for the entire packet to arrive before sending it out on the link
	- If a packet is $L$ bits, and the transmission rate is $R$ bits per second, then the transmission delay is $\frac{L}{R}$
- **Propagation delay** refers to the delay associated with the propagation of the bit on the link it was sent through
	- This type of delay depends on the inherent physical medium and is therefore bounded by the speed of light
	- If $d$ is the distance between routers and $s$ is the speed of the medium, then the propagation delay is $\frac{d}{s}$
- The **total nodal delay** is the sum of all delays
	- $d_{nodal} = d_{proc} + d_{queue} + d_{trans} + d_{prop}$
### Queuing Delay and Packet Loss
- Since queuing delay can vary from packet to packet, it is often quantified using statistical measures
- Queuing delay depends on the rate at which traffic arrives at the queue, the transmission rate of the link, and the nature of the traffic itself
- If $a$ is the average rate at which packets arrive in the queue, with each packet being $L$ bits, then the average rate at which bits arrive in the queue is $La$ bits per second
- The ratio of this queue bit rate to the link transmission rate is $\frac{La}{R}$, known as the **traffic intensity**
	- If the traffic intensity is greater than 1, then the queue will increase without bound
	- If the traffic intensity is less than 1, then the queueing delay is impacted by the nature of the incoming traffic
		- If packets arrive periodically every $L/R$ seconds, then every packet arrives at an empty queue and there is no delay
		- If packets arrive in bursts (e.g. $N$ packets every $L/R$ seconds), then the $nth$ packet experiences a delay of $(n-1)\frac{L}{R}$
- Typically, the arrival of packets is random, which makes it difficult to quantify the delay appropriately
- If a packet arrives to a full queue, then router must **drop** a packet
### End-to-End Delay
- If there are $N - 1$ routers between a source and destination, each with negligible queueing delays and homogenous processing, transmission, and propagation delays, then the total end-to-end delay is:
	- $d_{end-to-end} = N(d_{proc} + d_{trans}+d_{prop})$
### Throughput in computer Networks
- **Instantaneous throughput*** is the rate at any instant of time in which it takes to transfer data from one end to another
	- **Average throughput** of $F$ bits of data taking $T$ seconds to fully transmit is $F/T$
- Throughput is influenced by **bottleneck links**
	- E.g. For a two-link network, the throughput is the minimum throughput of its two links
	- Typically, though, the core of the Internet is fast enough in terms of throughput that it is seldom the bottleneck link - typically, the bottleneck links are the access networks (edges)
		- If there is very heavy traffic - data from *many* hosts go through a single high-speed link - though it can be the case that a core link can be a bottleneck
## Protocol Layers and their Service Models
- Network protocols are organized into **layers**, corresponding to a **service model** where one layer provides service to layers above it while making use of the services of the layers below it
	- Protocol layers can be implemented in hardware, software, or a combination of the both
- An argued advantage to the layering model is that it enables modularity, allowing for more ease in updating system components
	- An argued disadvantage, though is that a higher level layer may duplicate lower-layer functionality (e.g. error recovery) and that functionality at one layer may require information only present in another layer, which defeats the purpose of separating layers
- The **application layer** is the topmost layer and is where network applications reside
	- It consists of protocols such as HTTP, SMTP (email transfer), FTP (file transfer), DNS, and so forth
	- The information that an applicational layer end system exchanges with another is known as a **message**
- The **transport layer** is the next layer and transports application-layer messages between application endpoints, opting to use either TCP or UDP
	- TCP ensures guaranteed delivery of messages, flow control (matches speed of sender and receiver), and congestion control mechanisms (source throttles its transmission rate when a network is congested)
	- UDP does not ensure the same guarantees as TCP, but as a result is faster (though less reliable)
	- A transport-layer packet is known as a **segment**
- The **network layer** moves packets known as **datagrams** from one host to another, relying on the IP protocol which defines fields in the datagram and how end systems and routers act on these fields
	- There are also routing protocols that determine the routes datagrams take between sources and destinations
- At each node, the network layer passes the datagram down to the link layer, which delivers the datagram to the next node along the route
	- At this next node, the link layer then passes the datagram back up to the network layer
	- The services depend on the specific link-layer protocol employed over the link, which may differ over Ethernet, WiFi, and so forth
	- Link-layer packets are known as **frames**
- The **physical layer** moves individual bits (in contrast to the link layer, which moves multiple bits)
	- The specific protocols are link dependent, drawing heavily on the actual medium of the link
### Encapsulation
- ![Encapsulation](./Images/Encapsulation)
- Routers and links are both packet switches, but link-layer switches only implement the physical and link layer whereas routers implement the physical, link, and network layers
	- Although link-layer switches do not recognize IP addresses as a result, they can still recognize layer 2 addresses such as Ethernet addresses, which allows them to still route properly
- Along each step of the process, each layer may append additional **header** information in addition to the **payload** packet from the layer above
	- This header information is useful on the receiver-side of the same layer - e.g. the receiver-side network layer may need to know the appropriate source and destination addresses
	- This is an example of **encapsulation**
## Networks Under Attack
- **Malware** received through the internet can pose a risk to devices, as they can compromise private information on the device and send them to the attackers
	- These devices may even become part of a **botnet**, which can be leveraged for spam or DDoS purposes
	- Malware can be self-replicating - an infected device can seek out other devices to infect
- A **Denial-of-Service (DoS)** attack renders a piece of Internet infrastructure unusable by exploiting a vulnerability, flooding its bandwidth (sending a large barrage of packets), or flooding its connection (establishing a large number of TCP connections)
	- In bandwidth flooding, a single large source of traffic can be detected relatively easily and purged, hence why **Distributed Denial-of-Service (DDoS)** attacks are more common, where the attacker has multiple sources, each of which barrages packets at the target
- **Packet sniffing** involves leveraging devices (packet sniffers) to record a copy of every packet that passes through a receiver 
	- To protect against sensitive information being stolen, cryptography is often utilized
- **IP Spoofing** involves injecting packets in the Internet using a false source address, allowing one to potentially assume the identity of another party
	- This requires some form of end-point authentication to properly address
