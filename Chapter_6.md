# The Link Layers and LANs
- The link layer concerns itself with how packets are sent across *individual links* that make up an end-to-end communication path
- There are two types of link layer channels:
	- Broadcast channels connect *multiple* hosts and therefore require a media access protocol to coordinate the transmission of frames from the many different sources 
	- Point-to-point channels connect only two hosts directly and therefore do not require complex media access protocols to manage
## Introduction to the Link Layer
- Any device that runs a link layer protocol is known as a **node**
	- e.g. Hosts, routers, switches, WiFi access points
- The communication channel that connects two adjacent nodes is known as a **link**
	- Datagrams are transferred from source host to destination host by moving over *each individual link* in the end-to-end path
	- Over a given link, the transmitting node encapsulates the datagram (from the network layer) into a **link-layer frame**
### Services Provided by the Link Layer
- *Framing*: Link layer protocols encapsulate data into frames, which contains the payload plus headers
	- The structure of the frame is specified by the specific link layer protocol (e.g. different frame for ethernet versus WiFi)
- *Link Access*: A media access control (MAC) protocol specifies the rules by which a frame is transmitted onto a link (important for the case where *multiple* nodes share a single broadcast link)
- *Reliable Delivery*: Some (but not all) link layer protocols provide reliable delivery guarantees - this is common for error-prone links such as WiFi but not so common for more stable links such as fiber or coax
- *Error Detection and Correction*: A node may be able to determine bit errors via some error detection mechanism and, in some cases, even correct the error after determining where it had occurred
### Link Layer Implementation
- Most link layer functionality is implemented in hardware, typically through a **network adapter** (also known as a **network interface controller**), where a sender takes a higher layer datagram, encapsulates it into a link layer frame, and finally transmits the frame under the specified link access protocol and a receiver controller receives the frame and extracts the network layer datagram
- Some portion of the link layer is implemented in software, typically dealing with tasks such as assembling link-layer addressing information and activating the controller hardware as well as responding to controller interrupts whenever a frame is received
## Error Detection and Correction Techniques
- Given data $D$ and error-detection and correction bits $EDC$ sent through a link, the goal is to determine if the received $D'$ and $EDC'$ differ from the original due to bit flips
	- Error detection techniques *do not guarantee* that an error is detected, as it is possible for there to be **undetected bit errors**
### Parity Checks
- A single parity check makes use of a **parity bit**, which, in an even parity scheme, is chosen such that the total number of bits that are 1 in the entire frame (including the parity bit) is even; in an odd parity scheme, the parity bit is chosen such that the total number of bits that are 1 in the entire frame is odd
	- Even: The number of ones in the $d + 1$ bits is even
	- Odd: The number of ones in the $d + 1$ bits is odd
	- This scheme will fail if an even number of bit errors occur, and it has been shown that, in practice, bit errors typically come in *bursts* - so there is roughly a 50 percent chance that the error is picked up using just a single parity bit
- A **two dimensional parity** scheme divides the data $d$ into $i$ rows and $j$ columns , computing a parity value for each row and each column
	- With this, when a bit error occurs, *which bit was flipped* can be identified and corrected
		- Only a single bit error can be corrected; if there are multiple bit errors, it may be possible to recognize that bit errors have occurred, but it will not be possible to identify which bits were flipped
		- ![Figure 6.5](./Images/Two_Dimensional_Parity.png)
	- The ability for **forward error correction** can allow for the immediate correction of a bit error at the reciever, reducing the need for retransmission at higher layers
### Checksumming Methods
- Another error detection method is to take the **Internet checksum** of the data:
	- Sum the 16-bit segments of the data and take the 1's complement to form the Internet checksum
	- The receiver takes the sum of the received data and the Internet checksum and checks whether the result is all 1's
- Checksumming methods are typically weak compared to other methods and also are typically only used in the transport layer
	- At the transport layer, error detection is implemented in software and therefore must be simple and fast (because software is not as fast as hardware)
	- Hardware allows for more sophisticated error detection methods
### Cyclic Redundancy Codes
- A commonly used error detection method leverages **cyclic redundancy check (CRC) codes**
- Given a $d$ bit piece of data $D$ to be sent, the sender and receiver first agree upon a $r + 1$ bit pattern known as a **generator** $G$
	- The sender will choose $r$ additional bits $R$ and append them to $D$ such that the resulting $d + r$ bit pattern is exactly divisible by $G$ using modulo-2 arithmetic
		- Under modulo-2 arithmetic, all addition and subtraction is equivalent to just taking the XOR (a + b = a - b = a XOR b)
		- Multiplication and division is similar to standard multiplication and division, except that no carrying is done whenever there is addition or subtraction
	- Error checking involves the receiver dividing the $d + r$ bits by $G$ and checking whether there is a nonzero remainder
- $D \times 2^r \ XOR \ R$ will yield a d + r bit pattern
	- To compute $R$:
		- $D \times 2^r \ XOR \ R = nG$
		- $D \times 2^r = nG \ XOR \ R$
			- If $D \times 2^r$ is divided by $G$, the remainder will be $R$
			- Thus, $R = remainder(\frac{D \times 2^r}{G})$
- Example: $D = 101110$, $d = 6$, $G = 1001$, $r = 3$. It must be the case that $R = 011$ and the transmitted message is $101110 011$ ($D +R$)
	- ![Figure 6.7](./Images/CRC_Calculation.png)
## Multiple Access Links and Protocols
- **Broadcast links** can have multiple sending and receiving nodes all connected to a single, shared broadcast channel - coordinating this is known as the **multiple access problem**
	- Obviously, it is possible for two nodes to transmit frames at the same time, resulting in a **collision** that essentially garbles both of the transmitted frames - thus, collisions should be minimized
- An ideal multiple access protocol over a channel with throughput $R$ should:
	- Provide a throughput of $R$ to a node if that is the only node that has data to send
	- Provide an average throughput of $R / M$ to each node if there are $M$ nodes that have data send 
	- Be decentralized (no master node)
	- Be simple and inexpensive
### Channel Partitioning Protocols
- ![Figure 6.9](./Images/TDM_FDM.png)
- A time-division multiplexing approach divides time into **time frames** and divides each frame into $N$ **time slots**, where $N$ is the number of nodes that the channel can support
	- Each node is assigned a time slot, and whenever it has a packet to send it will transmit its packet during its segmented time slot
	- This approach avoids collisions and is perfectly fair
	- An issue with this approach, though, is that each node is limited only to $R / N$ bits per second even when it is the *only node sending packets*, and this can be inefficient
- A frequency-division multiplexing approach divides the channel into different *frequencies*, each with bandwidth $R / N$ and then assigns a frequency to each of the $N$ nodes
	- This approach also avoids collisions and is fair, but suffers from the same drawback that a node is always limited to $R / N$ even if it is the only one that sends packets
- Another partitioning protocol is known as **code division multiple access**, which assigns a different code to each node such that each node uses its unique code to encode the data that it sends
	- Given the proper choice of codes, this approach can allow for nodes to transmit simultaneously and still have the receivers receive the correct data
	- This approach is also resistive to jamming
### Random Access Protocols
- Under a random access protocol approach, each node transmits at the full rate of the channel $R$ and repeatedly transmits a frame until it gets through without collision
	- Upon a collision being detected, though, a *random delay* is set by the node before retransmission, with the goal being that independent random delays for each node can allow for an eventual passing of data by all nodes to the receiver
#### Slotted ALOHA
- ![Figure 6.10](./Images/Slotted_ALOHA.png)
- Let all frames consist of $L$ bits, time be divided into $L/R$ seconds (a slot is the amount of time to transmit one frame), nodes transmit frames only at the beginning of a time slot, nodes are synchronized at the beginning so they know when the slot begins, and nodes are able to detect collisions before a time slot ends
- Protocol:
	- If a node has a frame to send, it waits until the beginning of the next slot and transmits the entire frame
	- If there is no collision, then the node moves on to consider the next frame (during the next slot)
	- If there is a collision, the node *retransmits* the frame in each subsequent slot with probability $p$ until the frame is transmitted without collision
- This protocol will allow for a node to transmit at a full rate if it is the only node sending packets and is also decentralized, needing only a centralized way to synchronize the nodes at the *beginning*
- There are, however, concerns about efficiency, as slots that are *collided* or are *empty* are inherently wasted
	- The probability that a slot is *successful* (no collisions from other nodes and is not empty) is $p(1-p)^{N-1}$, since one node transmits and all other nodes (N - 1) do not transmit
	- There are $N$ nodes, so the probability that *any* of the nodes are successful is $Np(1-p)^{N-1}$
	- Finding the maximum efficiency yields $1/e = 0.37$, so the effective transmission rate of the channel is $0.37R$, which is not particularly effective
