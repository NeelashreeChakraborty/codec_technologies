from Crypto.Cipher import AES, PKCS1_OAEP
from Crypto.PublicKey import RSA
from Crypto.Random import get_random_bytes
from Crypto.Util.Padding import pad, unpad
from Crypto.Hash import SHA256
import base64


# ---------- AES Encryption ----------
def aes_encrypt(message):
    key = get_random_bytes(16)  # AES-128 key
    cipher = AES.new(key, AES.MODE_CBC)

    ciphertext = cipher.encrypt(pad(message.encode(), AES.block_size))

    return {
        "key": base64.b64encode(key).decode(),
        "iv": base64.b64encode(cipher.iv).decode(),
        "ciphertext": base64.b64encode(ciphertext).decode()
    }


# ---------- AES Decryption ----------
def aes_decrypt(ciphertext, key, iv):
    key = base64.b64decode(key)
    iv = base64.b64decode(iv)
    ciphertext = base64.b64decode(ciphertext)

    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = unpad(cipher.decrypt(ciphertext), AES.block_size)

    return plaintext.decode()


# ---------- RSA Key Generation ----------
def generate_rsa_keys():
    key = RSA.generate(2048)

    private_key = key.export_key()
    public_key = key.publickey().export_key()

    return public_key, private_key


# ---------- RSA Encryption ----------
def rsa_encrypt(message, public_key):
    rsa_key = RSA.import_key(public_key)
    cipher = PKCS1_OAEP.new(rsa_key)

    encrypted = cipher.encrypt(message.encode())

    return base64.b64encode(encrypted).decode()


# ---------- RSA Decryption ----------
def rsa_decrypt(ciphertext, private_key):
    rsa_key = RSA.import_key(private_key)
    cipher = PKCS1_OAEP.new(rsa_key)

    decrypted = cipher.decrypt(base64.b64decode(ciphertext))

    return decrypted.decode()


# ---------- SHA-256 Hashing ----------
def sha_hash(message):
    h = SHA256.new(message.encode())
    return h.hexdigest()


# ---------- Main Program ----------
while True:
    print("\n===== Cryptography Algorithms Project =====")
    print("1. AES Encryption/Decryption")
    print("2. RSA Encryption/Decryption")
    print("3. SHA-256 Hashing")
    print("4. Exit")

    choice = input("Enter your choice: ")

    if choice == "1":
        message = input("Enter message: ")

        result = aes_encrypt(message)

        print("\nEncrypted Text:", result["ciphertext"])
        print("Key:", result["key"])
        print("IV:", result["iv"])

        decrypted = aes_decrypt(
            result["ciphertext"],
            result["key"],
            result["iv"]
        )

        print("Decrypted Text:", decrypted)

    elif choice == "2":
        message = input("Enter message: ")

        public_key, private_key = generate_rsa_keys()

        encrypted = rsa_encrypt(message, public_key)
        print("\nEncrypted Text:", encrypted)

        decrypted = rsa_decrypt(encrypted, private_key)
        print("Decrypted Text:", decrypted)

    elif choice == "3":
        message = input("Enter message: ")

        hash_value = sha_hash(message)

        print("\nSHA-256 Hash:")
        print(hash_value)

    elif choice == "4":
        print("Program terminated.")
        break

    else:
        print("Invalid choice! Please try again.")
