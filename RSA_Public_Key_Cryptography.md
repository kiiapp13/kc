## Step 1: Set Up Environment (Google Colab or local Python)

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
message = b"kinjal"
print("\nORIGINAL MESSAGE:", message)

# Encryption using the Public Key
cipher_encrypt = PKCS1_v1_5.new(key.publickey())
encrypted_message = cipher_encrypt.encrypt(message)
print("\nENCRYPTED MESSAGE:", binascii.hexlify(encrypted_message))

# Decryption using the Private Key
cipher_decrypt = PKCS1_v1_5.new(key)
decrypted_message = cipher_decrypt.decrypt(encrypted_message, None)
print("\nDECRYPTED MESSAGE:", decrypted_message)

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

