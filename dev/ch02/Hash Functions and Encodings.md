- ## Hash Functions
- A hash function is one that takes a single value as input, and produces a single value as output. Simple hash functions are **tractable**,  meaning that, given some hash value, it is possible to calculate the input used to generate that hash value. Other hash functions are **intractable**, meaning that, given a hash value, it is not practically possible to figure out the input.
- Cryptographic processes use intractable hash functions to encrypt data.
- Bitcoin uses many intractable hash functions, to create values for a variety of purposes, such as:
- Bitcoin addresses
- Block headers
-  Transaction ID's
- ## Primary Hash Functions Used in Bitcoin
- SHA256
    - Output length: 32 bytes
    - Generates unique 256-bit value for a text 
    - Use: Proof of work algorithm
- SHA256SHA256 -- (double SHA256) AKA SHA265d 
    - Output length: 32 bytes
    - SHA256 hash of the SHA256 hash. 
    - Use: blocks and transactions
- SHA512
    - 64 bytes
    - Output length: Generates unique 512 bit value for a text
    - Use: wallet encryption (AES) with paraphrase, applications
- RIPEMD160 
    - Output length: 20 bytes
    - Generates unique 160 bit value for a text
    - Use: Bitcoin Address (part of process)
- SHA256RIPEMD160 
    - Output length: 20 bytes
    - RIPEMD160 hash of the SH256 hash. 
    - Use: Bitcoin Address (part of process)
- sha256HMAC/sha512HMAC 
    - Output length: 32 bytes
    - hash(data1 + data2)  
        - to prevent length extension attack, use HMAC(data1, data2). 
        - HMAC can be used with any hash function
    - Use: Bitcoin Address (part of process)
- ## Exposure of Functions
- BSV exposes these hash functions on the crypto object as
- `bsv.Hash.<hash function name>`
- Ex: `bsv.Hash.sha256`
- ## Properties of Hash Functions
    - Hash functions are intractable; given an input, calculation of a hash value is easy. However, given a hash value, it is very difficult to derive the input. Even a very small change in input produces a very different output.
    - An input value can be of any length; however, the output value is of a fixed length. For instance, SHA256 produces a 256 bit output, and RIPEMD160 produces a 160 bit output.
```javascript
> var bsv = require('bsv')
> var hash1 = bsv.Hash.sha256(Buffer.from('Bitcoin is a global public blockchain')).toString('hex')
> console.log(hash1)
> // Displays:
> // '595526048c7692ab8b12372be9a78a856b6a456791d1adb9b374a62f639d8936'
> var hash2 = bsv.Hash.sha256Ripemd160(Buffer.from('Hashing is so much fun')).toString('hex')
> console.log(hash2)
> // Displays:
> // 'a34ebdb46bc3acbfe35757d259beae29b7529557'
> 
```
- ## Base 58 Encoding
- Bitcoin uses base 58 encoding to store addresses as alphanumeric characters, However, to avoid confusion, some characters that might be misread because they resemble other characters are not used. For instance, lowercase 'L' ('l') and uppercase 'i' ('I'), are very similar, and uppercase 'o' ('O') and zero ('0') are similar. Some other characters are also not used, such as  '/' and '='.
- **Base58check** includes a checksum, a short hash of the hash value itself, prepended to the data. 
- Given a possibly valid base 58 value **Z** containing a checksum **C** and a base value **V**, V can be tested for validity by calculating the checksum for V (call this **C2**).  Z is valid if C = C2.
- ### Code Examples
- Given a string **"My hovercraft is full of eels!"**, it can be encoded into base58 like this:
```javascript
> var string = "My hovercraft is full of eels!"
> var base58 = bsv.Base58.fromBuffer(Buffer.from(string)).toString()
> console.log(base58)
// Displays:
// GXN9t2zPstdo6mRRntFkec5AcS6viBJxPCxfoYYY8
// using Base 58 check encoding: 
> var base58Check = bsv.Base58Check.fromBuffer(Buffer.from(string)).toString()
> console.log(base58Check)
// Displays:
// 2kafpK43NKE8FL8isuao3SEvaDAZWLzrnDPnvb4Hcu9yHXV
```
- We can decode these by using similar functions as so:
```javascript
 > var buf = bsv.Base58.fromString(base58).toBuffer()
> console.log(buf.toString())
// Displays:
// My hovercraft is full of eels!

// Using Base 58 Check
> var base58check = '2kafpK43NKE8FL8isuao3SEvaDAZWLzrnDPnvb4Hcu9yHXV'
> var buf = bsv.Base58Check.fromString(base58check).toBuffer()
> console.log(buf.toString())
// Displays:
// My hovercraft is full of eels!
```
