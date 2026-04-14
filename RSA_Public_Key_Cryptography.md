# Practical No. 4
## Aim: Implementation of Public-Key Cryptography (PKI) Using RSA in Python

---

## What is RSA?
RSA (Rivest–Shamir–Adleman) is an asymmetric public-key cryptography algorithm. It uses two keys:
- **Public Key**: Used to **encrypt** data. Can be shared with anyone.
- **Private Key**: Used to **decrypt** data. Must be kept secret.

---

## Step 1: Set Up Environment (Google Colab or local Python)

### If using Google Colab:
1. Go to https://colab.research.google.com
2. Create a new notebook.
3. In the first cell, install the required library:

```python
!pip install pycryptodome
```

### If using local Python:
Open terminal/command prompt and run:
```bash
pip install pycryptodome
```

Expected output:
```
Collecting pycryptodome
  Downloading pycryptodome-3.23.0-...
Successfully installed pycryptodome-3.23.0
```

---

## Step 2: Write the RSA Encryption/Decryption Code

```python
from Crypto.PublicKey import RSA
from Crypto import Random
from Crypto.Cipher import PKCS1_v1_5
import binascii

# Random number generator
random_generator = Random.new().read

# Generate RSA Key Pair (2048-bit key)
key = RSA.generate(2048, random_generator)

# Display the Public Key
print("PUBLIC KEY:")
print(key.publickey().export_key())

# Display the Private Key
print("\nPRIVATE KEY:")
print(key.export_key())

# Message to Encrypt (must be in bytes)
message = b"Lavesh Burla"
print("\nORIGINAL MESSAGE:", message)

# Encryption using the Public Key
cipher_encrypt = PKCS1_v1_5.new(key.publickey())
encrypted_message = cipher_encrypt.encrypt(message)
print("\nENCRYPTED MESSAGE:", binascii.hexlify(encrypted_message))

# Decryption using the Private Key
cipher_decrypt = PKCS1_v1_5.new(key)
decrypted_message = cipher_decrypt.decrypt(encrypted_message, None)
print("\nDECRYPTED MESSAGE:", decrypted_message)
```

---

## Step 3: Run the Code and Observe Output

### Expected Output:
```
PUBLIC KEY:
b'-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA...\n-----END PUBLIC KEY-----'

PRIVATE KEY:
b'-----BEGIN RSA PRIVATE KEY-----\nMIIEowIBAAKCAQEA...\n-----END RSA PRIVATE KEY-----'

ORIGINAL MESSAGE: b'Lavesh Burla'

ENCRYPTED MESSAGE: b'53822cd7358b83f3ca6d0e360b0221c7906c36c5ccb4250238f8afabde31ae7ec...'

DECRYPTED MESSAGE: b'Lavesh Burla'
```

---

## Code Explanation

| Part | Description |
|---|---|
| `RSA.generate(2048, random_generator)` | Generates a 2048-bit RSA key pair (public + private) |
| `key.publickey().export_key()` | Exports the public key in PEM format |
| `key.export_key()` | Exports the private key in PEM format |
| `PKCS1_v1_5.new(key.publickey())` | Creates an encryption cipher using the public key |
| `cipher_encrypt.encrypt(message)` | Encrypts the message using the public key |
| `binascii.hexlify(encrypted_message)` | Converts encrypted bytes to hexadecimal string for display |
| `PKCS1_v1_5.new(key)` | Creates a decryption cipher using the private key |
| `cipher_decrypt.decrypt(encrypted_message, None)` | Decrypts the message using the private key |

---

## How RSA Works (Theory)

1. **Key Generation**:
   - Choose two large prime numbers p and q.
   - Compute n = p × q (modulus).
   - Compute φ(n) = (p-1)(q-1).
   - Choose public exponent e (commonly 65537).
   - Compute private exponent d such that e × d ≡ 1 (mod φ(n)).
   - **Public Key** = (e, n), **Private Key** = (d, n).

2. **Encryption**: C = M^e mod n (uses public key)

3. **Decryption**: M = C^d mod n (uses private key)

---

## Key Points for Exam
- RSA is an **asymmetric** encryption algorithm (two different keys).
- **Public key** encrypts; **private key** decrypts.
- Key size: 2048-bit provides strong security.
- `pycryptodome` library is used in Python for RSA implementation.
- `PKCS1_v1_5` is the padding scheme used for encryption/decryption.
- The encrypted message is different every time (due to random padding), but decryption always gives the original message.
- Security of RSA is based on the difficulty of factoring large numbers.
