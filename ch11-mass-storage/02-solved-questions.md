# Chapter 11 — Fully Solved Questions (Mass Storage + Disk Scheduling Numericals)

All questions include complete answers/solutions.

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Mass storage is about disks/SSDs, scheduling requests, and reliability.
- Mnemonic: SCAN = elevator goes to end; LOOK = elevator stops at last request; C-SCAN = one direction then wrap.
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Short Theory — Solved

### Q1) What are seek time and rotational latency?
**Answer:** Seek time is the time to move the disk arm/head to the required track; rotational latency is the time waiting for the desired sector to rotate under the head.

### Q2) Why do we need disk scheduling?
**Answer:** To reduce average head movement/seek time, improve throughput, and provide fair service when multiple I/O requests are pending.

---

## B) Disk Scheduling Numericals — Fully Solved (FCFS / SSTF / SCAN / C-SCAN / LOOK / C-LOOK)

### Common dataset
Disk cylinders: 0 to 199
Request queue (in arrival order):
`98, 183, 37, 122, 14, 124, 65, 67`
Initial head position: `53`

We compute **total head movement** (sum of absolute differences).

---

### Q3) FCFS — Solve
Order: 53 → 98 → 183 → 37 → 122 → 14 → 124 → 65 → 67

Movement:
- |53−98|=45
- |98−183|=85 (total 130)
- |183−37|=146 (276)
- |37−122|=85 (361)
- |122−14|=108 (469)
- |14−124|=110 (579)
- |124−65|=59 (638)
- |65−67|=2 (640)

**Total FCFS head movement = 640 cylinders**

---

### Q4) SSTF — Solve
At each step choose closest pending request.

From 53, pending {98,183,37,122,14,124,65,67}:
- Closest is 65 (|53−65|=12)
Then from 65 → 67 (2) → 37 (30) → 14 (23) → 98 (84) → 122 (24) → 124 (2) → 183 (59)

Compute total:
- 53→65: 12
- 65→67: 2  (14)
- 67→37: 30 (44)
- 37→14: 23 (67)
- 14→98: 84 (151)
- 98→122: 24 (175)
- 122→124: 2 (177)
- 124→183: 59 (236)

**Total SSTF head movement = 236 cylinders**

(Observation: much better average seek, but starvation is possible in general.)

---

### Q5) SCAN (Elevator), moving toward 0 first — Solve
Assume head moves downward first, servicing requests <53 on the way to 0, then reverses.

Requests below 53: 37, 14
Requests above 53: 65, 67, 98, 122, 124, 183

Order:
53 → 37 → 14 → 0 → 65 → 67 → 98 → 122 → 124 → 183

Movement:
- 53→37: 16
- 37→14: 23 (39)
- 14→0: 14 (53)
- 0→65: 65 (118)
- 65→67: 2 (120)
- 67→98: 31 (151)
- 98→122: 24 (175)
- 122→124: 2 (177)
- 124→183: 59 (236)

**Total SCAN (toward 0 first) = 236 cylinders**

---

### Q6) C-SCAN, moving toward 199 (upward) — Solve
Assume head moves upward only, then jumps from end to start.

Order (service upward):
53 → 65 → 67 → 98 → 122 → 124 → 183 → 199 → 0 → 14 → 37

Movement:
- 53→65: 12
- 65→67: 2 (14)
- 67→98: 31 (45)
- 98→122: 24 (69)
- 122→124: 2 (71)
- 124→183: 59 (130)
- 183→199: 16 (146)
- 199→0: 199 (345)
- 0→14: 14 (359)
- 14→37: 23 (382)

**Total C-SCAN = 382 cylinders**

---

### Q7) LOOK (like SCAN but don’t go to end), moving toward 0 first — Solve
Order:
53 → 37 → 14 → 65 → 67 → 98 → 122 → 124 → 183

Movement:
- 53→37: 16
- 37→14: 23 (39)
- 14→65: 51 (90)
- 65→67: 2 (92)
- 67→98: 31 (123)
- 98→122: 24 (147)
- 122→124: 2 (149)
- 124→183: 59 (208)

**Total LOOK = 208 cylinders**

---

### Q8) C-LOOK, moving upward — Solve
Service upward to highest request, then jump to lowest request (not to 0).

Order:
53 → 65 → 67 → 98 → 122 → 124 → 183 → 14 → 37

Movement:
- 53→65: 12
- 65→67: 2 (14)
- 67→98: 31 (45)
- 98→122: 24 (69)
- 122→124: 2 (71)
- 124→183: 59 (130)
- 183→14: 169 (299)
- 14→37: 23 (322)

**Total C-LOOK = 322 cylinders**

---

## C) RAID — Solved

### Q9) Compare RAID 0, 1, 5, 6, 10 in one table (exam-ready).
**Answer:**
- **RAID 0:** striping; fast; no fault tolerance.
- **RAID 1:** mirroring; survives 1 disk failure per mirror; ~50% usable capacity.
- **RAID 5:** striping + distributed parity; survives 1 disk failure; write penalty due to parity.
- **RAID 6:** like RAID 5 with dual parity; survives 2 disk failures.
- **RAID 10:** striped mirrors; strong performance and reliability; capacity overhead.

### Q10) If you have 4 disks of 1 TB each, what usable capacity do you get in RAID 0 and RAID 1?
**Solution:**
- RAID 0 capacity = 4 × 1 TB = **4 TB**
- RAID 1 (mirroring pairs) usable ≈ half = **2 TB**

---

## D) Complete “Algorithms” (pseudocode) — Solved

### Q11) Algorithm: FCFS total head movement
```text
total_movement = 0
head = H
for req in queue:
  total_movement += abs(head - req)
  head = req
return total_movement
```

### Q12) Algorithm: SSTF selection
```text
while requests remain:
  choose req that minimizes abs(head - req)
  move head to req; add distance to total
  remove req from pending set
```
