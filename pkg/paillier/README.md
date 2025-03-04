<!-- Code generated by gomarkdoc. DO NOT EDIT -->

# paillier

```go
import "github.com/coinbase/kryptology/pkg/paillier"
```

Package paillier contains Paillier's cryptosystem \(1999\) \[P99\]\. Public\-Key Cryptosystems Based on Composite Degree Residuosity Class\. http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.112.4035&rep=rep1&type=pdf All routines here from pseudocode §2\.5\. Fig 1: The Paillier Cryptosystem\.

This module provides APIs for:

\- generating a safe keypair\, \- encryption and decryption\, \- adding two encrypted values\, Enc\(a\) and Enc\(b\)\, and obtaining Enc\(a \+ b\)\, and \- multiplying a plain value\, a\, and an encrypted value Enc\(b\)\, and obtaining Enc\(a \* b\)\.

The encrypted values are represented as big\.Int and are serializable\. This module also provides JSON serialization for the PublicKey and the SecretKey\.

<details><summary>Example (Encrypt Decrypt)</summary>
<p>

```go
{

	if testing.Short() {

		fmt.Println("Succeeded in encrypting and decrypting the input message: Hello World!")
		return
	}
	hexMessage := hex.EncodeToString([]byte("Hello World!"))
	mappedMessage, ok := new(big.Int).SetString(hexMessage, 16)
	if !ok {
		panic("Error mapping message to scalar point.")
	}
	pub, sec, err := NewKeys()
	if err != nil {
		log.Fatalf("Error in generating keypair: %v", err)
	}

	cipher, _, err := pub.Encrypt(mappedMessage)
	if err != nil {
		log.Fatalf("Error in Encrypting the message: %v", err)
	}

	decrypted, err := sec.Decrypt(cipher)
	if err != nil {
		log.Fatalf("Error in Decrypting the ciphertext: %v", err)
	}

	decoded := string(decrypted.Bytes())
	fmt.Println("Succeeded in encrypting and decrypting the input message:", decoded)

}
```

#### Output

```
Succeeded in encrypting and decrypting the input message: Hello World!
```

</p>
</details>

<details><summary>Example (Homomorphic Addition)</summary>
<p>

```go
{

	if testing.Short() {

		fmt.Println("Encrypting 123 and 456 separately.")
		fmt.Println("Adding their encrypted versions together.")
		fmt.Println("Succeeded in decrypting 579")
		return
	}
	pub, sec, err := NewKeys()
	if err != nil {
		log.Fatalf("Error in generating keypair: %v", err)
	}

	msg1 := tt.B10("123")
	msg2 := tt.B10("456")
	fmt.Printf("Encrypting %s and %s separately.\n", msg1, msg2)

	cipher1, _, err := pub.Encrypt(msg1)
	if err != nil {
		log.Fatalf("Error in Encrypting the message: %v", err)
	}
	cipher2, _, err := pub.Encrypt(msg2)
	if err != nil {
		log.Fatalf("Error in Encrypting the message: %v", err)
	}

	fmt.Println("Adding their encrypted versions together.")
	cipher3, err := pub.Add(cipher1, cipher2)
	if err != nil {
		log.Fatalf("Error in adding the two ciphertexts: %v", err)
	}
	decrypted3, err := sec.Decrypt(cipher3)
	if err != nil {
		log.Fatalf("Error in Decrypting the ciphertext: %v", err)
	}
	fmt.Println("Succeeded in decrypting", decrypted3)

}
```

#### Output

```
Encrypting 123 and 456 separately.
Adding their encrypted versions together.
Succeeded in decrypting 579
```

</p>
</details>

<details><summary>Example (Homomorphic Multiplication)</summary>
<p>

```go
{

	if testing.Short() {

		fmt.Println("Encrypting 10.")
		fmt.Println("Multiplying plain 5 with the encrypted 10.")
		fmt.Println("Succeeded in decrypting 50.")
		return
	}
	pub, sec, err := NewKeys()
	if err != nil {
		log.Fatalf("Error in generating keypair: %v", err)
	}

	msg1 := tt.B10("10")
	msg2 := tt.B10("5")
	fmt.Printf("Encrypting %s.\n", msg1)

	cipher1, _, err := pub.Encrypt(msg1)
	if err != nil {
		log.Fatalf("Error in Encrypting the message: %v", err)
	}

	fmt.Printf("Multiplying plain %s with the encrypted %s.\n", msg2, msg1)
	cipher3, err := pub.Mul(msg2, cipher1)
	if err != nil {
		log.Fatalf("Error in adding the two ciphertexts: %v", err)
	}
	decrypted3, err := sec.Decrypt(cipher3)
	if err != nil {
		log.Fatalf("Error in Decrypting the ciphertext: %v", err)
	}
	fmt.Printf("Succeeded in decrypting %s.\n", decrypted3)

}
```

#### Output

```
Encrypting 10.
Multiplying plain 5 with the encrypted 10.
Succeeded in decrypting 50.
```

</p>
</details>

## Index

- [Constants](<#constants>)
- [func NewKeys() (*PublicKey, *SecretKey, error)](<#func-newkeys>)
- [type Ciphertext](<#type-ciphertext>)
- [type PsfProof](<#type-psfproof>)
  - [func (p PsfProof) Verify(psf *PsfVerifyParams) error](<#func-psfproof-verify>)
- [type PsfProofParams](<#type-psfproofparams>)
  - [func (p *PsfProofParams) Prove() (PsfProof, error)](<#func-psfproofparams-prove>)
- [type PsfVerifyParams](<#type-psfverifyparams>)
- [type PublicKey](<#type-publickey>)
  - [func NewPubkey(n *big.Int) *PublicKey](<#func-newpubkey>)
  - [func (pk *PublicKey) Add(c, d Ciphertext) (Ciphertext, error)](<#func-publickey-add>)
  - [func (pk *PublicKey) Encrypt(msg *big.Int) (Ciphertext, *big.Int, error)](<#func-publickey-encrypt>)
  - [func (pk PublicKey) MarshalJSON() ([]byte, error)](<#func-publickey-marshaljson>)
  - [func (pk *PublicKey) Mul(a *big.Int, c Ciphertext) (Ciphertext, error)](<#func-publickey-mul>)
  - [func (pk *PublicKey) UnmarshalJSON(bytes []byte) error](<#func-publickey-unmarshaljson>)
- [type PublicKeyJson](<#type-publickeyjson>)
- [type SecretKey](<#type-secretkey>)
  - [func NewSecretKey(p, q *big.Int) (*SecretKey, error)](<#func-newsecretkey>)
  - [func (sk *SecretKey) Decrypt(c Ciphertext) (*big.Int, error)](<#func-secretkey-decrypt>)
  - [func (sk SecretKey) MarshalJSON() ([]byte, error)](<#func-secretkey-marshaljson>)
  - [func (sk *SecretKey) UnmarshalJSON(bytes []byte) error](<#func-secretkey-unmarshaljson>)
- [type SecretKeyJson](<#type-secretkeyjson>)


## Constants

PaillierPrimeBits is the number of bits used to generate Paillier Safe Primes\.

```go
const PaillierPrimeBits = 1024
```

\[spec\] 10\.2 and ProvePSF\, VerifyPSF fig\.15

```go
const PsfProofLength = 13
```

## func [NewKeys](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L73>)

```go
func NewKeys() (*PublicKey, *SecretKey, error)
```

NewKeys generates Paillier keys with \`bits\` sized safe primes\.

## type [Ciphertext](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L65>)

Ciphertext in Pailler's cryptosystem: a value $c \\in Z\_\{N²\}$ \.

```go
type Ciphertext *big.Int
```

## type [PsfProof](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/psf.go#L40>)

PsfProof is a slice of 13 big\.Int's that prove that a Paillier modulus is square\-free

```go
type PsfProof []*big.Int
```

### func \(PsfProof\) [Verify](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/psf.go#L97>)

```go
func (p PsfProof) Verify(psf *PsfVerifyParams) error
```

Verify that a Paillier modulus is square\-free \[spec\] §10\.fig 15

## type [PsfProofParams](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/psf.go#L24-L29>)

PsfProofParams contains the inputs to ProvePSF

```go
type PsfProofParams struct {
    Curve     elliptic.Curve
    SecretKey *SecretKey
    Pi        uint32
    Y         *curves.EcPoint
}
```

### func \(\*PsfProofParams\) [Prove](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/psf.go#L44>)

```go
func (p *PsfProofParams) Prove() (PsfProof, error)
```

Prove that a Paillier modulus is square\-free \[spec\] §10\.fig 15

## type [PsfVerifyParams](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/psf.go#L32-L37>)

PsfVerifyParams contains the inputs to VerifyPSF

```go
type PsfVerifyParams struct {
    Curve     elliptic.Curve
    PublicKey *PublicKey
    Pi        uint32
    Y         *curves.EcPoint
}
```

## type [PublicKey](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L37-L40>)

PublicKey is a Paillier public key: N = P\*Q; for safe primes P\,Q\.

```go
type PublicKey struct {
    N   *big.Int // N = PQ
    N2  *big.Int // N² computed and cached to prevent re-computation.
}
```

### func [NewPubkey](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L209>)

```go
func NewPubkey(n *big.Int) *PublicKey
```

NewPubkey initializes a Paillier public key with a given n\.

### func \(\*PublicKey\) [Add](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L218>)

```go
func (pk *PublicKey) Add(c, d Ciphertext) (Ciphertext, error)
```

Add combines two Paillier ciphertexts\.

### func \(\*PublicKey\) [Encrypt](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L272>)

```go
func (pk *PublicKey) Encrypt(msg *big.Int) (Ciphertext, *big.Int, error)
```

Encrypt produces a ciphertext on input message\.

### func \(PublicKey\) [MarshalJSON](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L148>)

```go
func (pk PublicKey) MarshalJSON() ([]byte, error)
```

MarshalJSON converts the public key into json format\.

### func \(\*PublicKey\) [Mul](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L246>)

```go
func (pk *PublicKey) Mul(a *big.Int, c Ciphertext) (Ciphertext, error)
```

Mul is equivalent to adding two Paillier exponents\.

### func \(\*PublicKey\) [UnmarshalJSON](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L154>)

```go
func (pk *PublicKey) UnmarshalJSON(bytes []byte) error
```

UnmarshalJSON converts the json data into this public key\.

## type [PublicKeyJson](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L45-L47>)

PublicKeyJson encapsulates the data that is serialized to JSON\. It is used internally and not for external use\. Public so other pieces can use for serialization\.

```go
type PublicKeyJson struct {
    N *big.Int
}
```

## type [SecretKey](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L50-L55>)

SecretKey is a Paillier secret key\.

```go
type SecretKey struct {
    PublicKey
    Lambda  *big.Int // lcm(P - 1, Q - 1)
    Totient *big.Int // Euler's totient: (P - 1) * (Q - 1)
    U       *big.Int // L((N + 1)^λ(N) mod N²)−1 mod N
}
```

### func [NewSecretKey](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L112>)

```go
func NewSecretKey(p, q *big.Int) (*SecretKey, error)
```

NewSecretKey computes intermediate values based on safe primes p\, q\.

### func \(\*SecretKey\) [Decrypt](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L320>)

```go
func (sk *SecretKey) Decrypt(c Ciphertext) (*big.Int, error)
```

Decrypt is the reverse operation of Encrypt\.

### func \(SecretKey\) [MarshalJSON](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L350>)

```go
func (sk SecretKey) MarshalJSON() ([]byte, error)
```

MarshalJSON converts the secret key into json format\.

### func \(\*SecretKey\) [UnmarshalJSON](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L361>)

```go
func (sk *SecretKey) UnmarshalJSON(bytes []byte) error
```

UnmarshalJSON converts the json data into this secret key\.

## type [SecretKeyJson](<https://github.com/coinbase/kryptology/blob/master/pkg/paillier/paillier.go#L60-L62>)

SecretKeyJson encapsulates the data that is serialized to JSON\. It is used internally and not for external use\. Public so other pieces can use for serialization\.

```go
type SecretKeyJson struct {
    N, Lambda, Totient, U *big.Int
}
```



Generated by [gomarkdoc](<https://github.com/princjef/gomarkdoc>)
