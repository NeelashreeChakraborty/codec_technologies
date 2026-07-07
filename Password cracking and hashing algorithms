import hashlib
import itertools
import string
import secrets
import time


# ============================================================
# PART 1: HASHING + SALTING UTILITIES
# ============================================================

SUPPORTED_ALGOS = {
    "md5": hashlib.md5,
    "sha1": hashlib.sha1,
    "sha256": hashlib.sha256,
    "sha512": hashlib.sha512,
}


def generate_salt(length: int = 16) -> str:
    """Generate a cryptographically random salt, returned as a hex string."""
    return secrets.token_hex(length)


def hash_password(password: str, algorithm: str = "sha256", salt: str = None):
    """
    Hash a password with the given algorithm.
    If a salt is provided, it's prepended to the password before hashing.
    Returns (hash_hex, salt).
    """
    algorithm = algorithm.lower()
    if algorithm not in SUPPORTED_ALGOS:
        raise ValueError(f"Unsupported algorithm '{algorithm}'. "
                          f"Choose from {list(SUPPORTED_ALGOS.keys())}")

    if salt is None:
        salt = generate_salt()

    hasher = SUPPORTED_ALGOS[algorithm]()
    hasher.update((salt + password).encode("utf-8"))
    return hasher.hexdigest(), salt


def verify_password(password: str, salt: str, target_hash: str, algorithm: str = "sha256") -> bool:
    """Recompute the hash for a candidate password + salt and compare."""
    candidate_hash, _ = hash_password(password, algorithm=algorithm, salt=salt)
    return candidate_hash == target_hash


# ============================================================
# PART 2: WORDLIST (in-memory, so no external file is needed)
# ============================================================

WORDLIST = [
    "123456", "password", "123456789", "12345678", "12345", "qwerty",
    "abc123", "letmein", "monkey", "111111", "iloveyou", "admin",
    "welcome", "dragon", "sunshine", "princess", "football", "password1",
    "123123", "baseball", "trustno1", "000000", "hunter2", "shadow",
    "master", "freedom", "whatever", "qazwsx", "superman", "michael",
    "ninja", "mustang", "password123", "starwars", "login", "solo",
    "passw0rd", "zxcvbn", "batman", "access", "flower", "hottie",
    "loveme", "biteme", "secret", "summer", "internet", "service",
    "canada", "hockey",
]


# ============================================================
# PART 3: CRACKING TECHNIQUES
# ============================================================

def dictionary_attack(target_hash: str, wordlist, algorithm: str = "sha256", salt: str = None):
    """
    Try every word in `wordlist` (a list of strings) against target_hash.
    Returns (password_found_or_None, attempts, elapsed_seconds).
    """
    start = time.time()
    attempts = 0

    for word in wordlist:
        word = word.strip()
        if not word:
            continue
        attempts += 1
        candidate_hash, _ = hash_password(word, algorithm=algorithm, salt=salt)
        if candidate_hash == target_hash:
            return word, attempts, time.time() - start

    return None, attempts, time.time() - start


def brute_force_attack(target_hash: str, algorithm: str = "sha256", salt: str = None,
                        charset: str = string.ascii_lowercase + string.digits,
                        max_length: int = 4):
    """
    Try every possible combination of characters from `charset`,
    from length 1 up to max_length.
    WARNING: grows as len(charset)^length — keep max_length small (<=5)
    or this can take a very long time.
    """
    start = time.time()
    attempts = 0

    for length in range(1, max_length + 1):
        for combo in itertools.product(charset, repeat=length):
            candidate = "".join(combo)
            attempts += 1
            candidate_hash, _ = hash_password(candidate, algorithm=algorithm, salt=salt)
            if candidate_hash == target_hash:
                return candidate, attempts, time.time() - start

    return None, attempts, time.time() - start


# ============================================================
# PART 4: SMALL INPUT HELPERS
# ============================================================

def ask_algorithm() -> str:
    print("Choose a hash algorithm:")
    print("  1) md5")
    print("  2) sha1")
    print("  3) sha256 (default)")
    print("  4) sha512")
    choice = input("Enter 1-4 (or press Enter for default): ").strip()
    mapping = {"1": "md5", "2": "sha1", "3": "sha256", "4": "sha512", "": "sha256"}
    return mapping.get(choice, "sha256")


def ask_yes_no(prompt: str) -> bool:
    answer = input(prompt + " (y/n): ").strip().lower()
    return answer.startswith("y")


# ============================================================
# PART 5: MENU ACTIONS (all driven by user input)
# ============================================================

def action_hash_a_password():
    print()
    print("== Hash a password ==")
    pw = input("Enter a password to hash: ")
    algo = ask_algorithm()
    use_salt = ask_yes_no("Add a random salt?")

    if use_salt:
        h, salt = hash_password(pw, algorithm=algo, salt=None)
        print()
        print(f"Algorithm : {algo}")
        print(f"Salt      : {salt}")
        print(f"Hash      : {h}")
        print("(Save the salt! You need it to verify or crack this hash later.)")
    else:
        h, _ = hash_password(pw, algorithm=algo, salt="")
        print()
        print(f"Algorithm : {algo}")
        print(f"Hash (no salt): {h}")


def action_verify_a_password():
    print()
    print("== Verify a password against a known hash ==")
    target_hash = input("Enter the target hash: ").strip()
    algo = ask_algorithm()
    salt = input("Enter the salt (leave blank if none): ").strip()
    pw = input("Enter the password to check: ")

    match = verify_password(pw, salt, target_hash, algorithm=algo)
    print()
    if match:
        print("MATCH -- that password produces the given hash.")
    else:
        print("NO MATCH -- that password does not produce the given hash.")


def action_dictionary_attack():
    print()
    print("== Dictionary attack ==")
    print("You can either:")
    print("  1) Type a password yourself and we'll hash it, then try to crack it")
    print("  2) Paste in a hash you already have")
    choice = input("Enter 1 or 2: ").strip()

    algo = ask_algorithm()
    salt = input("Enter the salt used, if any (leave blank if none): ").strip()

    if choice == "1":
        real_pw = input("Enter the password to hash and then crack: ")
        target_hash, salt = hash_password(real_pw, algorithm=algo, salt=(salt if salt else ""))
        print(f"\nGenerated hash to crack: {target_hash}")
    else:
        target_hash = input("Paste the target hash: ").strip()

    use_custom_wordlist = ask_yes_no("Do you want to type in your own custom word to add to the wordlist first?")
    wordlist = list(WORDLIST)
    if use_custom_wordlist:
        custom_word = input("Enter the extra word to add: ").strip()
        if custom_word:
            wordlist.append(custom_word)

    print("\nRunning dictionary attack...")
    found, attempts, elapsed = dictionary_attack(target_hash, wordlist, algorithm=algo, salt=salt)

    print()
    if found:
        print(f"CRACKED: '{found}'  (tried {attempts} words in {elapsed:.4f}s)")
    else:
        print(f"Not found in wordlist (tried {attempts} words in {elapsed:.4f}s)")


def action_brute_force_attack():
    print()
    print("== Brute-force attack ==")
    print("You can either:")
    print("  1) Type a password yourself and we'll hash it, then try to crack it")
    print("  2) Paste in a hash you already have")
    choice = input("Enter 1 or 2: ").strip()

    algo = ask_algorithm()
    salt = input("Enter the salt used, if any (leave blank if none): ").strip()

    if choice == "1":
        real_pw = input("Enter a SHORT password to hash and then crack (keep it short, e.g. 3-4 characters): ")
        target_hash, salt = hash_password(real_pw, algorithm=algo, salt=(salt if salt else ""))
        print(f"\nGenerated hash to crack: {target_hash}")
    else:
        target_hash = input("Paste the target hash: ").strip()

    print("\nCharacter set options:")
    print("  1) lowercase letters + digits (default)")
    print("  2) lowercase letters only")
    print("  3) lowercase + uppercase + digits")
    cs_choice = input("Enter 1-3 (or press Enter for default): ").strip()
    charset_map = {
        "1": string.ascii_lowercase + string.digits,
        "2": string.ascii_lowercase,
        "3": string.ascii_letters + string.digits,
        "": string.ascii_lowercase + string.digits,
    }
    charset = charset_map.get(cs_choice, string.ascii_lowercase + string.digits)

    max_len_input = input("Max password length to try (recommended 4-5, higher = much slower): ").strip()
    try:
        max_length = int(max_len_input)
    except ValueError:
        max_length = 4
    max_length = max(1, min(max_length, 6))  # safety cap so it doesn't run forever

    print(f"\nRunning brute-force attack (charset size={len(charset)}, max length={max_length})...")
    print("This may take a while for longer lengths...")
    found, attempts, elapsed = brute_force_attack(
        target_hash, algorithm=algo, salt=salt, charset=charset, max_length=max_length
    )

    print()
    if found:
        print(f"CRACKED: '{found}'  (tried {attempts} combinations in {elapsed:.4f}s)")
    else:
        print(f"Not found (tried {attempts} combinations in {elapsed:.4f}s)")


def action_salt_demo():
    print()
    print("== Why salting matters (demo) ==")
    pw = input("Enter a weak password to test (try one from the wordlist, e.g. 'monkey'): ")
    algo = ask_algorithm()

    salt = generate_salt()
    target_hash, _ = hash_password(pw, algorithm=algo, salt=salt)
    print(f"\nPassword '{pw}' hashed WITH a random salt: {salt}")
    print(f"Resulting hash: {target_hash}")

    print("\nTrying a dictionary attack WITHOUT knowing the salt...")
    found, attempts, elapsed = dictionary_attack(target_hash, WORDLIST, algorithm=algo, salt="")
    print(f"  Result: {'CRACKED: ' + found if found else 'FAILED to crack'} "
          f"(tried {attempts} words in {elapsed:.4f}s)")

    print("\nNow trying the SAME attack, but this time using the correct salt...")
    found, attempts, elapsed = dictionary_attack(target_hash, WORDLIST, algorithm=algo, salt=salt)
    print(f"  Result: {'CRACKED: ' + found if found else 'FAILED to crack'} "
          f"(tried {attempts} words in {elapsed:.4f}s)")

    print("\nTakeaway: salts stop attackers from using precomputed rainbow")
    print("tables across many accounts, but they don't stop a fast per-hash")
    print("attack once the salt is known. Real protection comes from SLOW")
    print("hash functions designed for passwords (bcrypt, scrypt, argon2)")
    print("instead of fast general-purpose hashes like MD5/SHA-256.")


# ============================================================
# PART 6: MAIN MENU LOOP
# ============================================================

def print_menu():
    print()
    print("=" * 60)
    print("PASSWORD CRACKING AND HASHING ALGORITHMS")
    print("=" * 60)
    print("1) Hash a password (with or without salt)")
    print("2) Verify a password against a known hash")
    print("3) Dictionary attack")
    print("4) Brute-force attack")
    print("5) Salting demo (why salts matter)")
    print("6) Exit")
    print("=" * 60)


def main():
    while True:
        print_menu()
        choice = input("Choose an option (1-6): ").strip()

        if choice == "1":
            action_hash_a_password()
        elif choice == "2":
            action_verify_a_password()
        elif choice == "3":
            action_dictionary_attack()
        elif choice == "4":
            action_brute_force_attack()
        elif choice == "5":
            action_salt_demo()
        elif choice == "6":
            print("Goodbye!")
            break
        else:
            print("Invalid choice, please enter a number from 1 to 6.")


if __name__ == "__main__":
    main()
