# Chapter 4 — Fully Solved Exam Questions (Threads & Concurrency)

All questions include complete answers.

---

## A) Very Short (1–2 marks) — Solved

### Q1) Define thread.
**Answer:** A thread is the basic unit of CPU utilization inside a process, having its own PC, registers, and stack, while sharing the process’s code/data/heap.

### Q2) What do threads in the same process share?
**Answer:** They share code, data/heap, and OS resources like open files; each thread has its own PC, registers, and stack.

---

## B) Short (3–5 marks) — Solved

### Q3) Differentiate process and thread.
**Answer:**
- **Process:** independent execution with separate address space; communication needs IPC; heavier.
- **Thread:** execution path inside a process; shares address space; cheaper to create/switch but needs synchronization.

### Q4) List four benefits of multithreading.
**Answer:**
- responsiveness
- resource sharing
- economy
- scalability on multicore

### Q5) User threads vs kernel threads.
**Answer:**
- **User threads:** managed by user library; fast operations; can suffer if blocking system call blocks all (depends on model).
- **Kernel threads:** managed by OS; better true parallelism and blocking handling; more overhead.

---

## C) Long (8–12 marks) — Solved Model Answers

### Q6) Explain multithreading models (many-to-one, one-to-one, many-to-many).
**Model answer:**
- **Many-to-one:** multiple user threads mapped to one kernel thread.
  - Pros: fast user-level switching.
  - Cons: no true parallelism; one blocking syscall blocks process.
- **One-to-one:** each user thread maps to one kernel thread.
  - Pros: true parallelism; better with blocking.
  - Cons: overhead; OS may limit threads.
- **Many-to-many:** many user threads multiplexed over a smaller/larger set of kernel threads.
  - Pros: parallelism + flexibility.
  - Cons: more complex.

```mermaid
flowchart LR
  subgraph U[User Threads]
    U1[U1] U2[U2] U3[U3] U4[U4]
  end
  subgraph K[Kernel Threads]
    K1[K1] K2[K2]
  end
  U1-->K1
  U2-->K1
  U3-->K2
  U4-->K2
```

### Q7) Explain race condition and critical section with an example.
**Model answer:**
- A **race condition** occurs when multiple threads access shared data and the result depends on their execution order.
- A **critical section** is a code region that accesses shared data and must not be executed by more than one thread at a time.

**Example:** Two threads increment `count`:
- if both read old value before either writes back, one increment is lost.

**Conclusion:** Need synchronization (mutex/semaphore/monitor) to protect critical section.

---

## D) Algorithms (Pseudocode) — Complete + Explained

### Q8) Algorithm: Creating and joining a thread (generic)
**Question:** Write steps for thread creation and join.

**Solution:**
```text
main():
  tid = create_thread(worker, arg)
  do_other_work()
  join_thread(tid)
  exit

worker(arg):
  compute()
  return
```

**Exam note:** `join` ensures main waits for worker completion.

### Q9) Algorithm: Thread pool idea
**Question:** Write pseudocode for a thread pool handling tasks.

**Solution:**
```text
initialize_pool(k):
  create k worker threads

submit_task(task):
  enqueue(task_queue, task)
  signal(worker_available)

worker_thread():
  loop forever:
    wait_until(task_queue not empty)
    task = dequeue(task_queue)
    execute(task)
```

**Why used:** reduces overhead of repeated create/destroy and improves throughput.

---

## E) Small numerical-style — Fully Solved (speedup intuition)

### Q10) If a program is 70% parallelizable and you use 4 cores, estimate speedup (idealized).
Use Amdahl’s Law:
$$S = \frac{1}{(1-P) + \frac{P}{N}}$$
Where $P=0.7$, $N=4$.

**Solution:**
- Denominator = $(1-0.7) + 0.7/4 = 0.3 + 0.175 = 0.475$
- $S = 1/0.475 \approx 2.105$

**Answer:** About **2.11×** speedup (idealized).

(If your course doesn’t use Amdahl’s Law, skip this in revision.)

---

## F) Bonus Exam Pack — Fully Solved

### Q11) Why can a blocking system call block the whole process in many-to-one model?
**Answer:** In many-to-one, multiple user threads map to a single kernel thread. If any user thread makes a blocking system call, the **single kernel thread blocks**, so the OS cannot schedule another thread from that process on the CPU until the call returns.

---

### Q12) Amdahl’s Law numerical (different values)
If $P=0.9$ (90% parallel) and $N=8$ cores, estimate speedup.

**Solution:**
$$S = \frac{1}{(1-P)+\frac{P}{N}} = \frac{1}{0.1 + \frac{0.9}{8}} = \frac{1}{0.1 + 0.1125} = \frac{1}{0.2125} \approx 4.706$$

**Final:** about **4.71×**.
