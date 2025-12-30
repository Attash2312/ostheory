# Chapter 5 — Fully Solved CPU Scheduling Questions (Algorithms + Numericals)

Everything here is fully solved with an exam-friendly method.

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Scheduling decides who gets the CPU next to balance speed, fairness, and response.
- Mnemonic: Formulas = TAT=CT-AT; WT=TAT-BT; RT=FirstRun-AT.
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Short Theory — Solved

### Q1) Differentiate preemptive and non-preemptive scheduling.
**Answer:**
- **Non-preemptive:** once a process gets CPU, it runs until it blocks or finishes (e.g., FCFS non-preemptive).
- **Preemptive:** OS can take CPU away (timer interrupt or higher-priority arrival) (e.g., RR, SRTF).

### Q2) Define turnaround time, waiting time, and response time.
**Answer:**
For a process $i$:
- $TAT_i = Completion_i - Arrival_i$
- $WT_i = TAT_i - Burst_i$
- $RT_i = FirstStart_i - Arrival_i$

### Q3) What is starvation? How does aging solve it?
**Answer:**
- **Starvation:** a process waits indefinitely because others keep getting CPU.
- **Aging:** gradually increases a waiting process’s priority so it eventually runs.

---

## B) Numericals (Gantt charts) — Fully Solved

### Problem set used in multiple algorithms
Processes:

| Process | Arrival (AT) | Burst (BT) |
|---|---:|---:|
| P1 | 0 | 7 |
| P2 | 2 | 4 |
| P3 | 4 | 1 |
| P4 | 5 | 4 |

We will solve FCFS, SJF (non-preemptive), SRTF (preemptive), and RR (q=2).

---

### Q4) FCFS (non-preemptive) — Solve

**Step 1: Order by arrival:** P1, P2, P3, P4

**Gantt chart:**
- P1: 0–7
- P2: 7–11
- P3: 11–12
- P4: 12–16

```text
0 | P1 | 7 | P2 | 11 | P3 | 12 | P4 | 16
```

**Completion times (CT):**
- CT(P1)=7, CT(P2)=11, CT(P3)=12, CT(P4)=16

Compute:
- $TAT = CT - AT$
- $WT = TAT - BT$
- $RT = firstStart - AT$ (FCFS firstStart = start time)

| P | Start | CT | TAT | WT | RT |
|---|---:|---:|---:|---:|---:|
| P1 | 0 | 7 | 7 | 0 | 0 |
| P2 | 7 | 11 | 9 | 5 | 5 |
| P3 | 11 | 12 | 8 | 7 | 7 |
| P4 | 12 | 16 | 11 | 7 | 7 |

**Averages:**
- Avg WT = (0+5+7+7)/4 = 4.75
- Avg TAT = (7+9+8+11)/4 = 8.75
- Avg RT = (0+5+7+7)/4 = 4.75

---

### Q5) SJF (non-preemptive) — Solve

**Rule:** at each decision, choose the shortest burst among arrived processes.

Timeline:
- t=0: only P1 available → run P1 (0–7)
- t=7: P2 (4), P3 (1), P4 (4) are all available → choose P3 (7–8)
- then choose P2 (8–12)
- then P4 (12–16)

```text
0 | P1 | 7 | P3 | 8 | P2 | 12 | P4 | 16
```

| P | Start | CT | TAT | WT | RT |
|---|---:|---:|---:|---:|---:|
| P1 | 0 | 7 | 7 | 0 | 0 |
| P3 | 7 | 8 | 4 | 3 | 3 |
| P2 | 8 | 12 | 10 | 6 | 6 |
| P4 | 12 | 16 | 11 | 7 | 7 |

**Averages:**
- Avg WT = (0+3+6+7)/4 = 4.0
- Avg TAT = (7+4+10+11)/4 = 8.0

---

### Q6) SRTF (preemptive SJF) — Solve

**Rule:** always run the process with shortest *remaining* time; preempt on arrivals.

Work through arrivals:
- t=0–2: P1 runs, remaining(P1)=5
- t=2: P2 arrives (4) < remaining(P1)=5 → switch to P2
- t=2–4: P2 runs, remaining(P2)=2
- t=4: P3 arrives (1) < remaining(P2)=2 → switch to P3
- t=4–5: P3 finishes
- t=5: P4 arrives (4). Compare remaining: P2=2, P1=5, P4=4 → run P2
- t=5–7: P2 finishes
- remaining: P4=4, P1=5 → run P4 (7–11)
- finally P1 (11–16)

```text
0 | P1 | 2 | P2 | 4 | P3 | 5 | P2 | 7 | P4 | 11 | P1 | 16
```

Compute CT:
- CT(P3)=5
- CT(P2)=7
- CT(P4)=11
- CT(P1)=16

First start times:
- P1 starts at 0
- P2 starts at 2
- P3 starts at 4
- P4 starts at 7

| P | FirstStart | CT | TAT | WT | RT |
|---|---:|---:|---:|---:|---:|
| P1 | 0 | 16 | 16 | 9 | 0 |
| P2 | 2 | 7 | 5 | 1 | 0 |
| P3 | 4 | 5 | 1 | 0 | 0 |
| P4 | 7 | 11 | 6 | 2 | 2 |

**Averages:**
- Avg WT = (9+1+0+2)/4 = 3.0
- Avg TAT = (16+5+1+6)/4 = 7.0
- Avg RT = (0+0+0+2)/4 = 0.5

---

### Q7) Round Robin (q = 2) — Solve

**Rule:** each process gets up to 2 units, then goes to back of ready queue if remaining.

Simulate with arrivals:
- 0–2: P1 (rem 5)
- 2–4: P2 (arrived at 2) (rem 2)
- 4–6: P1 (rem 3) (P3 arrives at 4, P4 at 5)
- 6–7: P3 runs (needs 1) finishes
- 7–9: P2 runs (rem 0) finishes
- 9–11: P4 runs (rem 2)
- 11–13: P1 runs (rem 1)
- 13–15: P4 runs (rem 0) finishes
- 15–16: P1 runs (rem 0) finishes

```text
0|P1|2|P2|4|P1|6|P3|7|P2|9|P4|11|P1|13|P4|15|P1|16
```

Completion times:
- CT(P3)=7, CT(P2)=9, CT(P4)=15, CT(P1)=16
First starts:
- P1=0, P2=2, P3=6, P4=9

| P | FirstStart | CT | TAT | WT | RT |
|---|---:|---:|---:|---:|---:|
| P1 | 0 | 16 | 16 | 9 | 0 |
| P2 | 2 | 9 | 7 | 3 | 0 |
| P3 | 6 | 7 | 3 | 2 | 2 |
| P4 | 9 | 15 | 10 | 6 | 4 |

Averages:
- Avg WT = (9+3+2+6)/4 = 5.0
- Avg TAT = (16+7+3+10)/4 = 9.0
- Avg RT = (0+0+2+4)/4 = 1.5

---

## C) Priority Scheduling — Fully Solved Numerical

### Q8) Priority (non-preemptive) with smaller number = higher priority

| Process | AT | BT | Priority |
|---|---:|---:|---:|
| P1 | 0 | 6 | 3 |
| P2 | 1 | 3 | 1 |
| P3 | 2 | 5 | 4 |
| P4 | 3 | 2 | 2 |

**Non-preemptive decision points:**
- t=0: only P1 → run 0–6
- t=6: choose among P2(pr1), P4(pr2), P3(pr4) → P2 (6–9)
- then P4 (9–11)
- then P3 (11–16)

```text
0 | P1 | 6 | P2 | 9 | P4 | 11 | P3 | 16
```

Compute:
| P | Start | CT | TAT | WT | RT |
|---|---:|---:|---:|---:|---:|
| P1 | 0 | 6 | 6 | 0 | 0 |
| P2 | 6 | 9 | 8 | 5 | 5 |
| P4 | 9 | 11 | 8 | 6 | 6 |
| P3 | 11 | 16 | 14 | 9 | 9 |

**Starvation note:** low priority can wait long; fix via aging.

---

## D) Scheduling “Algorithms” (pseudocode) — Complete

### Q9) Algorithm: FCFS
```text
FCFS(ready_queue):
  sort by arrival time
  for each process in order:
    run to completion (non-preemptive)
```

### Q10) Algorithm: SJF (non-preemptive)
```text
SJF:
  time = 0
  while unfinished processes exist:
    add all arrived processes to candidate set
    choose process with minimum burst time
    run it to completion
    time += burst
```

### Q11) Algorithm: SRTF (preemptive)
```text
SRTF:
  time = 0
  while unfinished processes exist:
    add arrived processes to ready set
    choose process with minimum remaining time
    run it for 1 time unit (or until next arrival)
    update remaining time
    if remaining becomes 0: mark complete
    time++
```

### Q12) Algorithm: Round Robin
```text
RR(q):
  time = 0
  while unfinished processes exist:
    pop front process from ready queue
    run for min(q, remaining)
    time += executed
    add any newly arrived processes
    if remaining > 0: push process to back
    else: record completion
```

---

## E) Common theory long question — Solved

### Q13) Explain criteria for CPU scheduling and why RR is good for interactive systems.
**Answer:**
- Criteria: maximize utilization/throughput; minimize WT/TAT/RT; ensure fairness.
- RR gives each process a time slice, so no process waits too long before getting CPU again → better response time for interactive workloads.
- Tradeoff: small quantum increases context-switch overhead.
