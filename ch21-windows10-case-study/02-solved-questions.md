# Chapter 21 — Fully Solved Questions (Windows 10 Case Study)

All questions include complete answers.

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Windows case study shows how a commercial OS organizes kernel components and services.
- Mnemonic: Hybrid kernel = mix of monolithic speed + microkernel ideas.
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Architecture — Solved

### Q1) Describe Windows 10 architecture at a high level.
**Answer:** Windows separates user mode and kernel mode. Applications call system APIs which transition into kernel mode where core OS services, the scheduler, memory manager, and I/O subsystems operate with drivers to control hardware. The design is often described as hybrid for performance and modularity.

---

## B) Processes/Threads — Solved

### Q2) Differentiate a process and a thread in Windows.
**Answer:** A process owns an address space and resources (handles). A thread is the schedulable execution entity that runs code within a process.

### Q3) Why is Windows described as thread-centric?
**Answer:** Because scheduling decisions are made for threads (priority, quantum), while processes mainly provide the execution environment and resource container.

---

## C) I/O — Solved

### Q4) Outline the typical I/O flow.
**Answer:** Application issues an I/O call → the OS routes it through an I/O manager → request passes through a driver stack → device performs the operation → completion is returned (often asynchronously).

---

## D) Security — Solved

### Q5) How does access control work conceptually?
**Answer:** Access checks compare a subject’s security token (identity, groups, privileges) against an object’s ACL to decide allow/deny for requested operations.

---

## E) Algorithms / Pseudocode — Complete

### Q6) Generic access check with ACL (Windows-like concept)
```text
access_check(token, object_acl, desired_right):
  allowed = union of rights for token.user and token.groups in object_acl
  if desired_right in allowed:
    return ALLOW
  else:
    return DENY
```

---

## F) Numericals — Fully Solved (simple exam-style)

### Q7) Working set style question
A process has a working set of 200 MiB. If it grows by 15%, what is the new size?

**Solution:**
New = 200 × 1.15 = 230 MiB.

**Final:** **230 MiB**.

---

## G) Bonus Exam Pack — Fully Solved

### Q8) What is a handle in Windows and why is it process-specific?
**Answer:** A handle is a process-local reference (an index/token) to a kernel object (file, process, mutex, etc.). It is process-specific because each process has its own handle table, enabling isolation and access control (one process can’t automatically use another process’s handles).

---

### Q9) What is an access token (concept) and how is it used?
**Answer:** An access token represents a security context (user SID, group SIDs, privileges). During an access check, Windows compares the token against the object’s ACL to decide if requested rights are allowed.

---

### Q10) Pseudocode: simplified process creation flow (conceptual)
```text
CreateProcess(image, args):
  create EPROCESS (process object)
  create address space + map image sections
  create initial thread (ETHREAD) with start routine
  setup handle table, environment, security token
  insert thread into scheduler ready queue
  return handles/IDs
```

---

### Q11) Explain asynchronous I/O in one paragraph (exam-ready)
**Answer:** With asynchronous I/O, a thread issues an I/O request and continues without blocking; completion is delivered later (completion event/callback). This improves responsiveness and throughput because CPU can run other work while the device operates.

---

### Q12) Numerical: working set reduction
A process working set is 300 MiB. The memory manager trims it by 20% under pressure. What is the new working set size?

**Solution:**
New = 300 × (1 − 0.20) = 300 × 0.80 = 240 MiB.

**Final:** **240 MiB**.
