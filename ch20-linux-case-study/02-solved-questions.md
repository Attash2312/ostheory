# Chapter 20 — Fully Solved Questions (Linux Case Study)

All questions include complete answers.

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: Linux case study connects OS concepts to a real OS (processes, scheduling, memory, FS).
- Mnemonic: "Everything is a file" (common Linux mindset for devices and I/O).
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Architecture — Solved

### Q1) Explain Linux architecture in 4–6 lines.
**Answer:** Linux uses a monolithic kernel design with loadable modules. User programs interact with kernel services through the system call interface. The kernel provides process management, memory management, VFS/file systems, networking, and device drivers, ultimately controlling hardware.

### Q2) Why are loadable kernel modules useful?
**Answer:** They allow adding/removing driver and feature support without rebuilding/rebooting the entire kernel, improving flexibility and keeping the base kernel smaller.

---

## B) Process Creation — Solved

### Q3) Explain the `fork()` + `exec()` pattern.
**Answer:** A parent process calls `fork()` to create a child. The child then calls `exec()` to replace its memory image with a new program. This is a standard pattern for launching a new program while inheriting some context (like open file descriptors) from the parent.

### Q4) Why is `fork()` efficient in modern OS designs?
**Answer:** Because of **copy-on-write**: the parent and child initially share pages marked read-only; only pages that are written are copied.

---

## C) Algorithms / Pseudocode — Complete

### Q5) Pseudocode: process creation (fork/exec/wait)
```text
parent():
  pid = fork()
  if pid == 0:
    exec(program, argv)
    exit(FAIL)           # if exec fails
  else:
    wait(pid)
    continue
```

---

### Q6) Pseudocode: copy-on-write write fault (concept)
```text
on_write_to_shared_page(page):
  if page.is_shared and page.is_readonly:
    new = allocate_frame()
    copy(page.frame, new)
    map_current_process(page.va, new, writable=true)
    decrement_refcount(page.frame)
```

---

## D) VFS — Solved

### Q7) What problem does VFS solve?
**Answer:** It provides a single, consistent file API and naming model for applications while supporting multiple file system implementations underneath.

### Q8) Outline path resolution at high level.
**Answer:** The OS walks path components from a starting directory (root or cwd), looking up each name in directories and following metadata pointers to reach the final inode/FCB.

---

## E) Numericals — Fully Solved (simple exam-style)

### Q9) COW memory saving
A parent has 400 MiB of resident pages. After `fork()`, the child writes to 10% of pages. Estimate additional memory used by COW copies.

**Solution:**
Additional copies ≈ 10% of 400 MiB = 40 MiB.

**Final:** about **40 MiB** extra.

---

## F) Bonus Exam Pack — Fully Solved

### Q10) What is `/proc` in Linux (concept) and why useful?
**Answer:** `/proc` is a virtual (pseudo) file system exposing kernel and process information as files (e.g., process status, memory maps). It’s useful for monitoring, debugging, and configuration without special APIs.

---

### Q11) Numerical: Unix permissions
A file has mode `-rwxr-x---`.
1) What are the permissions for owner/group/others?
2) What is the octal representation?

**Solution:**
1) Owner: `rwx` (7), Group: `r-x` (5), Others: `---` (0)
2) Octal = **750**

**Final:** owner=7, group=5, others=0 ⇒ **750**.

---

### Q12) Algorithm: permission check (owner/group/others)
```text
can_access(user, file, op):
  if user.uid == file.owner_uid:
    rights = file.owner_bits
  else if user.gid in file.group_gids:
    rights = file.group_bits
  else:
    rights = file.other_bits

  return (op in rights)
```

**Exam note:** Real systems also check capabilities/ACLs, but this is the core Unix bit check.
