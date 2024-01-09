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
