# Elliptic Curves
- "Elliptic Curves" is a relatively advanced mathematical subject. Needing to understand this subject in detail is unlikely to be necessary for a development project using BSV. However, a particular elliptic curve is used in **Elliptic Curve Digital Signature Algorithm** (ECDSA). In turn, ECDSA is central to creation and management of UTXO's.
- An elliptic curve is the set of all points that satisfy the conditions
- y2 = 𝑥2 + a𝑥 + 𝑏 
- and
- 4𝑎3 + 27𝑏3 ≠ 0 
- Bitcoin uses a Koblitz curve specified in  **Standards for Efficient Cryptography** standard: **secp256k1**.
- Bitcoin uses the equation 
- y2 = (𝑥3 + 7) mod p
- with values of curve parameters required by secp256k1 for a specific base point **G**.
- For this curve, G specifies a subgroup such that any point on the curve is nG, and
    - where  n and the coordinates of G are large numbers,
    - for any point P (that is, (Px, Py)  ), 
- there is some scalar p such that P = pG.
- A **private key** is some value p such that pG is on the elliptic curve, and P = pG is the **public key**.
- Diffie-Hellman key exchange uses this property. If, for Alice, her private key p = a, then her public key, A, = aG. Similarly, for Bob, p=b, and B = bG.
- Now, note that 
- aB = abG = baG = bA.
- Alice's private key times Bob's public key is the same as Bob's private key times Alice's public key.
- This allows either Alice or Bob to encrypt a message using their private key and the recipient's public key, and for the recipient to decrypt the encrypted message using the recipient's private key and the sender's public key.
 
```shell
// The bsv library contains functions related to creation of a G, 
> var bsv = require ('bsv') 

// Point has methods we will use.
> bsv.Point
[class K extends f]

> // store G. G is constant.
> var G = bsv.Point.getG()
undefined

> console.log(G)
// Displays two coordinates of G
// x: <BN-R: 79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798>,
// y: <BN-R: 483ada7726a3c4655da4fbfc0e1108a8fd17b448a68554199c47d08ffb10d4b8>,

// multiply G by a scalar.
> console.log(G.mul(bsv.Bn('2')))
// Displays two new coordinates.
// x: <BN-R: c6047f9441ed7d6d3045406e95c07cd85c778e4b8cef3ca7abac09b95c709ee5>,
// y: <BN-R: 1ae168fea63dc339a3c58419466ceaeef7f632653266d0e1236431a950cfe52a>

// create a scalar p
// first, generate a random number.
> var hex = bsv.Random.getRandomBuffer(32)
undefined
> var phex = bsv.Random.getRandomBuffer(32).toString('hex')
undefined
> var p = bsv.Bn.fromBuffer(Buffer.from(phex, 'hex'))
undefined // p is random.

> console.log(p)
// Displays something like:
// <BN: a9c9adca7491160f8cd817d3fd15842c55938c2523a0c8a4b6dc2afdb924ee7e>

// calculate P (a point on the curve)
> var P = G.mul(p)
undefined
> console.log(P)
// Displays something like:
//  x: <BN-R: 4d307e961bed3b29a95b7b7ae9eeb52d6c3e0d792dac259ef492cf03689a039a>,
//  y: <BN-R: 9b822fc785f131ca831f38f9ef7329a5b0ce976dcf784f58e22d1f66e62abb99>,

// P is a point in 2D, so we can find its X and Y.
> P.getX()
// Displays: <BN: 4d307e961bed3b29a95b7b7ae9eeb52d6c3e0d792dac259ef492cf03689a039a>
// This is the x component of P, and matches the output above.

> P.getY()
// Displays: <BN: 9b822fc785f131ca831f38f9ef7329a5b0ce976dcf784f58e22d1f66e62abb99>
```
