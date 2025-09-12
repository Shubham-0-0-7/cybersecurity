## Statement  
Your company’s network administrator forgot his administration passwords. He does however have a backup of his startup-config file. Use it to recover his passwords!
The flag is the concatenation of the "enable" and "administrator" passwords.  
Good luck!

## Theory (little bit)
### since i dont know much 

#### MD5
MD5 (Message-Digest Algorithm 5) is a cryptographic hash function that takes any size input and produces a fixed-size 128-bit "fingerprint" called an MD5 hash. It was designed to verify data integrity and detect modifications by providing a unique digital fingerprint for data, but it is now considered insecure for cryptographic purposes due to known vulnerabilities. It is still used as a checksum for non-cryptographic functions, like confirming a file hasn't been altered  

# Understanding Salt and md5crypt (Cisco Type-5)

---

## 1 What salt does

**Salt** is a small piece of extra data (like `"mERr"` in your hashes). It’s added to the password **before hashing**.

**Why use salt?**  

1. **Uniqueness**: If two users have the same password, the hashes look different because each has a different salt.  
2. **Protection from precomputed attacks**: Attackers cannot just use a “rainbow table” of common hashes; they have to recompute hashes for each salt individually.

**How it’s used in md5crypt**:

- Suppose password = `"admin"`, salt = `"mERr"`  
- md5crypt will mix `"admin"` + `"mERr"` in a special way to produce the final hash.

You can think of salt as a “modifier” that changes the hash result while keeping the password secret.

---

## 2 How md5crypt works (high-level)

md5crypt is **not just plain MD5**; it’s a scheme built on MD5 to make hashing a password with a salt more secure.

**Steps:**

1. **Take password + salt**  
   - Example: password = `"admin"`, salt = `"mERr"`  
   - md5crypt prepends, appends, and mixes these in a defined way.

2. **Run MD5 multiple times (iterations)**  
   - md5crypt does **1000 iterations** of MD5 internally.  
   - Iterations make the hash slower to compute → harder for brute-force attacks.  
   - Each iteration uses combinations of password, salt, and previous hash bytes.

3. **Rearrange and encode the output**  
   - md5crypt produces a 128-bit MD5 digest.  
   - Then it encodes it using a **modified Base64** into the final 22-character hash.  
   - Final format: `$1$<salt>$<encoded-hash>`

Example hash:
$1$mERr$A419.HL58lq743wXS4kSM1

- `$1$` → md5crypt  
- `mERr` → salt  
- `A419.HL58lq743wXS4kSM1` → final MD5-based encoded hash

---

## 3 How John the Ripper uses it

For each candidate password in the wordlist:

1. Extract the salt from the stored hash (`mERr`).  
2. Compute `md5crypt(candidate, salt)` exactly the same way Cisco did.  
3. Compare the computed hash to the stored hash:
   - Match → password found  
   - No match → try next candidate  

**Optimization:** Since md5crypt is slow (1000 iterations), John can speed up cracking by testing small wordlists first or using rules to mutate candidates.  



1. **Inspected the router config**
    - Found two lines with Cisco hashed secrets:  
      ```
      enable secret 5 $1$mERr$A419.HL58lq743wXS4kSM1
      username administrator secret 5 $1$mERr$yhf7f2RnC74CxKANvoekD.
      ```
    - Recognized format `$1$<salt>$<hash>` → **md5crypt (Cisco type-5)** with salt `mERr`.

2. **Prepared the hashes file**
    - Created `hashes.txt` containing the two `$1$...` lines (one per line).  
    - This is what password crackers (John/hashcat) expect.

3. **Chose a cracking approach** (human reasoning)
    - md5crypt is one-way but crackable by guessing candidate passwords and hashing them with the same salt.  
    - Decided to use **John the Ripper** with wordlists (fast and practical for CTFs).

4. **Ran John with a wordlist**
    - First attempted with a small list `ch53.txt`:  
      ```bash
      john --wordlist=ch53.txt --format=md5crypt hashes.txt
      ```  
      → nothing cracked.
    - Fixed a path error earlier (`/path/to/...` was a placeholder).

5. **Got a better wordlist (rockyou) / or used another list**
    - Downloaded/used a larger wordlist (rockyou or similar) and/or tried focused guesses.  
    - Ran John with the better wordlist (and optionally `--rules`).

6. **John cracked both hashes**
    - `john --show --format=md5crypt hashes.txt` displayed:
        - `dolphinsforever`
        - `P@ssw0rd!1`

7. **Built the flag**
    - The challenge asked for the concatenation of the **enable** and **administrator** passwords (in that order).  
    - Flag = `dolphinsforeverP@ssw0rd!1`  
    - Verified with:
      ```bash
      echo -n "dolphinsforeverP@ssw0rd!1" > flag.txt
      ```
