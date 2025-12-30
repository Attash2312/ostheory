# Chapter 13 — Fully Solved Questions (File-System Interface)

All questions include complete answers.

---

## Beginner shortcuts (layman + mnemonics)

- How to score marks fast: write the definition in 1 line, then 3-5 crisp bullets, then a tiny example if possible.
- In one line: File system interface is what users/programs see: files, directories, and operations.
- Mnemonic: CRUD+O = Create, Read, Update/Write, Delete, Open/Close.
- Common mistakes: skipping the rule-set/assumptions, mixing symbols, and not showing steps in numericals.
- If a numerical exists: write Given -> Table/Diagram -> Steps -> Final Answer (boxed).

## A) Short Theory — Solved

### Q1) Define file and list four attributes.
**Answer:** A file is a named collection of related information stored on secondary storage. Attributes include name, size, location, protection, owner, timestamps.

### Q2) List common file operations.
**Answer:** create, open, read, write, seek, close, delete, truncate.

---

## B) Access Methods — Fully Solved

### Q3) Differentiate sequential and direct access with examples.
**Answer:**
- **Sequential:** data is processed in order; example: log files, media streams.
- **Direct:** data can be accessed at any position; example: database record access.

### Q4) What is indexed access and where is it useful?
**Answer:** Indexed access uses an index structure to locate blocks/records quickly, useful when both sequential and random access are required.

---

## C) Directory Structures — Fully Solved

### Q5) Compare single-level, two-level, and tree directories.
**Answer:**
- **Single-level:** simplest; name conflicts; no grouping.
- **Two-level:** separate per user; reduces conflicts; limited sharing.
- **Tree:** hierarchical; supports grouping and scalable organization.

### Q6) Why avoid cycles in directory graphs?
**Answer:** Cycles can cause infinite traversal and complicate operations like backup, deletion, and size computation.

---

## D) Protection — Fully Solved

### Q7) Explain ACL and capability lists.
**Answer:**
- **ACL:** stored with object; specifies which users/groups have which permissions.
- **Capabilities:** stored with subjects; a capability is an unforgeable token granting rights to an object.

### Q8) Which is better for answering “who can access this file” and why?
**Answer:** ACL is better because permissions are directly listed per file, so you can inspect one ACL to see all authorized users/groups.

---

## E) Scenario Questions — Fully Solved

### Q9) Two processes open the same file and both write without coordination. What can go wrong and what OS tool helps?
**Answer:** Writes can interleave causing corruption or lost updates. File locking (exclusive locks for writers) or synchronization mechanisms prevent this.

### Q10) What is mounting? Give one example.
**Answer:** Mounting attaches a file system to a directory (mount point) so it becomes part of the directory tree. Example: mounting a USB drive at `/mnt/usb`.

---

## F) “Algorithmic” steps — Complete

### Q11) Algorithm: safe file update using temp file (concept)
**Solution:**
```text
open original for read
create temp file
copy + apply changes into temp
fsync temp (optional)
rename temp -> original (atomic in many FS)
```

**Why used:** reduces risk of partial updates if crash occurs.

---

## G) Bonus Exam Pack — Fully Solved

### Q12) Hard link vs soft link (symbolic link). What happens on deletion?
**Answer:**
- **Hard link:** another directory entry pointing to the same inode/FCB. File data is removed only when the link count reaches 0 *and* no process has it open.
- **Soft link (symlink):** a special file containing a pathname to the target. If the target is deleted, the symlink becomes “dangling” (broken).

**Exam point:** Deleting an open file typically removes the directory entry, but the OS keeps the inode and data until the last reference (open handle) is closed.
