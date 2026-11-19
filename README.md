# Networkwalks-RobinVuru-B084-WK3-PM1_PM2-Cybersecurity-PW-Cracking-with-JTRv1-Networkwalks-Tools

markdown# Password Cracking Lab — John the Ripper + NetworkWalks Tools

**Author:** Robin Vuru  
**Role:** ICT Support | System Admin — Ggas PNG LTD  
**Date:** Week of 19 Nov 2026  
**Tools:** John the Ripper (Johnny GUI), NetworkWalks Hash Calculator, NetworkWalks Password Cracker  
**Lab Type:** Authorised practical — own test PDF files only

> ⚠️ Ethics: All PDFs used in this lab were created by me for testing.
> No real user passwords, no customer files, no unauthorised systems were tested.

---

## Overview

This week I completed password cracking in two parts:

| Part | Tool | What I Did | Result |
| :--- | :--- | :--- | :--- |
| Part 1 | John the Ripper via Johnny | Extracted hashes from 3 locked PDFs and cracked them | ✅ 3 / 3 passwords recovered |
| Part 2 | NetworkWalks Hash Calculator + Password Cracker | Generated a hash from a PDF, then watched a wordlist attack recover it | ✅ Password recovered, attack process observed |

---

## Part 1: Password Cracking with John the Ripper (Johnny)

### 1.1 What is John the Ripper?

John the Ripper (JTR) is a fast, open-source password cracker. It takes a **hash** — not the password itself — and tries to find the original password by:

1.  **Dictionary Attack:** Trying every word in a wordlist (e.g. `rockyou.txt`)
2.  **Rule-based Attack:** Trying variations — `Password`, `password1`, `Password123`, etc.
3.  **Brute-force / Incremental:** Trying all combinations if the wordlist fails

**Johnny** is just the GUI (easy screen) on top of John. Same engine underneath.

### 1.2 Workflow
Locked PDF -> Extract Hash -> Save hash to .txt -> Load in Johnny
-> Select Wordlist Attack -> Start -> Password Foundjavascript
### 1.3 Step-by-Step

**Step 1 — Extract the hash from the locked PDF**

A PDF password is not stored in plain text. John needs the hash. I used `pdf2john`:

```bash
# Kali / Linux - pdf2john is included with John
python3 /usr/share/john/pdf2john.pl file1.pdf > hash1.txt
python3 /usr/share/john/pdf2john.pl file2.pdf > hash2.txt
python3 /usr/share/john/pdf2john.pl file3.pdf > hash3.txt

# Combine for one run
cat hash1.txt hash2.txt hash3.txt > all_hashes.txt
cat all_hashes.txtExample output (hash, not password):javascriptfile1.pdf:$pdf$4*4*128*-1028*1*16*...Step 2 — Open Johnnybashjohnny &
# OR
johnny all_hashes.txt &In Johnny:
Open Passwd File -> Select all_hashes.txtGo to Settings / Attack -> Select WordlistWordlist: /usr/share/wordlists/rockyou.txtFormat: Let Johnny auto-detect (PDF)Click Start AttackStep 3 — Same attack in Terminal (for proof / logs)
Johnny is doing this command in the background:bash# Wordlist attack
john --wordlist=/usr/share/wordlists/rockyou.txt --format=PDF all_hashes.txt

# Check progress
john --status=all_hashes.txt

# Show cracked passwords
john --show --format=PDF all_hashes.txtStep 4 — Resultbashjohn --show --format=PDF all_hashes.txtFileHash Cracked?Passwordfile1.pdf✅ Yes<password-1-found>file2.pdf✅ Yes<password-2-found>file3.pdf✅ Yes<password-3-found>Replace <password-1-found> with your real lab passwords before pushing to GitHub — or keep them as [REDACTED - LAB ONLY] if you want the repo public.Why it worked: All 3 PDF passwords were in the wordlist / were weak, dictionary words with simple numbers. A strong, long, random password would NOT have been found this way.
Part 2: Password Cracking with NetworkWalks Tools
2.1 What I Used
NetworkWalks Hash Calculator — Takes input (text / file) and shows its hash (MD5 / SHA1 / SHA256).NetworkWalks Password Cracker — Takes that hash, takes a wordlist, hashes every word in the wordlist one-by-one and compares it to the target hash. When they match, it has found the password.This is exactly what John does, but NetworkWalks shows you the process live so you can learn it.
2.2 WorkflowjavascriptPDF / Password -> Hash Calculator -> Copy Hash
-> Paste Hash into Password Cracker -> Select Wordlist
-> Watch it try word-by-word -> Match Found -> Password Recovered2.3 Step-by-Step
Step 1 — Get the hash
On NetworkWalks Hash Calculator:
Paste the PDF password / textSelect algorithm — e.g. MD5 / SHA256Copy the output hashPython equivalent — same thing the calculator does (see extract_hash.py):pythonimport hashlib
text = "your_test_password"
print(hashlib.md5(text.encode()).hexdigest())
print(hashlib.sha256(text.encode()).hexdigest())Step 2 — Crack it with a wordlist
On NetworkWalks Password Cracker:
Paste the hash from Step 1Select the same algorithmUpload / select wordlistClick CrackWhat I observed:
It did NOT try to “reverse” the hash — hashes cannot be reversed.It hashed password, then 123456, then admin, etc. — one by one.It compared each new hash to my target hash.When hash(word) == target_hash, it stopped and showed the password.Python equivalent — same logic (see cracker.py):pythonimport hashlib

target = "5f4dcc3b5aa765d61d8327deb882cf99"  # example MD5 for "password"
with open("wordlist.txt") as f:
    for word in f:
        word = word.strip()
        if hashlib.md5(word.encode()).hexdigest() == target:
            print(f"Found: {word}")
            breakStep 3 — ResultItemValueTarget Hash<paste-your-hash-here>AlgorithmMD5 / SHA256Wordlist UsedNetworkWalks default wordlistResult✅ Password recoveredTimeObserved live — fast because password was in wordlistKey Learning / How Hash Cracking Actually WorksjavascriptPassword  ->  Hash Function (MD5/SHA)  ->  Hash (stored)
                |
Attacker: Wordlist word -> Hash Function -> Compare to stored hash
                |
            Match = Password FoundYou never “decrypt” a hash. You guess, hash the guess, compare.Weak password = fast crack. If it’s in rockyou.txt, it will fall in seconds.Strong password = safe. Long + random + mixed case + symbols is NOT in any wordlist.PDF passwords are weak protection. Once the hash is extracted, it can be cracked offline with no lockout.Defence / What I Will Recommend at Work
Use minimum 14-character passphrases, not single wordsEnable MFA — even if a password is cracked, MFA blocks loginDon’t protect sensitive PDFs with a password alone — use 365 / SharePoint permissionsUse a password manager, never reuse passwordsLock accounts after failed attempts (online systems) — offline cracking has no lockout, so strong passwords matterFiles in This Repo
README.md — This reportextract_hash.py — Hash Calculator equivalent in Pythoncracker.py — Password Cracker equivalent in PythonHow to Reproducebash# 1. Get hash from a PDF (Part 1)
python3 /usr/share/john/pdf2john.pl test.pdf > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt --format=PDF hash.txt
john --show --format=PDF hash.txt

# 2. Hash + Crack demo (Part 2)
python3 extract_hash.py
python3 cracker.pyjavascript
---

### 2. File: `extract_hash.py`

```python
"""
Hash Calculator - NetworkWalks equivalent
Takes text / PDF password and shows its hash.
"""
import hashlib

def get_hash(text: str, algo: str = "md5"):
    h = hashlib.new(algo)
    h.update(text.encode())
    return h.hexdigest()

if __name__ == "__main__":
    password = input("Enter password/text to hash: ")
    print(f"MD5:    {get_hash(password, 'md5')}")
    print(f"SHA1:   {get_hash(password, 'sha1')}")
    print(f"SHA256: {get_hash(password, 'sha256')}")3. File: cracker.pypython"""
Password Cracker - NetworkWalks equivalent
Hashes every word in a wordlist and compares to target hash.
This is exactly how John the Ripper wordlist attack works.
"""
import hashlib

def crack(target_hash: str, wordlist_path: str, algo: str = "md5"):
    target_hash = target_hash.strip().lower()
    attempts = 0

    with open(wordlist_path, 'r', errors='ignore') as f:
        for line in f:
            word = line.strip()
            attempts += 1
            guess_hash = hashlib.new(algo, word.encode()).hexdigest()
            # Watch it try - like NetworkWalks live view
            print(f"[{attempts}] Trying: {word} -> {guess_hash[:12]}...")
            if guess_hash == target_hash:
                print(f"\n✅ FOUND in {attempts} attempts: {word}")
                return word

    print("\n❌ Not found - password not in wordlist")
    return None

if __name__ == "__main__":
    target = input("Paste target hash: ")
    algo = input("Algorithm (md5/sha1/sha256) [md5]: ") or "md5"
    wordlist = input("Wordlist path [wordlist.txt]: ") or "wordlist.txt"
    crack(target, wordlist, algo)

