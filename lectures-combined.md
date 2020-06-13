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
_Flow control_ on **data link** layer makes sure that a sender does not send information faster than it can be received. End-to-end _congestion control_ is a joint responsibility of the **network** and **transport** layers. _Goodput_ is the rate of useful (without repeats and errors) packets arriving at the receiver.

**Congestion control approaches**:
* network provisioning - increase available bandwidth.
* traffic-aware routing - choose routes depending on traffic and not only topology.
* admission control - new traffic load is allowed only if the network has sufficient capacity (can be combined with looking for an alternative uncongested route).
* traffic throttling - send messages (choke signal) in opposite direction to indicate network congestion for example with special bits in IP (*Explicit Congestion Notification*) packet or through TCP; can be done end-to-end or link-by-link depending on implementation.
* load shedding - if there is a risk of total network failure packets start getting dumped on overloaded routers (packet loss is implicit signal for the sender to slow down); this is called *Random Early Detection*.

**Quality of service**:
* bandwidth - maximum data rate in bits per second (file sharing, VOD).
* delay - time it takes to get from source to destination (telephony, videoconferencing)
* jitter - variation in packet delay where 0 means constant delay (AOD, VOD, telephony, videoconferencing).
* packet loss - probability of packets being dropped
* contract negotiation*

|      Network service      |   application   |
|--------------------------:|----------------:|
|    constant bit rate      |    telephony    |
|real-time variable bit rate|videoconferencing|
|non-real-time var. bit rate|streaming movies |
|     available bit rate    |  file transfer  | 

Network services are configurations of QoS. To give guarantees, network needs to be able to control:
* data rate;
* packet scheduling;
* admission control.

_Traffic shaping_ regulates rate and burstiness of data entering the network:
* leaky bucket - constant rate and maximum size of queue
* token bucket - enables regulating bursts

_Internetworking_ is getting packets from source to destination through different networks:
* they may use different protocols
* they may have different QoS guarantees
* they may have different maximum packet sizes

If source and destination networks use the same protocols then _tunneling_ is possible (network is hidden). When networks have different maximum packet size; the minimal largest size of packets on a route is called Path Maximum Transmission Unit. _Packet fragmentation_ can be done:
* transparently - reassembled by the router on the other side of the link (very complex).
* nontransparent - packets need to be reassembled at the destination.
* MTU discovery - when a packet is too large for a router it is refused and the source is required to fragment in (used in IP).

**IPv4**:
* 13 mandatory fields and one optional field.
* time to live - used to avoid loops in the network, normally counts the number of hops.
* identification - bookkeeping for fragmentation.
* IPv4 addresses are 32-bit written in _dotted decimal notation_.
* routing table sizes are reduced with _hierarchical routing_.

Example address 37.60.0.0/16 means that 16 bits are used by the network (subnet mask) and last 16 bits belong to host addresses.

**Classless InterDomain Routing** is used to manage hierarchy in routing tables (_route aggregation_).
**Network Address Translation** limits the use of IP addresses (only 4 billion) by creating an internal IP scheme for all devices connected to a single router (which has an external address). NAT is implemented with ports (NAT box keeps a table of source-destination links). Smartphones use _carrier-grade NATs_ implemented at telecom level.

**IPv6**:
* only 8 fields + some options (improved bandwidth and latency).
* easier to add _options in the header_.
* many more addresses (128-bits in hexadecimal notation).
* improved security capabilitites.

**Internet Control Message Protocol** - when something goes wrong, routers send these messages to the senders. Possible use cases include destinatio unreachable, time exceeded (`traceroute`), echo and echo reply (`ping`), and router advertisment/solicitation.

**Address Resolution Protocol**:
1. Sender doesn't know who owns an IP address.
2. Sender broadcasts over Ethernet an ARP packet.
3. Owner replies with an ARP packet containing its IP and MAC addresses (vulnerable to spoofing).

**Dynamic Host Configuration Protocol**:
1. Sender doesn't know its own address (only MAC addresses are built into NICs)
2. Sender without an IP requests to get one assigned by sending a "DHCP discover" packet.
3. When DHCP server receives the "discover" packet, it offers an available address (broadcast).
4. Also used to configure mask, default gateway, DNS, time servers, etc.

**MultiProtocol Label Switching** is a routing algorithm which uses short labels instead of long network addresses, at each hop labels need to be replaced (basically circuit switching + tunelling). Routers that use only IP are called _label edge routers_, while the ones using labels are called _label switched routers_. MPLS used for example by ISPs (lack of transparency). 

**Open Shortest Path First** is an _Interior Gateway Protocol_ used for routing ***within*** a large independent network; a form of link state routing (graph representation of the network) and uses a hierarchy of _areas_ to manage networks.

**Border Gateway Protocol** is an _Exterior Gateway Protocol_ used for routing ***between*** independent networks (supports arbitrary policies by ISPs, companies or countries); a form of distance vector routing combined with path vector routing.

## Transport layer
Responsible for a _reliable_ (or sometimes _unreliable_) data stream over an _unreliable_ network. Transport protocols are run only on the end machines, smallest message entities of TCP are segments.
1) Transport Service Access Point (ports) are used by applications to communicate with transport layer.
2) Network Service Access Points (IP addresses) are used for communication with network layer.

**Primitives used to connect TSAP and NSAP**:
* listen - wait for a process to contact us.
* connect - connect to a process that is listening.
* send - transmit data over the established connection.
* receive - receive data over the established connection.
* disconnect (close) - release the connection.

**Berkeley Socket primitives** used by TCP include primitives above and additionally:
* socket - create a new communication endpoint.
* bind - assign a local address to the socket.
* accept - passively accept an incoming connection request.

**Process server** is used to limit use of resources for applications that only need to be connected to a port from time to time, additional servers are started only when needed and connection is passed to them.
**Multiplexing** is used to handle multiple transport connections over one network connection, **inverse multiplexing** is used to split one transport connection over multiple network connections (it requires machines which are capable of handling more than one network connection at a time - IP limited).

**Reliability problems**:
* data loss (even if links are reliable, routers can still drop packets)
* retransmission can result in duplicate segments (links have different latencies)

**Sequence numbers** are used to detect data loss, detect duplicates, and reorder data:
* need to ensure that duplicates are discarded and there are no false positives.
* initial sequence number is based on _time-of-day clock_ (keeps working even in case of crashes).
* sequence number `S(i) = S(i-1) + #bytesSent`.
* sequence numbers which at some particular time cannot be chosen (because a packet with that number can still be in the network) belong to the _forbidden region_, in TCP sequence numbers need to be renegotiated after around four hours.

**Three-way handshake** is used to negotiate initial connection:
1. Party A sends a _connection request_ containing its initial sequence number `x`.
2. Party B sendes an _ack_ containing acknowledgement of `x` and its initial sequence number `y`.
3. Party A can start sending data and acknowledges `y`.
4. In case of duplicates a _reject_ signal can be transmitted, non-duplicate continues normally.

**Connection release** needs to happen when the exchange is finished:
* asymmetrically - either participants finishes without agreement, can result in data loss
* symmetrically -> _two armies problem_ (no certain way to know whether parties have reached agreement):
   1) party A asks to release connection.
   2) party B agrees to release connection.
   3) party A sends an ack to party B.
   4) party A ends the connection
   5) if party B receives an ack it ends the connection as well, if not it will realize after some time.
   
**Error control and crash recovery** must be done on the application layer (in general recovery from layer `k` failure must be done by layer `k + 1`).
**Flow vs. congestion control** - there are two sources of troubles on the transport layer:
* small capacity receiver - sender needs to slow down -> flow control.
* small capacity network - network needs to provide feedback -> congestion control.

**Flow control** prevents a sender from overwhelming the receiver:
* tightly linked to the buffer management at the receiver:
   1. while connection is being set up, receiver _piggybacks_ a message about its buffer size;
   2. sender sends a message of M bytes;
   3. after each message, receiver sends the maximal size of message it can accept (current buffer).
* messages about buffer size are always piggybacked.

**Congestion control** - transport layer takes care of _offered load_ (on top of network and transport)

**Fair bandwidth allocation**:
* supposed to minimize the differences between bandwidth offered to different users in the network
* often _max-min fairness_  which is supposed to maximize minimum bandwidth and use excess where possible.
* when new connections are added, the bandwidth needs to be reallocated (convergence is difficult because total available bandwidth, network topology and other clients are unknown).
* dynamically adjusting bandwidth using trial and error:
   1. start sending while slowly increasing the usage;
   2. once a congestion signal (feedback/loss/latency) is detected, the machine slows down.
* finding optimum (fair and efficient) when sharing bandwidth can be done additively, multiplicatively or as a combination of both -> TCP often uses additive increase and multiplicative decrease.

Most popular protocols of transport layer are ***UDP*** and ***TCP*** (but others exist - Google's QUIC).

**User Datagram Protocol**:
* header provides ports needed to connect to remote applications.
* only four header fields (source port, destination port, UDP length, UDP checksum).
* very basic error control.
* does not provide flow control, congestion control and retransmissions.
* works well with multiplayer games, VoIP, video streaming (a lot faster than TCP).
* described in RFC768

**Transmission Control Protocol**:
* provides a ***reliable end-to-end byte stream*** over an unreliable network.
* header includes fields similar to UDP but also sequence number and ack number.
* in total number of header fields is 15 + options and data.
* more than 10 RFCs and a summary in RFC4614.

**TCP implementations**:
* every data byte has its own sequence number, based on clock with additional randomization.
* performance improvement by fast retransmission - resend immediately when acks come out of order.
* in three-way handshake original number `x` is acknowledged as `x+1` (all subsequent SN larger by one).
* in flow control if a window size is 0, then the sender stops sending and waits for a next ack
* original version uses additive decrease with multiplicative decrease using congestion window on the sender (specifies how many segments can be transmitted and dynamically adapts to network flow with ack rate):
   * _TCP Tahoe_ uses _slow start_ - for every ack sender transmits two packets, up to an arbitrary treshold the bandwidth grows exponentially, then switches to additive increase (packet loss resets window);
   * _TCP Reno_ couples slow start with fast recovery (window is set only to new treshold);
   * new versions of TCP use different variation of the same idea
   
|   Protocol   |            Signal             |   Explicit?   |   Precise?   |    Remarks    |
|-------------:|------------------------------:|--------------:|-------------:|--------------:|
| XCP          | Rate to use                   | Yes           | Yes          |               |
| TCP with ECN | Congestion warning            | Yes           | No           |               |
| FAST TCP     | End-to-end delay              | No            | Yes          |               |
| Compound TCP | Packet loss & end-to-end delay| No            | Yes          |used in Windows|   
| CUBIC TCP    | Packet loss                   | No            | No           |used in Linux  |
| TCP          | Packet loss                   | No            | No           |               |

**Domain Name System** translates _human readable names_ to IP addresses:
* domains follow a hierarchical structure; top level domains are assigned by _ICANN_ and organizations can request second-level domains from _registrars_.
* translation happens at a ***name server*** whose location is configured with _DHCP_ (or manually).
* there are two types of queries to DNS:
   * recursive queries - machine communicates with a _local name server_ which either serves the IP address or queries other name servers hierarchically (first asks _root name server_)
   * iterative queries - _local name server_ provides the address of _root name server_ to the machine which then has to query other name servers hierarchically (computation put on the local machine)/
* name servers reply with ***domain resource records**:
   * IPv4 address (type A);
   * IPv6 address (type AAAA);
   * domain that accepts email (type MX);
   * name server for this domain (type NS).

**Email**:
* messages contain a header (from, to, subject), a body (text) and an envelope (data to get mail delivered).
* _POP3_ and _IMAP_ are used to interact with the mailbox, IMAP is newer and doesn't delete mail on server.
* users and _message transfer agents_ use _SMTP_ with Extensions to get mail from source to destination.
* email address is resolved to IP with DNS.

**Simple Mail Transfer Protocol** is ASCII-based and in basic version doesn't support binary data and authentication. **Multipurpose Internet Mail Extensions** are additional headers for transfering different types of data (text, images, video, multipart). When it was introduced servers were not expecting non-ASCII data so all binary streams are encoded Base64 (\[A-Za-z0-9+/]) where 6 bits are translated into 1 character (if the number of bits in bitstream isn't divisible by 6 it is padded with 00's represented with "=").

**Internet Message Access Protocol** sends commands to mail server to manipulate mailboxes, commonly:
* LOGIN - log into server;
* FETCH - fetch messages from a folder;
* CREATE/DELETE - create or delete a folder;
* EXPUNGE - remove messages marked for deletion.

When logging into mail server through a webpage WWW (so HTTP or HTTPS) is used instead of POP3/IMAP.

**World Wide Web** is managed in form by W3C, it works as an exchange of HTTP requests and responses. _Persistent connections_ allow browsers to issue multiple requests over the same TCP connection, moreover requests can also be pipelined. Web uses **Universal Resource Locators** powered by DNS which specify the _protocol_, _domain name_, and a _path_ to resource.

Browsers use MIME type to decide what to do with data:
* text/html is rendered;
* other data is passed to a plugin or another app.
