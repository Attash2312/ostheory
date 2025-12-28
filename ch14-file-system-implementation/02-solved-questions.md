# Chapter 14 — Fully Solved Questions (File-System Implementation)

All questions include complete answers (theory + numericals + algorithms/pseudocode).

---

## A) Core Theory — Solved

### Q1) List the typical on-disk structures of a file system.
**Answer:** Boot control block (boot block), volume control block (superblock), directory structure, file control blocks (FCBs/inodes), and data blocks.

### Q2) Why does an OS keep both a per-process and a system-wide open-file table?
**Answer:**
- Per-process table maps **file descriptors → open file entries** and stores per-process flags.
- System-wide table holds **shared state** (current file offset, refcount, access mode) and links to cached inode/FCB.
This avoids duplicating metadata and supports shared opens.

---

## B) Allocation Methods — Solved Comparisons

### Q3) Compare contiguous, linked, and indexed allocation.
**Answer (exam-ready):**
- **Contiguous:** fast sequential + direct, but external fragmentation and file growth problems.
- **Linked:** no external fragmentation, easy growth, but no efficient random access and pointer overhead.
- **Indexed:** supports random access, no external fragmentation, but index-block overhead.

---

## C) Numericals — Fully Solved

### Q4) Contiguous allocation: blocks needed + internal fragmentation
A disk has **block size = 4 KiB**. A file size is **18 KiB**.
1) How many blocks are allocated?
2) Internal fragmentation in the last block?

**Solution:**
- Blocks needed $= \lceil 18/4 \rceil = \lceil 4.5 \rceil = 5$ blocks.
- Allocated space $= 5 \times 4 = 20$ KiB.
- Internal fragmentation $= 20 - 18 = 2$ KiB.

**Final:** 5 blocks, 2 KiB internal fragmentation.

---

### Q5) Bitmap size calculation
A disk has **1,048,576 blocks** (i.e., $2^{20}$ blocks). Use a bitmap with **1 bit per block**.
1) Bitmap size in bits and bytes
2) Bitmap size in KiB

**Solution:**
- Bits = $1{,}048{,}576$ bits.
- Bytes = $1{,}048{,}576/8 = 131{,}072$ bytes.
- KiB = $131{,}072/1024 = 128$ KiB.

**Final:** 1,048,576 bits = 131,072 bytes = 128 KiB.

---

### Q6) Inode addressing capacity (direct + single + double)
Assume:
- Block size = **4 KiB**
- A block pointer = **4 bytes**
- Inode has **12 direct pointers**, **1 single indirect**, **1 double indirect** (ignore triple)

Find maximum file size addressable.

**Solution:**
- Pointers per indirect block = $4096/4 = 1024$ pointers.
- Data via direct = $12 \times 4$ KiB = 48 KiB.
- Data via single indirect = $1024 \times 4$ KiB = 4096 KiB = 4 MiB.
- Data via double indirect = $1024 \times 1024 \times 4$ KiB
  = $1{,}048{,}576 \times 4$ KiB
  = 4,194,304 KiB
  = 4,096 MiB
  = 4 GiB.

**Total** = 48 KiB + 4 MiB + 4 GiB.

**Final:** approximately **4 GiB + 4 MiB + 48 KiB**.

---

## D) Complete Algorithms / Pseudocode

### Q7) Algorithm: find first free block using bitmap
**Goal:** return an index of a free block and mark it allocated.

```text
# bitmap[i] = 1 means free, 0 means allocated
allocate_block(bitmap):
  for i in 0..N-1:
    if bitmap[i] == 1:
      bitmap[i] = 0
      return i
  return FAIL
```

**Correctness:** first free bit found is allocated exactly once.

---

### Q8) Algorithm: allocate k contiguous blocks using bitmap
```text
allocate_k_contiguous(bitmap, k):
  run = 0
  start = -1
  for i in 0..N-1:
    if bitmap[i] == 1:
      if run == 0: start = i
      run = run + 1
      if run == k:
        for j in start..start+k-1:
          bitmap[j] = 0
        return start
    else:
      run = 0
      start = -1
  return FAIL
```

---

### Q9) Algorithm: path name resolution (very exam-relevant)
Resolve `/a/b/c` to an inode.

```text
resolve_path(path):
  inode = root_inode
  for component in split(path, '/'):   # a, b, c
    if component is empty: continue
    dir = read_directory(inode)
    inode = dir.lookup(component)     # name -> inode
    if inode == NOT_FOUND: return FAIL
  return inode
```

---

### Q10) How does FAT implement linked allocation?
**Answer:** Directory entry stores first block. FAT table maps `block -> next_block` until EOF.

```text
read_fat_file(first_block):
  b = first_block
  while b != EOF:
    read_block(b)
    b = FAT[b]
```

---

## E) Trick/Exam Questions — Solved

### Q11) Why is linked allocation bad for direct access?
**Answer:** To reach block $i$, you must traverse blocks 0..i−1 by following pointers (or FAT entries), so access time is $O(i)$ not $O(1)$.

### Q12) Why does contiguous allocation suffer from external fragmentation?
**Answer:** Free space breaks into separated holes; even if total free blocks are enough, there may not be a **single contiguous run** large enough for the file.
