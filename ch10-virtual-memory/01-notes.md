# Chapter 10 — Virtual Memory (Operating System Concepts, 10e)

Original, exam-focused notes (not copied). This chapter is **very high-yield** for numericals (page replacement) and long theory (demand paging).

---

## Beginner shortcuts (layman + mnemonics)

- In one line: Virtual memory lets processes act like they have more RAM using paging + disk.
- Mnemonic: TLB = fast cache of page table entries (hit saves one memory access).
- Page replacement intuition: FIFO(oldest), LRU(least recently used), OPT(farthest future use).
- Exam keywords: demand paging, page fault steps, thrashing, working set, EAT.

## 0) What to prioritize

**Very high yield:**
- virtual memory concept (why needed)
- demand paging + page faults
- page replacement algorithms: FIFO, OPT, LRU (+ faults numericals)
- frame allocation basics
- thrashing + working set idea

**Medium yield:**
- copy-on-write (COW)
- memory-mapped files (short)

---

## 1) Virtual memory (VM) idea

Virtual memory provides:
- illusion of large memory
- process isolation
- ability to run programs larger than RAM

Key mechanism: only needed pages are loaded into memory.

---

## 2) Demand paging (core)

**Demand paging:** load pages into RAM only when they are referenced.

- If referenced page not in memory → **page fault**.

### Page fault handling (must know steps)
```mermaid
flowchart TB
  REF[Reference page] --> IN{Page in memory?}
  IN -- yes --> OK[Continue execution]
  IN -- no --> PF[Page fault trap]
  PF --> VALID{Address valid?}
  VALID -- no --> KILL[Abort / segmentation fault]
  VALID -- yes --> FREE{Free frame?}
  FREE -- yes --> READ[Read page from disk]
  FREE -- no --> REPL[Choose victim (replacement)]
  REPL --> WRITE{Dirty?}
  WRITE -- yes --> WB[Write victim to disk]
  WRITE -- no --> READ
  WB --> READ
  READ --> UPDATE[Update page table/TLB]
  UPDATE --> RESTART[Restart instruction]
```

---

## 3) Effective Access Time with page faults (concept)

Let:
- memory access time = $m$
- page-fault rate = $p$ (probability)
- page fault service time = $F$ (disk + OS overhead)

Approx:
$$EAT \approx (1-p)m + pF$$

Since $F \gg m$, even small $p$ can hurt performance → why replacement and locality matter.

---

## 4) Copy-on-Write (COW) (common short question)

When a process is forked, parent and child initially share pages as read-only. Only when one writes, the OS copies the page.

Benefits:
- saves memory
- speeds process creation

---

## 5) Page replacement (big numerical area)

When no free frames exist, OS must pick a victim page.

### 5.1 FIFO
- evict oldest page
- can show **Belady’s anomaly** (faults can increase with more frames)

### 5.2 OPT (Optimal)
- evict page whose next use is farthest in future
- minimal possible faults (benchmark)

### 5.3 LRU
- evict least recently used page
- approximates OPT using past behavior

---

## 6) Frame allocation (overview)

- **Equal allocation:** each process gets same frames.
- **Proportional allocation:** frames proportional to process size.
- **Local replacement:** a process replaces its own pages.
- **Global replacement:** can take frames from other processes.

---

## 7) Thrashing (very common theory)

**Thrashing:** system spends more time paging than executing because processes don’t have enough frames.

Symptoms:
- high page fault rate
- low CPU utilization

Fix ideas:
- reduce degree of multiprogramming
- give more frames
- use working set / page-fault frequency control

---

## 8) Write-up templates

### Template A — Demand paging + page fault steps (10 marks)
- define demand paging
- define page fault
- list handling steps (validity check, frame, replacement, disk read, update, restart)

### Template B — Compare FIFO, LRU, OPT (12 marks)
- define each
- mention implementation difficulty
- show one line on anomaly (FIFO) and benchmark (OPT)
