# codec_technologies
Cybersecurity internship projects

# 1. Password Hashing and Cracking Project

## Overview
This project demonstrates password hashing, salting, verification, dictionary attack, brute-force attack, and a salting demo using Python.

It is designed for learning how password security works and why weak passwords and fast hash algorithms can be risky.

## Features
- Generate random cryptographic salts.
- Hash passwords using MD5, SHA-1, SHA-256, and SHA-512.
- Verify a password against a stored hash.
- Perform dictionary attacks using an in-memory wordlist.
- Perform brute-force attacks with configurable charset and length.
- Demonstrate why salting makes attacks harder.

## Technologies Used
- Python 3
- hashlib
- itertools
- string
- secrets
- time

## How It Works
The program first hashes a password using the selected algorithm and optional salt.

For verification, it recomputes the hash using the same algorithm and salt and compares it with the target hash.

For attacks, it tries words from a built-in wordlist or generates all character combinations up to a chosen length.

## File Structure
```text
password-project/
├── password_cracker.py
└── README.md
```

## How to Run
1. Save the code in a file named `password_cracker.py`.
2. Open a terminal in the project folder.
3. Run:
```bash
python password_cracker.py
```

## Menu Options
- Hash a password.
- Verify a password against a hash.
- Run a dictionary attack.
- Run a brute-force attack.
- View a salting demo.

## Important Notes
- Brute-force attacks grow very quickly as length increases.
- Keep the maximum length small, preferably 4 or 5 characters.
- Salting protects against precomputed attacks, but strong password hashing algorithms are still recommended for real systems.

## Sample Use
Example:
- Password: `monkey`
- Algorithm: `sha256`
- Salt: random generated salt

The program will print the hash and the salt, which must be saved together for later verification.

## Educational Value
This project helps explain:
- Password storage best practices.
- Why hashing is not the same as encryption.
- Why salts are useful.
- How attackers test weak passwords.

## Limitations
- This is a learning project, not a secure production password manager.
- MD5 and SHA-1 are included only for demonstration.
- Real applications should use bcrypt, scrypt, or Argon2.

## Output
The program displays:
- Generated hash
- Salt value
- Match / no match result
- Number of attempts
- Time taken for attacks

# 2. Cryptography Algorithms Project

## Overview
This project demonstrates core cryptography concepts using AES, RSA, and SHA-256 in Python.

It includes encryption, decryption, key generation, and hashing in a simple menu-driven program.

## Features
- AES encryption and decryption in CBC mode.
- RSA key generation with 2048-bit keys.
- RSA encryption and decryption using OAEP.
- SHA-256 hashing.
- Interactive menu-based interface.

## Technologies Used
- Python 3
- PyCryptodome
- base64

## How It Works
### AES
AES is used as a symmetric cipher, meaning the same key is used to encrypt and decrypt data.

The program generates a random AES-128 key and IV, encrypts the message using CBC mode, and then decrypts it back.

### RSA
RSA is used as an asymmetric cipher.

The program generates a public/private key pair, encrypts the message with the public key, and decrypts it with the private key.

### SHA-256
SHA-256 generates a fixed-length hash of the input message.

It is used for integrity checking and password hashing demonstrations.

## File Structure
```text
cryptography-project/
├── crypto_project.py
└── README.md
```

## Installation
Install the required library first:
```bash
pip install pycryptodome
```

## How to Run
1. Save the code in a file named `crypto_project.py`.
2. Open a terminal in the project folder.
3. Run:
```bash
python crypto_project.py
```

## Menu Options
- AES Encryption/Decryption
- RSA Encryption/Decryption
- SHA-256 Hashing
- Exit

## Example Workflow
### AES
1. Enter a message.
2. The program encrypts it.
3. The program prints the ciphertext, key, and IV.
4. The program decrypts the ciphertext and shows the original text.

### RSA
1. Enter a message.
2. The program generates a new RSA key pair.
3. The message is encrypted with the public key.
4. The message is decrypted with the private key.

### SHA-256
1. Enter a message.
2. The program prints the SHA-256 hash.

## Educational Value
This project helps explain:
- Symmetric vs asymmetric cryptography.
- Encryption and decryption flow.
- Key generation.
- Hashing for data integrity.

## Security Notes
- AES keys and IVs are shown only for learning purposes.
- RSA in this project uses OAEP, which is a safer padding method than raw RSA.
- SHA-256 is a hash function, not encryption.

## Limitations
- This is a demonstration project, not a full secure messaging system.
- Keys are generated fresh each run and are not stored.
- No file encryption or password-based key derivation is included.

## Output
The program displays:
- Encrypted text
- AES key and IV
- Decrypted text
- RSA encrypted and decrypted text
- SHA-256 hash value
