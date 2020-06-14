## Quiz 1

**Question 1**

If data arrives at the router correctly but is not forwarded tothe correct destination. Which layer does not execute its functionality properly?

***Network layer***

**Question 2**

Why are hierarchical networks unsuitable for real-world networks?

***They are vulnerable to failures and attacks_, if one node fails then the network gets partitioned.***

**Question 3**

What functionality does a Cyclic Redundancy Check offer?

***CRCs can detect but not correct some errors***

**Question 4**

Assume Tom and Jerry use parity blocks for error detection. Tom receives a block from Jerry such that only the checks for the third row and the bit in the lower right corner do not work out. What went wrong during transmission?

***There can be multiple reasons of failure. Possibility 1 is that only the checkbit for the third row got flipped, possibility 2 is that there was an even number of bitflips in third row and all checkbits for other columns got flipped as well.***

## Tutorial 1

**Question 1**

The following bit strings are received messages with a parity bit added. Assuming an even parity, which of them are guaranteed to be wrong?
a) 0010 1010 1010
b) 0011 1111 1111
c) 0010 1000 1101
d) 0001 0110 0010

***In (a) there is 5 ones so an error must have happened, in (c) there is 5 ones as well so we have another error.***

**Question 2**

In the previous question, you detected an error with the following messages:
* 0010 1010 1010
* 0010 1000 1101   

Can you (as a receiver) determine what the correct message is?

***No, parity bits enable error detection but not error correction.***

**Question 3**

In the first question, we saw that parity bit method could not detect an error with the following messages:
* 0011 1111 1111
* 0001 0110 0010

Can we be sure that they are correct?

***No, it is possibile that the number of bitflips was even and they cancelled each other out.***

**Question 4**

A sender and receiver communicate by a protocol which uses parity blocks for error detection. Both know that the data block is a 6x7 matrix (which means the parity block is a 7x8 matrix). The receiver receives a bit sequence and constructs the parity block as follows:

1 0 1 1 0 1 0 | 0   
0 0 0 1 0 1 1 | 1   
1 0 0 0 1 0 1 | 1   
0 1 1 1 0 1 1 | 1   
0 1 0 0 1 0 1 | 1   
1 1 1 1 1 0 0 | 1   
_____________ | _   
1 1 1 0 1 1 0 | 1   

a. Does the receiver find any error?
b. Did the receiver receive the bit sequence correctly?

***The receiver did not find any errors but we cannot be sure the sequence was received correctly, it is possible that multiple errors happened which cancelled each other out.***

**Question 5**

Encode 110100011 using a CRC with the generator 1101.

110100011000 | 1101   
1101   
00000001100   
----------1101   
-------------10

***110100011010***
          
**Question 6**

You received a message 110011100111100001. You know that the message was encoded with CRC using a generator polynomial of 110010. Check whether there was an error during transmission of the message. 

110011100111100001 | 110010   
110010   
00000110011   
-------110010   
-------00000111000   
--------------110010   
--------------00101001   
-----------------110010   
-----------------011011   

***Since the calculated remainder is not `0` we know that an error has happened.***

**Question 7**
What is the Hamming Distance between the following two binary numbers
* 00001011101110110101
* 00011010011010111101

***To get from one number to the other we need 6 bit-flips therefore the Hamming Distance is 6***

**Question 8**
Take a code with the following code words.
a) 110110110
b) 001001001
c) 111000111
d) 000111000

What is the Hamming Distance of this code?

We have the following distances between keywords:
* a -> b = 9
* a -> c = 4
* a -> d = 5
* b -> c = 5
* b -> d = 4
* c -> d = 9

***Hamming distance for the code is the minimal Hamming distance between keywords, it is equal to 4***

**Question 9**
Take a code with the following code words.
a) 0000000000
b) 0000011111
c) 1111100000
d) 1111111111

What can we say when the 0000010101 is received?

***Hamming distance for the code is 5 therefore we can detect all errors with at most 4 bitflips and correct errors with at most 2 bitflips. In this case we know for sure that an error happened and we can suspect the transmitted code was 0000011111**

**Question 10**

Encode 1010110 using an (11, 7) Hamming Code with even parity.

`_ _ 1 _ 0 1 0 _ 1 1 0`    
`1 2 3 4 5 6 7 8 9 A B`

* bit `1` checks 1, 3, 5, 7, 9, B -> 0
* bit `2` checks 2, 3, 6, 7, A, B -> 1
* bit `4` checks 4, 5, 6, 7 -> 1
* bit `8` checks 8, 9, A, B -> 0

***The end result is 01110100110***

**Question 11**

Assuming a (15,11)-Hamming Code using odd parity, we receive 000110111010001. What is the error syndrome of this message? What should the original message be if there is at most one error?

`0 0 0 1 1 0 1 1 1 0 1 0 0 0 1`   
`1 2 3 4 5 6 7 8 9 A B C D E F`

bit `1` is 0, assuming odd parity should be `0`   
bit `2` is 0, assuming odd parity should be `0`   
bit `4` is 1, assuming odd parity should be `0`   
bit `8` is 1, assuming odd parity should be `0`   

***The syndrome of the message is 12, original message was 000110111011001***

## Quiz 2

**Question 1**

Which of the following options for the physical land data link layer has an IEEE standard (as of 2020)?

***Ethernet***

**Question 2**

What is the disadvantage of using byte stuffng with both flag and escape bytes?

***The protocol has a large overhead compared to bit stuffng***

**Question 3**

You want to send a frame to another machine. The data link layer uses flag bytes in combination with byte stuffing to delimit frames. The payload before byte stuffing is 

`0xaa 0xcc 0xbb 0xaa 0xdd 0xdd 0xdd 0xff`

You know that this payload does not contain escape bytes, but may contain flag bytes. Before passing the frame to the physical layer, the data link layer applies byte stuffing, changing the payload from 8 to 10 bytes. What is the flag byte used by this data link layer protocol?

***None of the above; all of the options are impossible because framing by default adds two flag bytes (at the beginning and end of the frame), because 0xbb, 0xcc, 0xdd all appear in the payload it means that at least one escape byte would need to be added but we know this cannot happen because final length is 10 bytes.

**Question 4**

What is the main disadvantage of Stop-and-Wait?

***The protocol has a low throughput as it spends a lot of time waiting for acknowledgements***

**Question 5**

Consider a network with 20 deployed nodes that tell the city lamps when to turn on the lights after sunset.  Each of the nodes uses ALOHA as channel allocation protocol. Assume that all the nodes detect the event (sunset) at the same time and they send the frame at the same fixed moment in time. Retransmissions in case of collision also occur at the same moment in time. What is the expected throughput of the network

***Expected throughput is 0%, if nodes send the frame at exactly the same time then collisions will always be happening.***

**Question 5**

Why does slotted ALOHA have nearly double the throughput of pure ALOHA?

***Stations are synchronized in order to restrict the transmissions to slots. Therefore, the vulnerable period for slotted Aloha is halved***

**Question 6**

Why does the data link layer not always use p-persistent CSMA with a low p for channel allocation?

***p-persistent CSMA with a low value of p has a higher latency than 1-persistant CSMA***

**Question 7**

In the figure on the last slide.  A, B, C, and D are wireless stations. Their radio range is limited to their direct neighbours. All stations send on the same channel. Consider the following scenarios, each consisting of two concurrent transmissions (no MACA): 
1) A is transmitting to B, C is transmitting to B.
2) A is transmitting to B, C is transmitting to D.
3) A is transmitting to B, D is transmitting to C.

Which of them leads to two successful transmissions?

***Only scenario 3 results in two successful transmissions***

**Question 8**

Let stations A, B, and C be such that B is in the range of both A and C and vice versa. However, A and C are not in range of each other. A and C both send a RTS frame to B at the same time using the same channel.  What happens next?

***The RTS frames collide. B does not receive either RTS frame .A and C will try again later.***

**Question 9**

Which of the following is NOT a collision-free protocol?
a)Basic bit-map
b)Binary countdown
c)Tag heist
d)Token ring

***Answer c, tag heist***

## Tutorial 2

**Question 1**
What does the routing table of A look like?

|      to      |      line      |      cost      |
|-------------:|---------------:|---------------:|
|       A      |        -       |        0       |
|       B      |        C       |       11       |
|       C      |        C       |        8       |
|       D      |        C       |        9       |
|       E      |        E       |        2       |
|       F      |        E       |        6       |
|       G      |        G       |       11       |

**Question 2**
How would the routing table change if the delay to G is 12 rather than 11?

|      to      |      line      |      cost      |
|-------------:|---------------:|---------------:|
|       A      |        -       |        0       |
|       B      |        C       |       11       |
|       C      |        C       |        8       |
|       D      |        C       |        9       |
|       E      |        E       |        2       |
|       F      |        E       |        6       |
|       G      |        G       |     **12**     |

***In the new scenario line to G can be either E or G depending on the implemented policy***

**Question 3**

Consider the following network:

(A)--1--(B)--5--(C)

What happens if C becomes unresponsive after nodes have constructed their routing tables? Can you think of changes to thea lgorithm that will prevent the issue?

***If C becomes unresponsive then the change in network structure will be very slowly communicated in routing tables of A and B (count to infinity problem). One possible solution (if distance vector routing has to be used) would be creating an arbitrary cutoff value for the cost, above which it is understood that a node is simply unavailable. Another idea would be to detect update loops.***

**Question 4**

**Question 5**

|      to      |      line      |      cost      |
|-------------:|---------------:|---------------:|
|       A      |        F       |        6       |
|       B      |        H       |        5       |
|       C      |        -       |        -       |
|       D      |        I       |        6       |
|       E      |        F       |        4       |
|       F      |        F       |        3       |
|       H      |        H       |        2       |
|       I      |        I       |        3       |

**Question 6**

***If H becomes unresponsive then it is removed from the routing table, additionally B whose shortest path led through H has a new path calculated through F with cost 9.***

**Question 7**

***0.0.0.0/0 is default gateway, it covers all addresses unsupported by other routers;   
Leonardo covers addresses 101.45.6.0 to 101.45.6.255; Michelangelo covers the address 101.6.0.3.   
Therefore:
101.6.0.3 -> Michelangelo
101.6.0.4 -> Splinter
101.45.6.254 -> Leonardo
166.1.33.5 -> Splinter
166.1.0.7 -> Splinter***

**Question 8**

***Additionally to previously defined routers we now have Donatello which covers addresses 166.1.0.0 to 166.1.255.255 and Raphael which covers 166.1.0.0 to 166.1.15.255.   
Therefore:
101.6.0.3 -> Michelangelo
101.6.0.4 -> Splinter
101.45.6.254 -> Leonardo
166.1.33.5 -> Donatello
166.1.0.7 -> Raphael according to longest matching prefix rule.

## Quiz 3

**Question 1**

***UDP does not re-order***

**Question 2**

***RED reacts to congestion by dropping random packets***

**Question 3**

***Process servers should be used for apps that are hardly used and reasonably tolerant to latency***

**Question 4**

***The TCP header relies on the network layer to encapsulate the transport layer header in the body of the IP packet***

**Question 5**

***After receiving an Explicit Congestion Notification and provided that TCP is used, the sender is supposed to lower the rate of sending traffic by a multiplicative factor.***

**Question 6**

***In terms of Quality of Service metrics, streaming videos requires highest bandwidth with strict requirements on jitter***

**Question 7**

***The main advantage of transparent fragmentation is that it allows intermediate networks to flawlessly process the fragment even if they have different requirements on maximal packet size.***

**Question 8**

***Flow control handles destination bottlenecks, congestion control handles network bottlenecks.***

**Question 9**

***None of the above, segments could have arrived out of order.***

**Question 10**

***With TCP jitter is not prevented from occurring on the connection.***

**Question 11*

***The third segment has number `4 + 1 + 40 + 56 = 101`***

**Question 12**

***Bitmasks are used to select certain bits***

