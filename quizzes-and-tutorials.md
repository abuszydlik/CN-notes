## Quiz 1

**Question 1**
If data arrives at the router correctly but is not forwarded tothe correct destination. Which layer does not execute its functionality properly?
_Network layer_

**Question 2**
Why are hierarchical networks unsuitable for real-world networks?
_They are vulnerable to failures and attacks_, if one node fails then the network gets partitioned.

**Question 3**
What functionality does a Cyclic Redundancy Check offer?
_CRCs can detect but not correct some errors_

**Question 4**
Assume Tom and Jerry use parity blocks for error detection. Tom receives a block from Jerry such that only the checks for the third row and the bit in the lower right corner do not work out. What went wrong during transmission?
_There can be multiple reasons of failure. Possibility 1 is that only the checkbit for the third row got flipped, possibility 2 is that there was an even number of bitflips in third row and all checkbits for other columns got flipped as well._

## Tutorial 1

**Question 1**
The following bit strings are received messages with a parity bit added. Assuming an even parity, which of them are guaranteed to be wrong?
a) 0010 1010 1010
b) 0011 1111 1111
c) 0010 1000 1101
d) 0001 0110 0010
_In (a) there is 5 ones so an error must have happened, in (c) there is 5 ones as well so we have another error._

**Question 2**
In the previous question, you detected an error with the following messages:
* 0010 1010 1010
* 0010 1000 1101   
Can you (as a receiver) determine what the correct message is?
_No, parity bits enable error detection but not error correction._
