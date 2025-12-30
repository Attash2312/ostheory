# Chapter 7 — Fully Solved Classic Synchronization Problems

Everything is complete: correct semaphore/monitor solutions and explanations.

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Classic sync problems teach correct semaphore ordering (Producer-Consumer, Readers-Writers, Dining Philosophers).
- Mnemonic (Producer): wait(empty)->wait(mutex)->insert->signal(mutex)->signal(full).
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Producer–Consumer (Bounded Buffer) — Fully Solved

### Q1) Give a complete semaphore solution for bounded buffer of size N.

**Solution:**
Shared:
- `semaphore mutex = 1`
- `semaphore empty = N`
- `semaphore full = 0`

Producer:
```text
produce(item):
  wait(empty)
  wait(mutex)
    insert(item)
  signal(mutex)
  signal(full)
```

Consumer:
```text
consume():
  wait(full)
  wait(mutex)
    item = remove()
  signal(mutex)
  signal(empty)
  return item
```

**Why correct (exam explanation):**
- `empty/full` prevent overflow/underflow.
- `mutex` ensures only one thread touches the buffer structure at a time.

---

## B) Readers–Writers (Reader-preference) — Fully Solved

### Q2) Provide a reader-preference semaphore solution.

**Goal:** many readers can read together; writers need exclusive access.

Shared:
- `semaphore rw_mutex = 1`  (controls access to the shared resource)
- `semaphore mutex = 1`     (protects readcount)
- `int readcount = 0`

Reader:
```text
reader():
  wait(mutex)
    readcount++
    if readcount == 1:
      wait(rw_mutex)
  signal(mutex)

  // reading

  wait(mutex)
    readcount--
    if readcount == 0:
      signal(rw_mutex)
  signal(mutex)
```

Writer:
```text
writer():
  wait(rw_mutex)
  // writing
  signal(rw_mutex)
```

**Property:** Writers can starve if readers keep arriving.

---

## C) Readers–Writers (Writer-fair idea) — Fully Solved

### Q3) How do you prevent writer starvation? Give a fair approach (conceptual).

**Solution idea:** Use a “turnstile” semaphore to queue readers behind writers.

Shared:
- `semaphore turnstile = 1`
- `semaphore rw_mutex = 1`
- `semaphore mutex = 1`
- `int readcount = 0`

Reader:
```text
wait(turnstile)
signal(turnstile)

wait(mutex)
  readcount++
  if readcount == 1: wait(rw_mutex)
signal(mutex)

// read

wait(mutex)
  readcount--
  if readcount == 0: signal(rw_mutex)
signal(mutex)
```

Writer:
```text
wait(turnstile)
wait(rw_mutex)
// write
signal(rw_mutex)
signal(turnstile)
```

**Why it helps:** If a writer arrives, it holds `turnstile`, preventing new readers from continually bypassing it.

---

## D) Dining Philosophers — Fully Solved

### Q4) Show how deadlock happens in dining philosophers (5 philosophers).
**Answer:** If each philosopher picks the left fork first, all 5 may hold one fork and wait forever for the other fork → circular wait → deadlock.

### Q5) Give a complete deadlock-free strategy using resource ordering.

**Solution:** Number forks 1..5. Philosopher i always picks lower-numbered fork first, then higher.

Pseudocode for philosopher needing forks a and b:
```text
first = min(a, b)
second = max(a, b)

wait(fork[first])
wait(fork[second])
  eat()
signal(fork[second])
signal(fork[first])
```

**Why deadlock-free:** Resource ordering breaks circular wait.

### Q6) Give another deadlock-free strategy: allow at most 4 philosophers to try at once.

**Solution:** Use counting semaphore `room = 4`.

```text
wait(room)
wait(leftFork)
wait(rightFork)
  eat()
signal(rightFork)
signal(leftFork)
signal(room)
```

**Why it works:** At least one philosopher can obtain both forks because not all 5 compete simultaneously.

---

## E) Sleeping Barber (if included) — Fully Solved

### Q7) Provide a correct semaphore solution (one barber, one chair, N waiting chairs).

Shared:
- `semaphore customers = 0`
- `semaphore barber = 0`
- `semaphore mutex = 1`
- `int waiting = 0`
- `int N = number_of_waiting_chairs`

Barber:
```text
barber():
  while true:
    wait(customers)
    wait(mutex)
      waiting--
      signal(barber)
    signal(mutex)
    cut_hair()
```

Customer:
```text
customer():
  wait(mutex)
    if waiting < N:
      waiting++
      signal(customers)
      signal(mutex)
      wait(barber)
      get_haircut()
    else:
      signal(mutex)
      leave()
```

**Explanation:**
- Customers semaphore wakes barber.
- Barber semaphore synchronizes customer entering the chair.
- Mutex protects `waiting`.

---

## F) Bonus Exam Pack (Most-asked variants) — Fully Solved

### Q8) Producer–Consumer using a monitor (condition variables)

**Solution (monitor-style):**
```text
monitor BoundedBuffer:
  condition notFull, notEmpty
  buffer[0..N-1]
  count = 0
  in = 0
  out = 0

  procedure insert(item):
    while count == N:
      notFull.wait()
    buffer[in] = item
    in = (in + 1) mod N
    count = count + 1
    notEmpty.signal()

  procedure remove() returns item:
    while count == 0:
      notEmpty.wait()
    item = buffer[out]
    out = (out + 1) mod N
    count = count - 1
    notFull.signal()
    return item
```

**Why correct:** Monitor ensures mutual exclusion; conditions enforce capacity constraints.

---

### Q9) Readers–Writers using a fairness “turnstile” (explain why it prevents starvation)

**Answer:** In reader-preference, a constant stream of incoming readers can keep `readcount>0` forever, so writers never acquire the resource.

The turnstile fixes this by forcing **all arriving threads** (readers and writers) to queue behind an arriving writer.

**Key exam explanation:** Once a writer does `wait(turnstile)`, **new readers cannot pass** the turnstile. Existing readers finish, `readcount` drops to 0, and writer gets `rw_mutex`.

---

### Q10) Dining philosophers: prove resource ordering avoids deadlock (2–4 lines)

**Solution:** With strict ordering, every philosopher acquires forks in increasing order. That implies there cannot exist a cycle of waits where each philosopher holds a higher-numbered fork and waits on a lower-numbered fork (waits always go from lower→higher). Therefore **circular wait** is broken → deadlock impossible.

---

### Q11) Dining philosophers: starvation risk and one simple fix

**Answer:** Even if deadlock is prevented, starvation may occur if a philosopher repeatedly loses the race to acquire forks.

**Fix:** Use a **fair mutex/queue** (FIFO lock) around the “pick forks” phase, or a monitor that grants forks in arrival order.

---

### Q12) Sleeping barber: what happens if `mutex` is removed? (fully explained)

**Answer:** Without `mutex`, updates to `waiting` are not atomic:
- Two customers may read `waiting` simultaneously and both decide there’s a free chair, then both increment → `waiting` becomes incorrect.
- Barber and customers can race on `waiting--/waiting++`, causing negative values or exceeding N.

**Conclusion:** `mutex` is required to protect the shared counter and maintain correctness.
