# Chapter 15 — Fully Solved Questions (File-System Internals)

All questions include complete answers.

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Internals cover reliability and performance tricks used inside file systems.
- Mnemonic: Journaling = write the plan first, then do the work (helps crash recovery).
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Caching/Buffering — Solved

### Q1) Explain buffer cache, page cache, and why a unified cache is useful.
**Answer:**
- Buffer cache stores disk blocks used by the file system (data + metadata).
- Page cache stores file contents as VM pages.
A unified cache avoids duplicate copies and improves hit rate by letting one cache serve both file I/O and memory-mapped I/O.

### Q2) What is read-ahead? When does it help?
**Answer:** Prefetching future blocks before they’re requested. It helps when access is sequential because next blocks are highly predictable.

---

## B) Crash Consistency — Solved

### Q3) Give an example of an inconsistent state after crash.
**Answer:** Bitmap marks a block allocated, but no inode points to it (leak), or inode points to a block but bitmap says free (double allocation risk).

### Q4) What does a consistency checker (fsck) do?
**Answer:** Scans metadata, validates invariants (link counts, directory connectivity, bitmap matches), and repairs inconsistencies by freeing leaked blocks, reconnecting orphaned files, etc.

---

## C) Journaling — Fully Solved

### Q5) Explain journaling with commit record and recovery cases.
**Answer:**
1) Collect updates as a transaction $T$.
2) Write journal records describing updates.
3) Write **commit record** to journal.
4) Apply updates to main FS.

Recovery after crash:
- Crash **before commit**: treat $T$ as not committed → ignore/rollback.
- Crash **after commit but before apply completes**: replay journal records → ensure $T$ is fully applied.

---

### Q6) Compare metadata journaling, full data journaling, and ordered mode.
**Answer:**
- **Metadata journaling:** logs metadata only → fast; data may be lost/corrupted unless ordered.
- **Full data journaling:** logs both data+metadata → strongest guarantees; slower.
- **Ordered mode:** ensure data blocks reach disk before metadata commit → common compromise.

---

## D) Numericals — Fully Solved

### Q7) Cache hit ratio and average access time
Assume:
- Cache hit time = **0.1 ms**
- Disk access time on miss = **8 ms**
- Hit ratio = **90%**

Compute average access time.

**Solution:**
Average $= 0.9\cdot 0.1 + 0.1\cdot (0.1 + 8)$ ms
$= 0.09 + 0.1\cdot 8.1$
$= 0.09 + 0.81$
$= 0.90$ ms.

**Final:** **0.90 ms** average.

---

## E) Complete Algorithms / Pseudocode

### Q8) LRU cache update (conceptual)
Maintain recency list; on every access move item to front.

```text
access(cache, key):
  if key in cache:
    move key to front (most recent)
    return HIT
  else:
    if cache full:
      evict least-recent (tail)
    insert key at front
    return MISS
```

---

### Q9) Journaling transaction protocol (metadata journaling)

```text
begin_txn(T)
write_journal(records_of_T)
write_journal(COMMIT(T))
flush_journal_to_disk()
apply_updates_to_main_fs(T)
checkpoint_if_needed()
end_txn(T)
```

Recovery:
```text
recover():
  scan journal
  for each txn T:
    if COMMIT(T) present:
      redo(T)
    else:
      ignore(T)
```

---

## F) Exam Scenarios — Solved

### Q10) App writes a file then power fails immediately. How can app ensure data is durable?
**Answer:** The app must call `fsync(fd)` (or equivalent) after writing to force buffered data + required metadata to stable storage. Without it, delayed write may lose data.

### Q11) Why does journaling reduce fsck time?
**Answer:** After crash, you only replay a bounded log (recent transactions) instead of scanning the entire file system for inconsistencies.

---

## G) Bonus Exam Pack — Fully Solved

### Q12) Why does “ordered” journaling help prevent file corruption?
**Answer:** In ordered mode, the file system ensures **data blocks are written to disk before** committing the corresponding metadata updates (like inode pointers or directory entries). That prevents a crash from leaving metadata pointing to uninitialized/old garbage blocks. It improves consistency without the full overhead of journaling all file data.
