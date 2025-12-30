# Chapter 8 — Fully Solved Deadlock Questions (Theory + Banker’s Numericals)

All questions are fully solved, including complete Banker’s algorithm steps.

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Deadlock is a permanent waiting cycle; avoidance checks if the system can still finish safely.
- Mnemonic: Coffman conditions = M-H-N-C = Mutual exclusion, Hold&wait, No preemption, Circular wait.
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Core Theory — Solved

### Q1) Define deadlock.
**Answer:** Deadlock is a situation where a set of processes are blocked forever because each holds at least one resource and waits for another resource held by another process in the set.

### Q2) State Coffman’s four necessary conditions for deadlock.
**Answer:** Mutual exclusion, hold and wait, no preemption, circular wait.

### Q3) How can circular wait be prevented?
**Answer:** Impose a strict ordering on resources (R1 < R2 < …) and require processes to request resources in increasing order only.

---

## B) Resource Allocation Graph (RAG) — Solved

### Q4) In a single-instance RAG, what does a cycle mean?
**Answer:** If each resource type has exactly one instance, a cycle in the RAG implies deadlock.

### Q5) For multiple instances per resource type, does a cycle always imply deadlock?
**Answer:** No. A cycle indicates possible deadlock, but it is not sufficient when resources have multiple instances.

---

## C) Banker’s Algorithm — Fully Solved Numerical (Safety)

### Q6) Determine if the system is in a safe state and find a safe sequence.

Assume 3 resource types (A, B, C) and 5 processes (P0..P4).

**Available:**
- A=3, B=3, C=2

**Allocation:**
| Process | A | B | C |
|---|---:|---:|---:|
| P0 | 0 | 1 | 0 |
| P1 | 2 | 0 | 0 |
| P2 | 3 | 0 | 2 |
| P3 | 2 | 1 | 1 |
| P4 | 0 | 0 | 2 |

**Max:**
| Process | A | B | C |
|---|---:|---:|---:|
| P0 | 7 | 5 | 3 |
| P1 | 3 | 2 | 2 |
| P2 | 9 | 0 | 2 |
| P3 | 2 | 2 | 2 |
| P4 | 4 | 3 | 3 |

**Step 1: Compute Need = Max − Allocation**
| Process | Need A | Need B | Need C |
|---|---:|---:|---:|
| P0 | 7 | 4 | 3 |
| P1 | 1 | 2 | 2 |
| P2 | 6 | 0 | 0 |
| P3 | 0 | 1 | 1 |
| P4 | 4 | 3 | 1 |

**Step 2: Safety check**
Let Work = Available = (3,3,2). Finish all false.

- Check P1 Need (1,2,2) ≤ Work (3,3,2) ⇒ can finish.
  - Work = Work + Allocation(P1) = (3,3,2) + (2,0,0) = (5,3,2)
- Check P3 Need (0,1,1) ≤ Work (5,3,2) ⇒ can finish.
  - Work = (5,3,2) + (2,1,1) = (7,4,3)
- Check P4 Need (4,3,1) ≤ Work (7,4,3) ⇒ can finish.
  - Work = (7,4,3) + (0,0,2) = (7,4,5)
- Check P0 Need (7,4,3) ≤ Work (7,4,5) ⇒ can finish.
  - Work = (7,4,5) + (0,1,0) = (7,5,5)
- Check P2 Need (6,0,0) ≤ Work (7,5,5) ⇒ can finish.
  - Work = (7,5,5) + (3,0,2) = (10,5,7)

**Safe sequence:** ⟨P1, P3, P4, P0, P2⟩

**Conclusion:** The system is in a **safe state**.

---

## D) Banker’s Algorithm — Fully Solved (Resource Request)

### Q7) Using the same system as Q6, suppose P1 requests (1,0,2). Should it be granted?

**Step 1: Check Request ≤ Need(P1)**
- Need(P1) = (1,2,2)
- Request = (1,0,2)
- (1,0,2) ≤ (1,2,2) ✅

**Step 2: Check Request ≤ Available**
- Available = (3,3,2)
- Request = (1,0,2)
- (1,0,2) ≤ (3,3,2) ✅

**Step 3: Pretend allocate**
- New Available = (3,3,2) − (1,0,2) = (2,3,0)
- New Allocation(P1) = (2,0,0) + (1,0,2) = (3,0,2)
- New Need(P1) = (1,2,2) − (1,0,2) = (0,2,0)

**Step 4: Run safety test quickly**
Work = (2,3,0)
- P1 Need (0,2,0) ≤ Work ⇒ finish P1 → Work += Alloc(P1) = (2,3,0)+(3,0,2)=(5,3,2)
Now the system reduces to the original safety case after P1 finishes, which we already know is safe.

**Decision:** Grant the request ✅

---

## E) Deadlock Detection (Matrix-style) — Fully Solved

### Q8) Explain the idea of deadlock detection in one paragraph.
**Answer:** Deadlock detection periodically checks whether processes can finish with current available resources, similar to Banker’s safety test but without `Max` guarantees. If some processes cannot be marked finished and no further progress is possible, those processes are in deadlock.

---

## F) Recovery — Solved theory

### Q9) Give two recovery methods after detecting deadlock.
**Answer:**
1. **Terminate processes** (all at once or one-by-one until the cycle breaks).
2. **Resource preemption** (take resource from one process, roll it back, and retry later), if safe/possible.

---

## G) Common long question — Solved write-up

### Q10) Compare deadlock prevention, avoidance, and detection.
**Answer:**
- **Prevention:** structurally break at least one Coffman condition; may reduce utilization.
- **Avoidance:** use knowledge of maximum needs (e.g., Banker’s) to stay in safe states; more computation and requires Max.
- **Detection:** allow deadlocks, detect them, then recover; simpler upfront but needs recovery handling.

---

## H) Bonus Exam Pack — Fully Solved

### Q11) Write a deadlock detection algorithm (matrix-style) in pseudocode.
**Solution (conceptual):**
```text
deadlock_detect(Available, Allocation, Request):
  Work = Available
  Finish[i] = false for all i

  repeat:
    found = false
    for each process i:
      if Finish[i] == false and Request[i] <= Work:
        Work = Work + Allocation[i]
        Finish[i] = true
        found = true
    until found == false

  deadlocked = { i | Finish[i] == false }
  return deadlocked
```

**Exam note:** `Request[i]` is remaining resource request by process i.

---

### Q12) Map common strategies to which Coffman condition they break.
**Answer:**
- **Resource ordering** breaks **circular wait**.
- **Request-all-at-once** breaks **hold and wait**.
- **Preemption** breaks **no preemption** (when resources can be safely preempted).
- **Spooling a printer** reduces **mutual exclusion** by virtualizing the resource (jobs share a queue).
