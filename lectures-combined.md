## Introduction
1962 - Internet architecture devised by Paul Baran (RAND) which was fault tolerant
Layered architecture of computer networks:
1) Physical layer
2) Data link layer (Medium Access Control sublayer included)
3) Network layer
4) Transport layer
5) Application layer

Each layer provides certain services but only to the layer directly above and layer directly below.

## Data Link layer
Responsible for transferring _frames_ over a single link:
1) groups bits into frames
2) provides services to the network layer 
3) handles transmission errors (bitflips, lost packets, duplicate packets).
4) regulates data flow

**Possible services**:
* unacknowledged connectionless service (no connection, no error recovery).
* acknowledged connectionless (retransmission if needed).
* acknowledged connection-oriented service (connection is established, rare).

**Framing methods**:
* byte count - first byte of the frame indicates its total length; very susceptible to errors.
* byte stuffing - indicate start and end of the frame with a flag; requires additional usage of escape bytes which adds overhead.
* bit stuffing - contains a special bit pattern designating beginning and end of the frame, in case the pattern repeats in data it is escaped with a single bit which will be removed on the receiver.

Errors are detected and corrected by adding redundant bits to the data.
_Hamming distance_ - number of bits that differ between two bit strings, i.e. number of single bitflips required to change one sequence into another. Hamming distance of `n` between two codewords enables detection of at most `d-1` errors and correction of `⌊(d-1)/2⌋` errors.

**Detecting errors**:
* parity - add a single bit at the end of message such that number of 1's is either even or odd.
* checksums - treat data as N-bit words and add N checkbits that are the `mod 2^N` sum of the words; improved error detection over parity bits (including bursts up to N errors) but vulnerable to systematic errors.
* cyclic redundancy check - use the concept of polynomial division to ensure correct transmission of the message; can detect all double bit errors and odd bit errors (`x+1` factor in generator), detects all burst errors <= number of redundant bits, not vulnerable to systematic errors.

For a block of `n = m + r` bits we have two metrics - (1) code rate `m / n` and number of errors reliably detected `N`

**Correcting errors**:
* Hamming codes - systematic way to add minimal number of redundant bits to a message, check bits are in locations that are a power of 2 and work as parity bits for all bits that have a particular power of 2 in their binary notation (for example bit 7 is protected by bits 1, 2 and 4).
* Binary convolutional codes - output stream is a function of all preceding input bits, bits are decoded with Viterbi algorithm (used in WiFi).

Link layer environment is commonly implemented as Network Interface Card and OS drivers

**Protocols**:
* Utopian simplex protocol - constantly broadcasting without flow-control, doesn't take into account buffer size of receiver, no error recovery.
* Stop-and-wait - waits for ack from receiver before transmitting next message.
* Automatic Repeat ReQuest (ARQ) - receiver acks correctly delivered frames, sender has a timer and resends the frame if no ack - frames and acks must be numbered (for stop-and-wait 2 numbers are sufficient).
* 1-bit sliding window - sender only sends one frame at a time, receiver only accepts one frame at a time.
* Go-Back-N - in case of timeout for acknowledgement, all frames that have been sent after the unacknowledged one are resent, buffer is bigger than one frame.
* Selective repeat - if a frame is lost and another one comes out of order, the receiver sends a NAK for the frame that is missing and only that one is resent (buffer stores received packets, also works with timeouts).

Piggybacked acknowledgements - if possible, ack for the last frame is added to a return message.

## MAC sublayer

**Allocating channel**:
* static allocation - divide channel into parts for each user, some pieces are unused so not very efficient.
* dynamic allocation - uses assumptions several assumptions:
   * traffic is indepenent 
   * there is only one link over which to communicate
   * frame collisions can be detected
   * continuous or slotted time
   * carrier sense or no carrier sense (detection if channel is in use).
   
**Protocols**:
* pure ALOHA - users transmit frames whenever they have data; if a collision occurs, users retry after a random delay. Vulnerable period (collision time for a particular packet) is 2 * duration of transmission (channel utilization <= 18%).
* slotted ALOHA - divides channel into explicit timeslots but requires additional complexity (though is double as efficient as pure ALOHA).
* Carrier-Sense Multiple Access - senders detect whether the channel is in use, it has 3 variations:
   * 1-persistent - wait for idle, then send.
   * Nonpersistent - if busy, wait random amount of time and try again.
   * p-persistent (where 0 < p < 1) - if busy, wait for next slot; if idle send with probability p.
* CSMA with _collision detection_ - if collision is detected immediately stop sending; furthermore we can separate contention periods (check if it is safe by sending a single bit) and transmission periods.
* basic bit-map protocol - collision-free protocol where stations that want to transmit send a '1' in their designated slot, then they transmit in correct order.
* token ring protocol - another collision-free protocol where stations send in a predefined order; the station with token is allowed to send token and a frame to the next station.
* binary countdown - competition for bandwidth is settled by comparing assigned station numbers (discrimination of stations with low numbers).
   
General rule is that being _greedy gives good performance under low load_ and being _generous gives good performance under high load_.

Wireless channels bring additional difficulty of _exposed_ and _hidden_ terminals (out of reach of a station) and undetectable collisions (no common carrier):
* Multiple Access with Collision Avoidance:
   1. Station that wants to start transmitting sends an RTS (request to send) signal.
   2. When RTS is received by the target station it sends a CTS (clear to send) signal.
   3. All stations that received both RTS and CTS have to be quiet to avoid collisions.
   4. If a station received only RTS then it is clear to send data to other stations out of range.
   
In classical Ethernet (single connection):
* CSMA/CD is needed to transmit data successfully.
* MAC protocol is 1-persistent CSMA/CD.
* Random delay after collision is computed with Binary Exponential Backoff.

IEEE 802.3 frame consists of 8 fields; it differs from DIX with an SOF flag and a _length_ field instead of _type_. There is a minimal size of payload so that collisions can be detected.

In 802.11:
* stations cannot detect collisions.
* uses ack system to determine collisions (if ack is lost then frame gets retransmitted).
* can use MACA but usually uses CSMA/CA:
   * frames carry a Network Allocation Field (NAV) that indicates the length of the transmission;
   * stations wait for the end of transmission period (anyone who receives an RTS or CTS waits);
   * backoff slots are used to avoid collisions
   
IEEE 802.11 frames are a lot more complicated and include for example duration field (NAV) and three addresses (source, destination, relay station).

In WIMAX:
* MAC is connection-oriented.
* IP is connectionless.
* Convergence sublayer between IP and MAC.
* Clients request the bandwidth they need, different kinds of service can be requested.

**Switching on data link layer**:
* Hub
* Switch -  more complexity but enable communication between everyone in network; a lot more expensive

**Backwards learning algorithm**:
1. Switch has a hash table to store all plugged computers.
2. When a packet from station A to station B comes, the port of station A will be saved in the table.
3. If the switch knows where B is located, it sends the message directly; else it broadcasts

Obvious disadvantage is that it would be impossible to ever change ports so in practice there are timeouts.

Redundant links between bridges are dangerous because packets can loop indefinitely and cause storms that result in total network failure (all bandwidth gets stolen).

**Spanning tree algorithm**:
1. A root bridge gets selected.
2. Every bridge finds its shortest path to the root.
3. Leftover links are unused to avoid shortcircuiting.

**Virtual LANs**:
Physical layout of network doesn't always match its logical layout; moreover, different machines can have different security requirements (for example one should not be connected to outside). Building separate networks is unfeasible so _virtual networks_ are used in practice. In a VLAN (802.1Q) frames are marked with the network they belong to - switches need to be able to convert frames.

## Network layer
Responsible for transfering messages between computers. Lowest layer concerned with end-to-end.
1) how to get from A to B?
2) how to prevent network congestion?
3) how to provide Quality of service?
4) how to connect multiple networks?

On network layer it is possible to distinguish two types of service: 
* connectionless (more popular) uses datagrams:
   * routers use routing algorithms to decide where to send each packet _individually_;
   * used by the Internet Protocol;
   * important to note that routers do not hold state information about connections;
   * QoS and congestion control is difficult in connectionless
* connection-oriented (mostly enterprise) use virtual circuits:
   * fixed route is decided in the beginning;
   * all packets that are part of the connection follow this route;
   * ISPs sometimes use this on top of IP;
   * QoS and congestion control are easy if resources can be allocated.

**Properties for routing**:
1. Correctness
2. Simplicity
3. Robustness
4. Stability
5. Fairness
6. Efficiency

*Optimality principle in sink trees* - if the best route from C to A goes through B, then B to A follows the same path; collection of all best paths to a given destination forms a _tree_ (benchmark for real implementations).

*Routing table* - is a table in memory of the router which for every address stores the data about the distance and link that should be chosen to forward the packet.


**Routing algorithms**:
* distance vector routing:
   1. send your _distance vector_ (information about links to other machines) to your neighbours;
   2. use incoming distance vectors from neighbours to construct (or update) a routing table;
   3. if something goes wrong (link goes out) -> _count to infinity problem_ - very slow updates.
* link state routing (more complicated but no _count to infinity_):
   1. routers only send packets with information about their direct neighbours;
   2. packets are flooded over the network;
   3. routers build in parallel their overview of the network using _Dijkstra's algorithm_;
   4. requires sequence numbers because packets can get mixed during flooding.   
* hierarchical routing (reduces routing table size for large networks):
   1. routers get connected into clusters;
   2. information about how to transmit to a particular cluster is stored in a routing table.
   
**Congestion control**:
_Flow control_ on **data link** layer makes sure that a sender does not send information faster than it can be received. End-to-end _congestion control_ is a joint responsibility of the **network** and **transport** layers.

