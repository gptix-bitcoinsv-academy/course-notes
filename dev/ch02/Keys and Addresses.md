- ## Private Keys
- # One should be extremely careful to protect private keys! 
- # Anyone with a private key can spend funds!
- Bitcoin private keys are 256-bit big numbers, generated using a seed number. The more random this seed is, the better.
- Bitcoin **wallets** are software used to store and manage private keys, public keys, and transactions. Bitcoin wallets use the **Wallet Import Format** (WIF) for private keys. Such private keys are Base 58 check formatted strings, and contain a byte that indicates whether the corresponding public key is compressed.  By default, a public key is compressed. 
- A private key associated with a **compressed** public key is 52 characters, starting with the character 'L' or 'K' on mainnet, or starting with the character 'c' on testnet.
- A private key associated with an **uncompressed** public key is 51 characters, starting with the character '5' on mainnet, or starting with the character '9' on testnet.
```javascript
// Generate a new random private key.
> var privKey = new bsv.PrivKey().fromRandom()

// Display the private key as string 
> console.log(privKey.toString())
// Displays something like
// KzBrS3k3ATzvcr9fK1LvyPNEZCjQK23QdCm3Ga1sd1X75Bj5bWty

// Display the private key in WIF format.
> console.log(privKey.toWif())
// Displays something like
// KzBrS3k3ATzvcr9fK1LvyPNEZCjQK23QdCm3Ga1sd1X75Bj5bWty
// (Same as above)

// A private key is a big number. Display it.
> console.log(privKey.bn.toString())
// Displays something like
// 40055229915579433279300902866918904869584285960742602042827660466286808067956

// Check whether this private key is compressed.
> privKey.compressed
// Displays: 
// true

// Create a key and address from text.
> var mytext = 'And what rough beast, its hour come round at last, Slouches towards Bethlehem to be born?'
> var value = Buffer.from(mytext);
> var hash = bsv.Hash.sha256(value);
> var bn = bsv.Bn.fromBuffer(hash);
> bn
//Displays: 
// <BN: eca04c98c6a610f5c7778c735aa63d104c634e99afbfb3769958dcdf9d9c0894>
> var pKey = new bsv.PrivKey().fromBn(bn);
> pKey.toString()
//Displays:
//'L59gTvGvQ8ivHn2dQXjib673ChymLDf1F2MuhtryYrqRwksHKc1Z'

// Display private key
> pKey
//Displays : 
// PrivKey {
//  Constants: { versionByteNum: 128 },
//  bn: <BN: eca04c98c6a610f5c7778c735aa63d104c634e99afbfb3769958dcdf9d9c0894>
// }
//Private key for testnet
> let testPrivKey = bsv.PrivKey.Testnet.fromRandom()
> testPrivKey.toString()
//Displays: 
// 'cRkksmwJ14EYcp7LdCxRNarpNRNamdXfxNmSksCMTq3TuDeSTJhH'
// Note: This is a testnet private key, so starts 'c'. Those on mainnet start with 'K or 'L'.
```
- **Public Keys**
- As discussed on the notes about Elliptical Curves, **G** is the base point of the curve used by Bitcoin. Since G is a point, G has an X component and a Y component.
- **p** is a private key, derived from a 256-bit big number, and is a scalar.
- **P**, a public key, is derived by the equation P = pG, so P also has an X component and a Y component, and can be expressed as 
- **P (Px, Py)**
- A useful property of elliptic curves is that, if we know 
    - the X coordinate, and 
    - whether the Y coordinate is even or odd,  
- we can derive the Y coordinate. This means we can express a public key as a pair, being the X coordinate and a bit indicating whether the Y coordinate is odd.
```javascript
//Creating a private key, from which a corresponding public key will be generated.
> var privKey = new bsv.PrivKey().fromRandom()
> privKey.toString()
// Displays something like: 
// 'L1HTwvv8avFzmTBDFMAdBeDpYXKHRizsCDTidxbLcmoaY36Y43q9'

> var pubKey = new bsv.PubKey().fromPrivKey(privKey)
> pubKey.toString()
// Displays: 
// '03a11afbca5ba02c0957b8fc830138d7364f2da1b3f7d785836720d4f106140652'

//tO generate a new random public key
> var pKey = new bsv.PubKey().fromPrivKey(new bsv.PrivKey().fromRandom())
> pKey.toString()
// Displays something like:
// '027ec48651b1ac4282f61b2da83e1d611e1704c7b18c08bb622e8b95d148687bbc'

> pubKey.point.getX().toString()
// Displays: '72870044439481707204664607384809508122868715494001835488305061363948694275666'
> pubKey.point.getY().toString()
// Displays: '104103941085680017028796355295089603056264441254891006422434611359634751297017'

// Checking by using the equation P = p G
> var G = bsv.Point.getG()
> var pubCalc = G.mul(privKey.bn).toString()
> pubCalc.toString()
// Displays: 
//'{"x":"72870044439481707204664607384809508122868715494001835488305061363948694275666",
//  "y":"104103941085680017028796355295089603056264441254891006422434611359634751297017"}'
// Note that these X and Y match those retrieved individually above.

// Length of pubKey
pubKey.toBuffer().length
// Displays : 33 
// (32 is the length of pubKey, and one additional byte encodes whether compressed.
```

- Compressed private keys are represented by their X coordinate value plus one more byte encoding whether the Y coordinate is odd or even. Public keys are compressed by default.
```javascript
pubKey.toHex()
// Displays: 
// '03a11afbca5ba02c0957b8fc830138d7364f2da1b3f7d785836720d4f106140652'

pubKey.compressed
// Displays : true

// CreatE *uncompressed* public key
> var pubk = new bsv.PubKey(pubKey.point, false)
> pubk.toHex()
// Displays:
//'04a11afbca5ba02c0957b8fc830138d7364f2da1b3f7d785836720d4f106140652e628be19dba957bebd5972d34887e36a906b1720c4e8c2054992d1cac4a515f9'
pubk.toBuffer().length
// Displays: 65
```
- **Bitcoin Address**
- As can be seen, an uncompressed public key is a concatenation of an X coordinate (32 bytes), a Y coordinate (32 bytes) and a single byte indicating whether it is compressed, for a total size of 65 bytes.
- **IMPORTANT: In Bitcoin, even public keys are not typically shared; rather, an object called a Bitcoin Address, derived in turn from the public key, is shared.**
- A bitcoin address is the RIPEMD160 hash of the SHA256 hash of the public key. In raw buffer form, these addresses are 20 bytes. However, in practice they are normally encoded in Base 58 Check format.
- A manual, stepwise process to create a Bitcoin Address is documented on the Bitcoin Wiki, https://wiki.bitcoinsv.io/index.php/Technical_background_of_Bitcoin_addresses
- Double hashing is used to allow for future scalability.
```javascript
// (Continuing from the code above). 
// Note: all mainnet address start with 1, as a consequence of Base58check encoding.
// Generate a Bitcoin Address.
var address = bsv.Address.fromPubKey(pubKey)

address.toString()
// Displays:
//'1DRQSXJ2XszL6uGXbhX8LPv87BjiPuhDeH'

// An address generated from the private key will be the same address
> var address1 = bsv.Address.fromPrivKey(privKey)
> address1.toString()
// Displays: '1DRQSXJ2XszL6uGXbhX8LPv87BjiPuhDeH'

// If the uncompressed public key is used to generate an address, a different address will be generated.
> var address2 = bsv.Address.fromPubKey(pubk) 
> address2.toString()
// Displays : '13dwxm9znVyN2Sz5RAE1yjtV5P8fMcLpUE'

//Generate a testnet address.
> let testPrivKey1 = bsv.PrivKey.Testnet.fromRandom()
> testPrivKey1.toString()
//Displays : 'cUPo14RtUHHoZ5GUp4ghdYz5yE6rGVvagaBkrUuA9u2c6qx1tSVc'

> var testAddress = bsv.Address.Testnet.fromPrivKey(privKey)
> testAddress.toString()
//Displays something like: 
// 'mswMjaP1LuRat1k9KGVWAK8SyBLRJuxacw'
```
- A Base 58 Check is used to validate an address, to protect against human error, such as in copying and pasting.
- Given one private key, the address derived from a compressed public key will differ from the address derived an uncompressed public key. (Uncompressed public keys are rarely used.)
- Bitcoin addresses are being used less frequently, with the reintroduction of peer-to-peer (P2P) payments and BIP270. With these, a payor requests a payment destination from the recipient directly, for each separate payment. This avoids the reuse of a public key, to protect privacy.
- This is analogous to being able to use a URL like example.com rather than an IP address like 123.11.22.33.
- ## Seed Phrases and Mnemonics
- BIP39 provides for mnemonics and seed phrases, to help less technical users easily store and use private keys.
- A type of wallet, the **hierarchical deterministic (HD) wallet** can use a mnemonic seed (typically a strictly ordered list of 12 words) to generate many private keys and corresponding public keys from a single secret value. The seed value, or master extended key, consists of a 256-bit private key and a 256-bit  **chain code** (so totalling 512 bits). The bsv library provides a class supporting this functionality.
