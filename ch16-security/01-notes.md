# Chapter 16 — Security (Exam Notes)

> Focus: threats, controls, authentication, access control, program threats, and practical OS security mechanisms.

---

## 1) Goals and terminology

### CIA triad
- **Confidentiality**: prevent unauthorized disclosure
- **Integrity**: prevent unauthorized modification
- **Availability**: ensure systems/data usable when needed

### Threat vs vulnerability vs attack
- **Vulnerability**: weakness (bug, misconfig)
- **Threat**: potential cause of incident
- **Attack/exploit**: action using vulnerability

---

## 2) Security policy vs mechanism
- **Policy**: what is allowed (rules)
- **Mechanism**: how OS enforces it (ACLs, capabilities, MAC)

Exam line: *Policy changes often; mechanisms are reusable.*

---

## 3) Authentication (who are you?)

### Factors
- Something you **know** (password/PIN)
- Something you **have** (token/smart card)
- Something you **are** (biometrics)

### Password best practices (OS view)
- Store **salted hash**, never plaintext
- Rate limiting + lockout/backoff
- MFA when possible

---

## 4) Authorization / Access control (what can you do?)

Common models:
- **DAC** (Discretionary): owner controls (Unix-style)
- **MAC** (Mandatory): system labels; users can’t override (SELinux-style)
- **RBAC**: permissions grouped into roles

```mermaid
flowchart TD
  U[User] --> R[Role]
  R --> P[Permissions]
  P --> O[Objects
(files, devices, IPC)]
```

---

## 5) Program threats (very exam-relevant)

### Malware categories
- **Virus**: attaches to host; spreads with user execution
- **Worm**: self-propagates over network
- **Trojan**: looks benign; performs malicious action
- **Spyware/Keylogger**: steals info

### Common exploit vectors
- **Buffer overflow** → control-flow hijack
- **Code injection** (shellcode) / **ROP**
- **Privilege escalation**

### OS defenses (must memorize)
- **DEP/NX bit**: non-executable pages
- **ASLR**: randomize address layout
- **Stack canaries**
- **Least privilege** + sandboxing
- **Patch management**

---

## 6) Trusted computing base (TCB)
TCB = minimal set of components that must be trusted (kernel, security-critical services).

Security design rule: *keep TCB small.*

---

## 7) Security in practice: auditing and logging
- **Audit trails** record security-relevant events
- Useful for detection + forensics
- Must protect logs (integrity + access)

---

## 8) Quick exam templates

- **“Explain ASLR + DEP”**: DEP prevents executing data; ASLR makes addresses unpredictable; together reduce exploit reliability.
- **“Policy vs mechanism”**: policy = rules; mechanism = enforcement tools.
- **“RBAC vs DAC vs MAC”**: RBAC role-based; DAC owner-controlled; MAC label-based mandatory.
