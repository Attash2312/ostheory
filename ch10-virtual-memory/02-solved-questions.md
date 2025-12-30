# Chapter 10 — Fully Solved Questions (Virtual Memory + Page Replacement Numericals)

All questions are fully solved, including complete algorithms and step-by-step page-fault counting.

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Virtual memory lets processes act like they have more RAM using paging + disk.
- Mnemonic: TLB = fast cache of page table entries (hit saves one memory access).
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Short Theory — Solved

### Q1) Define demand paging.
**Answer:** Demand paging is a virtual-memory technique where pages are loaded into RAM only when they are referenced; a missing page triggers a page fault.

### Q2) What is a page fault?
**Answer:** A page fault is an exception raised when a process references a page not currently in physical memory.

### Q3) What is thrashing?
**Answer:** Thrashing is excessive paging where the system spends most of its time swapping pages rather than executing, typically due to insufficient frames.

---

## B) Page Fault Handling — Solved long answer

### Q4) Explain steps of handling a page fault.
**Model answer:**
1. Page fault trap transfers control to OS.
2. OS checks if referenced address is valid.
3. If invalid → terminate process.
4. If valid → locate page on disk.
5. If free frame exists, use it; otherwise select victim page.
6. If victim is dirty, write back to disk.
7. Read required page into frame.
8. Update page table/TLB.
9. Restart the faulting instruction.

---

## C) Algorithms (Pseudocode) — Complete

### Q5) Algorithm: FIFO page replacement
```text
FIFO(frames, reference_string):
  queue = empty
  faults = 0
  for page in reference_string:
    if page not in frames:
      faults++
      if frames has free slot:
        put page in free slot
        enqueue(queue, page)
      else:
        victim = dequeue(queue)
        replace victim with page
        enqueue(queue, page)
  return faults
```

### Q6) Algorithm: OPT (optimal) replacement
```text
OPT(frames, reference_string):
  faults = 0
  for i from 0..len-1:
    page = reference_string[i]
    if page not in frames:
      faults++
      if free slot exists:
        place page
      else:
        for each frame_page in frames:
          find next index j > i where reference_string[j] == frame_page
          if not found: next_use = infinity
        victim = page with farthest next_use
        replace victim with page
  return faults
```

### Q7) Algorithm: LRU replacement (conceptual)
```text
LRU(frames, reference_string):
  faults = 0
  last_used = map(page -> time)
  time = 0
  for page in reference_string:
    time++
    if page in frames:
      last_used[page] = time
    else:
      faults++
      if free slot exists:
        place page
      else:
        victim = page in frames with smallest last_used value
        replace victim with page
        remove victim from last_used
      last_used[page] = time
  return faults
```

---

## D) Page Replacement Numericals — Fully Solved (FIFO / OPT / LRU)

### Reference string used
Let reference string be:
`7 0 1 2 0 3 0 4 2 3 0 3 2`
Frames = 3

We count page faults for FIFO, OPT, and LRU.

---

### Q8) FIFO with 3 frames — Solve

Simulate (frames shown as a set; FIFO order implicit):
- 7 F: {7}
- 0 F: {7,0}
- 1 F: {7,0,1}
- 2 F: evict 7 → {2,0,1}
- 0 hit
- 3 F: evict 0 → {2,3,1}
- 0 F: evict 1 → {2,3,0}
- 4 F: evict 2 → {4,3,0}
- 2 F: evict 3 → {4,2,0}
- 3 F: evict 0 → {4,2,3}
- 0 F: evict 4 → {0,2,3}
- 3 hit
- 2 hit

**Total FIFO faults = 10**

---

### Q9) OPT with 3 frames — Solve

Key idea: replace page whose next use is farthest.

Step summary:
- Load 7,0,1 → 3 faults.
- On 2: look ahead (0 used soon, 1 not used again, 7 not used again). Evict 7 or 1; pick either not-used-again. Evict 7 → faults=4.
- On 3: frames {2,0,1}. Look ahead: 2 used at 8, 0 used at 6, 1 never again → evict 1 → faults=5.
- On 4: frames {2,0,3}. Look ahead: 2 used at 9, 0 used at 11, 3 used at 10 → farthest is 0 at 11 → evict 0 → faults=6.
- On 0 at position 11: frames {2,4,3}. Look ahead after 0: 4 never again, 2 used at end, 3 used soon → evict 4 → faults=7.
Remaining references then hit.

**Total OPT faults = 7**

---

### Q10) LRU with 3 frames — Solve

Simulate with “least recently used” victim:
- 7 F: {7}
- 0 F: {7,0}
- 1 F: {7,0,1}
- 2 F: evict LRU(7) → {2,0,1}
- 0 hit (recent)
- 3 F: among {2,0,1}, LRU is 1 → {2,0,3}
- 0 hit
- 4 F: among {2,0,3}, LRU is 2 → {4,0,3}
- 2 F: among {4,0,3}, LRU is 3 → {4,0,2}
- 3 F: among {4,0,2}, LRU is 0 → {4,3,2}
- 0 F: among {4,3,2}, LRU is 4 → {0,3,2}
- 3 hit
- 2 hit

**Total LRU faults = 9**

---

## E) Page-fault-rate EAT (concept numerical) — Fully Solved

### Q11) If memory access is 100 ns, page fault service time is 8 ms, and page fault rate p=0.0001, estimate EAT.

Convert 8 ms to ns:
- $8\,ms = 8 \times 10^{-3}\,s = 8\times 10^{-3}\times 10^9\,ns = 8\times 10^6\,ns$

EAT:
$$EAT \approx (1-p)\cdot 100 + p\cdot (8\times 10^6)$$
$$= 0.9999\cdot 100 + 0.0001\cdot 8,000,000$$
$$= 99.99 + 800 = 899.99\,ns$$

**Answer:** approximately **900 ns** (almost 9× slower than 100 ns).

---

## F) Copy-on-Write — Solved

### Q12) Explain copy-on-write and why it helps `fork()`.
**Answer:** With COW, parent and child initially share the same physical pages marked read-only. If either writes, the OS copies the page and gives the writer a private copy. This reduces memory usage and makes `fork()` faster.
