# Networkwalks-RobinVuru-B084-WK3-PM1_PM2-Cybersecurity-PW-Cracking-with-JTRv1-Networkwalks-Tools

# Password Cracking Lab — John the Ripper + NetworkWalks Tools

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
-> Select Wordlist Attack -> Start -> Password Found

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
cat all_hashes.txt

Example output (hash, not password):
