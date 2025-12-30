# Chapter 16 — Fully Solved Questions (Security)

All questions include complete answers (theory + numericals + algorithms/pseudocode).

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Security is protecting systems from attacks and ensuring trust.
- Mnemonic: CIA = Confidentiality, Integrity, Availability.
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Core Theory — Solved

### Q1) Define CIA triad with one example each.
**Answer:**
- Confidentiality: only authorized read (e.g., file permissions).
- Integrity: prevent tampering (e.g., checksums, signed updates).
- Availability: services stay usable (e.g., redundancy, DoS mitigation).

### Q2) Differentiate threat, vulnerability, and attack.
**Answer:** Vulnerability is a weakness; threat is the potential that a weakness will be exploited; attack is the actual exploitation attempt.

---

## B) Authentication — Solved

### Q3) Why is salted hashing used for passwords?
**Answer:** Salt makes identical passwords hash differently, defeats precomputed rainbow tables, and increases attacker cost because they must attack each salt separately.

### Q4) Give a simple login verification algorithm.
```text
verify_login(username, password_input):
  (salt, stored_hash) = DB.get(username)
  candidate = H(salt || password_input)
  if constant_time_equal(candidate, stored_hash):
    return AUTH_OK
  else:
    return AUTH_FAIL
```

---

## C) Access Control Models — Solved

### Q5) Compare DAC, MAC, and RBAC in 3–4 lines.
**Answer:** DAC lets object owners set permissions; MAC uses system-enforced labels so users can’t bypass; RBAC assigns permissions to roles and users get permissions through role membership.

---

## D) Numericals — Fully Solved

### Q6) Password search-space (keyspace) numerical
A system enforces passwords of length **8**, using **A–Z (26)** and **0–9 (10)**. Total alphabet size = 36.
Compute total possible passwords.

**Solution:**
Total $= 36^8$.
$36^2=1296,\ 36^4=1{,}679{,}616,\ 36^8=(1{,}679{,}616)^2=2{,}821{,}109{,}907{,}456$.

**Final:** $36^8 = 2{,}821{,}109{,}907{,}456$ passwords (≈ $2.82\times 10^{12}$).

---

### Q7) Brute-force time estimate
If an attacker can try **1,000,000 guesses/second**, worst-case time for Q6?

**Solution:**
Worst-case guesses = $36^8$.
Time $= 2.821109907456\times 10^{12} / 10^6 = 2.821109907456\times 10^6$ seconds.
Convert: $2.821\times 10^6 / 3600 \approx 783.6$ hours ≈ 32.65 days.

**Final:** about **33 days worst-case** (half that on average).

---

## E) Program Threats & Defenses — Solved

### Q8) How do ASLR and DEP reduce buffer-overflow success?
**Answer:** DEP blocks execution from data pages (stack/heap), so injected shellcode can’t run directly. ASLR randomizes addresses so attacker can’t reliably jump to gadgets/targets; combined they reduce exploit reliability.

### Q9) What is the principle of least privilege and why important?
**Answer:** Give each user/process the minimum permissions required. It limits damage if the account or process is compromised.

---

## F) Simple detection algorithm (audit)

### Q10) Algorithm: account lockout after N failed logins in window T
```text
on_failed_login(user, now):
  record failure timestamp
  keep only failures in [now - T, now]
  if count_failures >= N:
    lock account for L minutes
```

**Why works:** prevents unlimited online guessing while allowing normal users to retry.

---

## G) Bonus Exam Pack — Fully Solved

### Q11) What is a reference monitor? List its 3 properties.
**Answer:** A reference monitor is an abstract machine that mediates all accesses between subjects and objects.
Properties:
1) **Complete mediation:** every access is checked.
2) **Tamperproof:** cannot be modified by untrusted code.
3) **Verifiable:** small/simple enough to be tested/verified.

---

### Q12) Numerical: probability of guessing a 4-digit PIN with limited attempts
A PIN has 4 digits (0000–9999). An attacker gets **3 attempts** before lockout.
What is the probability of success (assuming random guessing, no repeats)?

**Solution:**
- Total PINs = 10,000.
- Successful guesses possible in 3 tries = 3.
- Probability $= 3/10000 = 0.0003 = 0.03\%$.

**Final:** **0.03%** success chance.
