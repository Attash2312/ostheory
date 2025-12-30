# Chapter 17 — Fully Solved Questions (Protection)

All questions include complete answers (theory + algorithms/pseudocode).

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Protection controls what a process/user is allowed to do inside a system.
- Mnemonic: ACM = Access Control Matrix (Subject x Object -> Rights).
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Core Theory — Solved

### Q1) What is a protection domain?
**Answer:** A set of access rights that specifies which operations a subject (user/process) can perform on objects (files, devices, memory). It defines the subject’s authority at a given time.

### Q2) Differentiate protection and security.
**Answer:** Protection is internal authorization control inside the OS; security includes protection plus defenses against external threats (malware, network attacks, physical threats).

---

## B) Access Matrix — Solved

### Q3) Define an access matrix and explain two implementations.
**Answer:** Access matrix is a conceptual table mapping domains to objects with rights sets.
- Implementation 1: **ACL** per object (list who can access it).
- Implementation 2: **Capabilities** per domain (list what it can access).

---

## C) ACL vs Capability — Solved Scenarios

### Q4) If you need to answer “who can read file X?” which is better and why?
**Answer:** ACL — because it’s stored with the object (file X) and directly lists all subjects and their rights.

### Q5) If you need to delegate limited rights to another process quickly, which is often simpler?
**Answer:** Capability-style delegation can be simpler: give the other process a capability token representing the specific rights.

---

## D) Algorithms / Pseudocode — Complete

### Q6) Access check using an ACL
```text
check_access_ACL(user_or_role, object, op):
  acl = object.ACL
  rights = acl.lookup(user_or_role)
  if rights contains op:
    return ALLOW
  else:
    return DENY
```

---

### Q7) Access check using capabilities
```text
check_access_capability(process, object, op):
  cap = process.capability_list.find(object)
  if cap exists and cap.rights contains op:
    return ALLOW
  else:
    return DENY
```

---

### Q8) Controlled domain switching
Assume a process in domain D1 wants to switch to D2.

```text
switch_domain(process, D2):
  if (D2 in process.current_domain.switch_rights):
    process.current_domain = D2
    return OK
  else:
    return DENY
```

---

## E) Revocation — Solved

### Q9) Why is revocation harder with capabilities and how can we solve it?
**Answer:** Copies of capabilities may exist across subjects; you can’t easily find all of them.
Solutions include:
- **Indirection**: capability points to an object table entry; revoke by updating table.
- **Version numbers**: object version increments to invalidate old capabilities.
- **Expiration**: time-limited capabilities.

---

## F) Mini matrix example — Solved

### Q10) Given rights, decide if an operation is permitted.
Domain D1 has rights `{read, write}` on file F. Is `execute` permitted?

**Solution:** Execute is not in the rights set.

**Final:** **DENY**.

---

## G) Bonus Exam Pack — Fully Solved

### Q11) Convert an access matrix entry into ACL and capability forms.
Suppose domain D1 has `{read, write}` on file F and domain D2 has `{read}` on file F.

**Solution:**
- **ACL for F:** `[(D1: {read, write}), (D2: {read})]`
- **Capabilities:**
  - CapList(D1) contains `(F, {read, write})`
  - CapList(D2) contains `(F, {read})`

---

### Q12) Delegation vs revocation: short solved scenario
You want to temporarily grant a helper process permission to read one file, then revoke it later.

**Answer:**
- **Delegation:** capabilities make it easy (hand over a capability token for that file).
- **Revocation:** ACL is easier (remove helper from file’s ACL). For capabilities, use indirection/versioning/expiration to make revocation practical.
