# Chapter 6 — Fully Solved Questions (Synchronization Tools)

All questions are fully solved. Includes complete algorithms and correct semaphore logic.

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Synchronization prevents two threads from messing up shared data at the same time.
- Mnemonic: Mutex = 1 key (only one inside); Counting semaphore = k keys (k identical resources).
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Core Definitions — Solved

### Q1) What is a race condition?
**Answer:** A race condition occurs when multiple threads/processes access shared data concurrently and the outcome depends on the timing/interleaving of execution.

### Q2) State the three requirements of a critical-section solution.
**Answer:**
1. Mutual exclusion
2. Progress
3. Bounded waiting

---

## B) Long Theory — Solved Model Answers

### Q3) Explain Peterson’s solution and why it works.
**Answer (model):**
- Peterson’s algorithm solves the 2-process critical section problem using `flag[i]` (interest) and `turn` (tie-break).
- **Mutual exclusion:** If both try to enter, only the one whose turn it is can pass the while condition.
- **Progress:** If CS is empty, a process that wants to enter will not be postponed forever.
- **Bounded waiting:** A process cannot be bypassed indefinitely because `turn` alternates when both compete.
- Limitation: busy waiting; works reliably under assumptions of atomic reads/writes and proper memory visibility.

Algorithm:
```text
flag[i] = true
turn = 1-i
while(flag[1-i] && turn == 1-i) ;
// CS
flag[i] = false
```

---

## C) Algorithms (Complete) — Solved

### Q4) Write the algorithm for test-and-set lock.
**Solution:**
```text
shared boolean lock = false

boolean test_and_set(boolean *lock):
  old = *lock
  *lock = true
  return old

acquire():
  while(test_and_set(&lock)) ;

release():
  lock = false
```

**Explanation:** `test_and_set` is atomic; only one thread sees old=false and enters.

### Q5) Write a compare-and-swap (CAS) lock.
**Solution:**
```text
shared int lock = 0  // 0=free, 1=busy

int CAS(int *value, int expected, int new):
  temp = *value
  if (*value == expected) *value = new
  return temp

acquire():
  while(CAS(&lock, 0, 1) != 0) ;

release():
  lock = 0
```

---

## D) Semaphore Numericals / Patterns — Fully Solved

### Q6) (Counting semaphore) A lab has 3 identical printers. Use a semaphore to control access.
**Solution:**
- Use counting semaphore `S = 3`.
- Each print job:
```text
wait(S)
// use printer
signal(S)
```
**Why correct:** At most 3 jobs can decrement `S` and proceed; others block.

---

### Q7) (Binary semaphore / mutex) Protect a critical section.
**Solution:**
```text
semaphore mutex = 1

process:
  wait(mutex)
  // critical section
  signal(mutex)
```

---

### Q8) (Ordering problem) Ensure statement S1 in P1 executes before statement S2 in P2.
**Solution:** Use semaphore `sync = 0`.

```text
semaphore sync = 0

P1:
  S1
  signal(sync)

P2:
  wait(sync)
  S2
```
**Reason:** P2 cannot reach S2 until P1 signals.

---

### Q9) (Bounded buffer with semaphores) Buffer size N. Give correct semaphore solution.

**Solution:**
Use:
- `mutex = 1` (binary)
- `empty = N` (counting)
- `full = 0` (counting)

Producer:
```text
wait(empty)
wait(mutex)
  insert_item()
signal(mutex)
signal(full)
```

Consumer:
```text
wait(full)
wait(mutex)
  remove_item()
signal(mutex)
signal(empty)
```

**Why correct:**
- `empty/full` enforce capacity constraints
- `mutex` enforces mutual exclusion for buffer

---

## E) Monitor Question — Fully Solved

### Q10) Explain monitor and condition variables with a small example.
**Answer:**
- A monitor ensures only one thread executes monitor procedures at a time.
- Condition variables allow waiting for a condition.

Example (conceptual):
```text
monitor BoundedBuffer:
  condition notFull, notEmpty

  procedure insert(x):
    while buffer_full: notFull.wait()
    add x
    notEmpty.signal()

  procedure remove() returns x:
    while buffer_empty: notEmpty.wait()
    x = remove
    notFull.signal()
    return x
```

**Key exam point:** `wait()` releases the monitor lock while sleeping.

---

## F) Quick “common mistakes” — Solved explanations

### Q11) What happens if producer does `wait(mutex)` before `wait(empty)` in bounded buffer?
**Answer:** It can cause deadlock: producer may hold `mutex` while waiting on `empty`, blocking consumer from entering CS to consume and signal `empty`.

---

## G) Bonus Exam Pack — Fully Solved

### Q12) Semaphore vs monitor: what’s the difference and which is safer for exams?
**Answer:**
- **Semaphore:** low-level synchronization primitive (`wait/signal`) that can be used for mutual exclusion, ordering, and counting resources; easy to misuse (missing `signal`, wrong order) → bugs/deadlocks.
- **Monitor:** higher-level construct that bundles shared data + procedures with implicit mutual exclusion; condition variables handle waiting/signaling; typically easier to reason about.

**Exam line:** Monitors are “safer” conceptually because mutual exclusion is built-in; semaphores are more flexible but error-prone.
