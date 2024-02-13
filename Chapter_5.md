# The Network Layer: Control Plane
- The **control plane** handles *network-wide* logic, such as routing and how network-layer services and components are configured and managed
## Introduction
- Routing algorithms can run *per router*, where each router possesses a routing component that communicates with routing components in other routers to compute its forwarding tables
- Routing algorithms can also run via a *logically centralized controller*, which computes and distributes the forwarding tables to be used by every router
	- The controller interacts with routers' *control agents*; these control agents *only* interact with the controller - they do not interact with other routers
## Routing Algorithms
- **Routing algorithms** are used to determine efficient paths from senders to receivers
	- These algorithms are in the context of graphs, where networks are nodes and links between these networks are edges
		- The cost of an edge is typically associated with the length of the link, the link speed, or the monetary cost
	- Routing algorithms try to find the **shortest path** between two networks
- **Centralized routing algorithms** compute the shortest path using *global information* about the network (e.g. acquired from a logically centralized controller)
	- Algorithms with global information are known as **link-state algorithms**
- **Decentralized routing algorithms** compute the shortest path in a distributed, iterative manner by the routers, of which do not have complete information about the cost of all network links 
	- Routers exchange information with their neighbors and use this information to gradually compute a shortest path
- **Static routing algorithms** change shortest path routes *slowly over time* whereas **dynamic routing algorithms** change the routing paths as the network traffic or topology changes (done either periodically or in response to changes)
- **Load-sensitive algorithms** vary link costs in response to the congestion on that link
	- Most routing algorithms today are **load-insensitive**
### The Link-State Routing Algorithm
- Dijkstra's algorithm can be used to compute least-cost routes given global knowledge about a routers
	-     N' = {u}
		  for all nodes V
			  if V is a neighbor of u
				  then D(v) = c(u, v)
			  else
				  D(v) = infinity
		  Loop until N' = N
			  find w not in N' such that D(w) is a minimum
			  add w to N'
			  update D(v) for each neighbor v of w and not in N':
				  D(v) = min(D(v), D(w) + c(w, v))
	 - The predecessor nodes acquired from the algorithm can be used to construct the least-cost path, and this path can be used to set the forwarding tables in a node
 - One issue with the link-state routing algorithm arises when the topology where link costs are equal to the load carried on the link - this can cause *oscillation*
	 - ![Figure 5.5](./Images/Link_State_Routing_Oscillation.png)
	 - One solution to this problem is to ensure that not all routers run the link-state routing algorithm at the same time
### The Distance-Vector (DV) Routing Algorithm
- The distance-vector algorithm leverages the recurrence: $d_x(y) = min_v(c(x, v) + d_v(y))$
- In the Distance-Vector algorithm, each node maintains the cost to its direct neighbors $c(x,v)$ and distance vectors that acts as estimates of the distance from that router to *all possible nodes* $D_x=[D_x(y): y \in N]$
	- Periodically, each node sends a copy of its distance vector to its neighbors; upon receiving a distance vector, the neighbor should update its *own* distance vector according to the aforementioned recurrence
- Algorithm:
	-     Initialization
			for all destinations y in N:
				D_x(y) = c(x, y) // if y is not a neighbor, c = infinity
			for each neighbor w
				// The distance vectors of the neighbors are not yet known
				D_w(y) = ? for all destinations y in N
			for each neighbor w
				send distance vector D_x = [D_x(y): y in N] to w
		  loop forever:
			  wait until a link cost changes relative to a neighbor
			  OR a distance vector is received:
			 
			 for each y in N:
				 D_x(y) = min_v{c(x, v) + D_v(y)}
			 if D_x(y) changed for any destination y:
				 send distance vector D_x = [D_x(y): y in N] to all neighbors
	 - The next-hop router that a given router should forward to given a destination is based on the neighboring path that achieves the minimum according to the recurrence
- Example: 
	- ![Figure 5.6](./Images/Distance_Vector_Algorithm.png)
	- Initially, each node only has information regarding its direct neighbors
	- After receiving the first set of distance vectors from each of its neighbors, each router updates its own distance vectors accordingly
		- e.g. For x:
			- $D_x(x) = 0$
			- $D_x(y) = min(c(x, y) + D_y(y), c(x, z) + D_z(y)) = min(2 + 0, 7 + 1) = 2$
			- $D_x(z) = min(c(x, y) + D_y(z), c(x, z) + D_z(z)) = min(2 + 1, 7 + 0) = 3$
	- The routers that actually updated their distance vectors (found new minima), then send their updated distance vectors to all their neighbors (e.g. $x$ and $z$ sent their respective distance protocols to all of their neighbors)
- When the cost of a link between a router and its neighbor changes, the distance vector algorithm will update its distance vector and, if there is a change in least-cost path, will inform its neighbors accordingly
	- This performs well if costs decrease, but can suffer in certain cases if costs *increase*
	- ![Figure 5.7 (b)](./Images/Distance_Vector_Algorithm_Issue.png)
		- Before the cost changes, the distance vectors are $D_y(x) = 4$, $D_y(z) = 1$, $D_z(x) = 5$
		- When the cost changes, $y$ recomputes its distance vector as $D_y(x) = min(c(y, x) + D_x(x), c(y, z) + D_z(x)) = min(60 + 0, 1 + 5) = 6$
			- This information is *wrong*, as *z*'s shortest path was under the assumption that the cost did not change (that it could route through y to get to x)
			- At this update step, a packet may get stuck in a **routing loop**, a packet from *y* would route to *z*, which would then route it back to *y* - this will continue until the distance vectors are updated
		- Once *y* updates its cost, it sends its vectors to *z*, which then recomputes its own distance vectors
			- $D_z(x) = min(50 + 0, 1 + 6) = 7$
		- *z* will then send its updated distance vectors to *y*, which will then recompute 
			- $D_y(x) =  8$
		- This will go back and forth, increasing the updated distances *one at a time* until the correct distances are reached
			- This involves *44* iterations
	- The aforementioned issue can be mitigated via the *poisoned reverse technique*, though this technique will not always fix the issue
		- If a node *a* routes through another node *b* to get to destination *c*, *a* will advertise to *b* that its distance to *c* is infinity as long as it routes to *c* through *b*
		- In the previous case:
			- *z* will indicate to *y* that $D_z(x) = \infty$ since it is initially using *y* to get to *x*
			- When the link cost changes, *y* will update its distance vectors such that $D_y(x) = 60$ and *z* will correspondingly update its distance vectors such that $D_z(x) = 50$ (after receiving the update from *y*)
				- Since *z* is no longer using *y* to get to *x*, it unpoisons its reverse path, and indicates to *y* that actual distance to from *z* to *x* is 50
			- After *z* updates and sends to *y*, router *y* will update its distance vectors so that $D_y(x) = 51$, but now *y* will also poison the reverse path and indicate to *z* that $D_y(x)=\infty$ since now *y* is using *z* to get to *x*
			- Doing this prevents the long time to update *in this case*, but loops with three or more nodes may still suffer from the issue
