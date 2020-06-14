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
          
