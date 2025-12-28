# Chapter 6 — Synchronization Tools (Operating System Concepts, 10e)

Original, exam-focused notes (not copied). This is **extremely high yield** for theory + algorithms.

---

## 0) What to prioritize

**Very high yield:**
- race condition, critical section, requirements (mutual exclusion, progress, bounded waiting)
- Peterson’s algorithm (2-process software solution)
- hardware primitives: test-and-set, compare-and-swap (CAS)
- mutex locks, spinlocks
- semaphores (binary/counting) + `wait(P)` / `signal(V)`
- monitors + condition variables

**Medium yield:**
- memory ordering concept (keep brief)

---

## 1) The problem: race conditions

### 1.1 Race condition
When two or more threads/processes access shared data and the final result depends on execution interleaving.

### 1.2 Critical section problem
**Critical section:** code that accesses shared data.

A correct solution must satisfy:
1. **Mutual exclusion:** at most one process in CS.
2. **Progress:** if no one is in CS, selection of next entrant cannot be postponed indefinitely.
3. **Bounded waiting:** a process won’t wait forever (no starvation).

```mermaid
flowchart LR
  N[Non-critical] --> R[Request CS]
  R --> C[Critical Section]
  C --> E[Exit CS]
  E --> N
```

---

## 2) Peterson’s solution (two processes) — must know

Works for two processes P0 and P1 using:
- `flag[i]` indicates intention
- `turn` indicates whose turn

**Algorithm (complete):**
```text
// shared
boolean flag[2] = {false, false}
int turn = 0

process i (i = 0 or 1):
  do {
    flag[i] = true
    turn = 1 - i
    while (flag[1-i] == true && turn == 1-i) ;  // busy wait

    // critical section

    flag[i] = false

    // remainder section
  } while (true)
```

**Key exam points:**
- satisfies mutual exclusion, progress, bounded waiting (for 2 processes)
- busy waiting (spin)

---

## 3) Hardware support (atomic instructions)

### 3.1 Test-and-Set (TAS)
Atomic operation:
```text
boolean test_and_set(boolean *lock):
  old = *lock
  *lock = true
  return old
```
Usage:
```text
while (test_and_set(&lock)) ;
// critical section
lock = false
```

### 3.2 Compare-and-Swap (CAS)
Atomic operation:
```text
int compare_and_swap(int *value, int expected, int new):
  temp = *value
  if (*value == expected) *value = new
  return temp
```

**Spinlock note:** TAS/CAS often create spinlocks (good for short CS, bad for long CS on single core).

---

## 4) Mutex locks

A **mutex** provides mutual exclusion:
- `lock()` blocks or spins until available
- `unlock()` releases

**Spin vs blocking:**
- Spinlock wastes CPU but avoids context switch.
- Blocking lock saves CPU but may incur context-switch overhead.

---

## 5) Semaphores (very high yield)

**Semaphore S:** integer + atomic operations:
- `wait(S)` (P / down)
- `signal(S)` (V / up)

**Atomic definitions (conceptual):**
```text
wait(S):
  while S <= 0: block (or busy wait)
  S = S - 1

signal(S):
  S = S + 1
  wake one waiting process (if any)
```

Types:
- **Binary semaphore:** S in {0,1} (similar to mutex)
- **Counting semaphore:** S can be > 1 (count resources)

**Common exam traps:**
- semaphore operations must be atomic
- wrong ordering causes deadlock

---

## 6) Monitors + condition variables

A **monitor** encapsulates shared data + procedures so only one thread executes inside at a time.

Condition variables support waiting inside monitor:
- `x.wait()` releases monitor lock and sleeps
- `x.signal()` wakes one waiter

```mermaid
flowchart TB
  M[Monitor]
  M --> Data[Shared data]
  M --> Ops[Procedures
(mutual exclusion)]
  M --> CV[Condition variables
wait/signal]
```

---

## 7) Classic pitfalls (short but important)

- **Deadlock** can happen with multiple locks in inconsistent order.
- **Starvation** can happen with unfair scheduling/locking.

---

## 8) Write-up templates

### Template A — “Critical section requirements” (5 marks)
- define CS
- list 3 requirements
- give 1-line example of race

### Template B — “Semaphore vs monitor” (8 marks)
- semaphore: low-level signaling
- monitor: high-level structured synchronization
- mention condition variables
