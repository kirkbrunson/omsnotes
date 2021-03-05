
## RA1 - Modular Arithmetic
- Background for RSA Cryptosystem: 
	- go-over math background (modular arithmetic, inverses, and Euclid's GCD algorithm), 
	- then Fermat's little theorem (leads to RSA algorithm), 
	- finally primality testing to generate random primes
- Huge integers: consider n-bit itegers for n=1024 or n=2048
- Modular Arithmatic
	- x mod 2 = least significant bit of x, which tells us if it's odd or even
	- Notation: let `x===y mod N` mean they have the same modulo result
	- mod N has N equivalence classes
	- Formal notation (handles negative numbers): `x mod n = r` if `x = qN + r` for integers q (quotient), r (remainder)
	- Basic Fact: if `x===y mod N` and `a===b mod N`, then `(x+a)===(y+b) mod N` AND `x*a === y*b mod N`!
- Modular Exponentiation: computes `x^y mod N` in polynomial time (O(n^2) where n is huge and x, y, N are n-bit numbers)
- Multiplicative Inverses (for modular arithmatic)
	- Theorem: inverse exists if it shares no factors with N. Formally phrased: `x^-1 mod N` exists iff `gcd(x, N)=1` (equiv to saying x and N are relatively prime)
	- Multiplicative inverses are unique! (equivalent classes)
- Euclid's Algorithm: used to find the GCD between two numbers (O(n^3))
	- Euclid's Rule: for integers x,y where x>=y>0, `gcd(x,y) = gcd(x mod y, y)`. True because `gcd(x,y)=gcd(x-y,y)`
- - Extended Euclid's Algorithm: can be used to find multiplicative inverses (O(n^3))

## RA2 - RSA
- Fermat's little theorem: if p is prime, then for every 1<=z<=p-1 in set S, `z^(p-1) === 1 mod p`
- Euler's theorem (generalized Fermat's): for any N, z where gcd(z,N) = 1, then `z^(phi(N))=== 1 mod N`
	- phi(N) = # of integers between 1 and N which are relatively prime to N. Also called Euler's toitent function
	- So in case of prime number p, it reduces to Fermat's little theorem.
	- phi(p * q)= pq - p - q + 1 = (p-1)(q-1) for two prime numbers p, q
- Euler's theorem for N=pq
	- for any z where gcd(z,N)=1, then: `z^((p-1)(q-1)) === 1 mod pq`. This is foundation of RSA!
- RSA idea: encrypt + decrypt a message using large prime numbers
	- Public key: N, e. Send message y as `y === m^e mod N`
	- Private key: d. Decrypt message m from encrypted y as `y^d mod N = m`, and `d===e^(-1) mod (p-1)(q-1)` where p, q aren't public
- RSA Protocol
	```text
	Receiver does:
		- Pick 2 n-bit random primes p and q
		- Pick int e which is relatively prime to (p-1)(q-1). Try small e first, e.g. e=3,5,7,11... (easier for encrypter to send message over). Get gcd using Euclid's algorithm
		- Publish public key (N, e)
		- Compute private key d === e^-1 mod(p-1)(q-1) using ext-Euclid algorithm
	Sender does:
		- Lookup receiver's public key
		- Compute y===m^e mod N (use fast modular exponentiation algorithm)
		- Sends message y
	After receiving message, Receiver does:
		- Decrypt by computing y^d mod N = m (again use fast modular exponentiation algorithm)
	```
- RSA Pitfalls
	- If gcd(m,N)>1 (i.e. message isn't relatively prime to N), the message can be decrypted using the public key (details in lecture).
		- Safeguard: sender checks the gcd before sending
	- m must not be too large, i.e. m < N must hold, otherwise computationally infeasible.
	- m must not be too small, if m^e < N then mod N isn't doing anything.
		- Safeguard: send padded message with arbitrary string r (added or XOR-ed) to guarantee size. Keep track of arbitrary string used and just remove it after decrypting message
	- If same m is sent to multiple receivers e times, and all messages are intercepted, it's possible to decrypt the original message because Chinese Remainder Theorem. Not good!
- Assuming no pitfalls, breaking RSA is as hard as factoring N to get pq, and thus (p-1)(q-1), and thus d
- Primality Testing
	- Picking random primes p and q: sample a bunch of random numbers until reaching primes
		- P(r is prime) is about 1/n for an n-bit number
		- Check for primality by finding z where `z^(r-1) !=== 1 mod r`, "Fermat witness"
	- Fermat Witness: every composite has one. All except pseudo-prime numbers have a trivial fermat witness

## RA3 - Bloom Filters (Hashing)
- Balls into bins example problem
- Use Bloom Filters for space-efficient probabilistic storage. No guarantees on accuracy but mostly correct. Error rate can be adjusted according w/ tradeoff on size+computations
