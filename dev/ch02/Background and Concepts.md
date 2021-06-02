# Bitcoin as a Computer

Bitcoin is a distributed computer. 

A stand-alone computer has 

- Input and output devices

- A central processing unit (or an ensemble of them)

- Memory

Bitcoin has

- input and output devices attached to unitary computers that are part of bitcoin's network

- A network of nodes that each perform specific tasks, and communicate with each other as necessary

- A number of data structures (including the **ledger**) that are used by nodes to store data during **mining** and for long-term storage. In particular, the ledger is stored as multiple copies on different nodes.

## Nodes

Nodes are unitary computers similar to an ordinary stand-alone computer, and different nodes serve different purposes.

Script engines run and validate code (**script**) included in transaction messages.

## The Blockchain Ledger

The blockchain stores time-stamped representations of successfully executed transactions in a **ledger**. This ledger is a list of representations of **blocks**, which are collections of transactions. 

Nodes compete to most quickly validate a block, and, once having done so, announce that fact to other nodes. Once nodes have confirmed the validity of a newly **mined** block, nodes proceed to work to validate a next block of transactions.

Each node stores a copy of the ledger, and makes it publicly available to devices connected to the network.

It is possible that the leading edge of the ledger can differ between nodes working to add blocks. In this case, a cooperative process clarifies which versions are to be considered valid. Versions not considered valid are modified to match consensus.

## Transactions

Transactions are messages that contain data in a defined format. Transactions are used to send **coins** from one **wallet** to another, and record changes on the ledger. A particular field in transactions can hold arbitrary data, so the blockchain can be used to store general information, not only data related to coin transactions.

# Implementation of Bitcoin

Bitcoin uses a variety of cryptographic and mathematical ideas and algorithms to implement the structure and processes described above. These ideas and algortihms, and related concepts, are the subject of the remainder of this chapter.

Demonstration of use of these will use **bsv**, a javascript library.

## bsv the Javasctript Library

**bsv** provides standard functions for 

- key management,

- building transactions,

- signing transactions, and

- more.

**bsv** encapsulates low-level operations needed for application development, including the writing of data on the ledger.

This chapter assumes the use of the **node-js** runtime, although the library **bsv** can be used with other interfaces, such as a web browser.

**bsv** can be installed using either **npm** or **yarn**.

```javascript
npm install --save --save-exact bsv
// or
yarn add bsv
```

Install **node-js** in a directory in which you will install and run your new code.

Once installed, **bsv** can be used in your code.

```javascript
let bsv = require('bsv')
```

The **bsv** library bitcoin's **testnet**, which is usually used as a test environment by developers. Use of **testnet** can be controlled by setting the **NETWORK** parameter.

```javascript
NETWORK==testnet ./bin/bsv.js.
```

 The **bsv** library is open source, and its code is available at https://github.com/moneybutton/bsv.

# Data Types used by Bitcoin

Bitcoin code is written to strongly favor performance over friendliness to developers. One example of this is the variety of numeric types used.

## Endianness

**Endianness** specifies whether numbers are stored with the **most significant**, or **least significant**, byte at the **lowest** memory location.

**Big endian** numbers are stored with the **most significant** in the **lowest** memory location. **Little endian** numbers are stored with the **least significant** byte in the **lowest** memory location.

 Big endian numbers are used for such things as keys and signatures.

Little endian numbers are used for such things as hash values.



**Use of the incorrect endiannness is a common source of error when storing and using keys, signatures, and hash values.  If code is not working properly, try reversing the endianness of numbers.**



## Big numbers

Cryptographic algorithms use very large numbers for such numbers as keys and some hash values. For instance, Bitcoin uses 256 bit numbers for keys and some hash values.

Use of these big numbers requires non-standard functions. The **bsv** library provides, in **bn.js**, functions to do addition, subtraction, multiplication, division, modulo, and inverse modulo arithmetic, and also to do comparisons such as **greater than**, **equal**, and **less than**.



**Big Number functions do not work with floating point numbers.**



## Practice Code

The code below is in **node-js**.

```javascript
// Practice creating and using Big Numbers.

// Make use of the bsv library. 
> var bsv = require ('bsv')
undefined

// Generate a random hex number and store in the variable 'hex'.
> var hex = bsv.Random.getRandomBuffer(32)
undefined

// Display that number. 
> hex
// THis will display something that looks like:  
// <Buffer be 03 60 7a f0 64 19 86 86 22 25 06 b1 9b ba ce 6d 93 57 d6 9a 66 b4 0f 98 a6 15 3d 2d 1c 9d f6>

// Display the number as a string.
> hex.toString('hex')
// Displays something like:
// 'be03607af064198686222506b19bbace6d9357d69a66b40f98a6153d2d1c9df6'


// Creating big number using the value of 'hex'.
> var bn = bsv.Bn.fromBuffer(hex)
undefined

// Display 'bn' as a string using decimal (base-10). This is default for toString(), so no parameter is needed.
> bn.toString()
// Displays something like : '85945407654093862862680423835470004956440365328379648620174295018053272641014'

// Display 'bn' as a string using hexadecimal. Pass 16 as a parameter to specify hexadecimal.
> bn.toString(16)
//Displays somthing like: 'be03607af064198686222506b19bbace6d9357d69a66b40f98a6153d2d1c9df6'
// Note that this matches (except for any leading zeroes) the representation of 'hex'

//Add 1 to our big number .
// Store the value 1 as a big number.
> let bx = bsv.Bn

// Add our new big number ('bx') to 'bn' and print as a string.
> bn.add(new bx(1)).toString())
<BN: be03607af064198686222506b19bbace6d9357d69a66b40f98a6153d2d1c9df7>

// Similarly most of the arithmetic functions are possible in such manner.
> let n = bx(5)
> let m = bx(6)
> console.log(n.add(m))
// Displays : <BN: b> -- note that 'b' is hex for 11 in decimal.

// The operations sub, mul, div mod (modulo) and invm (inverse modulus) can be used similarly.
```

**bn.js** is specified, and usage described, at https://github.com/moneybutton/bsv/blob/a3ffe5c3016353022569f75c258421d299fdf0ed/lib/bn.js

