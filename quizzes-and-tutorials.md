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
