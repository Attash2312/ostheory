# Chapter 18 — Fully Solved Questions (Virtual Machines)

All questions include complete answers.

---

## A) Core Theory — Solved

### Q1) Define virtualization and list 4 benefits.
**Answer:** Virtualization abstracts hardware to run multiple isolated OS instances on one machine. Benefits: consolidation, isolation, portability (images), testing/snapshots.

### Q2) Differentiate Type 1 and Type 2 hypervisors.
**Answer:** Type 1 runs directly on hardware and manages VMs; Type 2 runs on a host OS as an application.

---

## B) CPU/Memory/I/O — Solved

### Q3) Explain trap-and-emulate.
**Answer:** Guest runs at lower privilege; privileged/sensitive operations trap to hypervisor which emulates the instruction safely.

### Q4) Shadow page tables vs nested paging (EPT/NPT).
**Answer:** Shadow page tables are maintained by hypervisor (software overhead on updates). Nested paging uses hardware to translate GPA→HPA, reducing overhead.

### Q5) Give three I/O virtualization approaches.
**Answer:** device emulation, paravirtual drivers, direct assignment (passthrough).

---

## C) Numericals — Fully Solved (simple exam-style)

### Q6) VM consolidation utilization
A server has 16 cores. Each VM needs 2 cores on average. How many VMs can you place if you cap CPU usage at 75% to avoid overload?

**Solution:**
- Max usable cores = $0.75\times 16 = 12$ cores.
- Each VM uses 2 cores ⇒ VMs = $\lfloor 12/2 \rfloor = 6$.

**Final:** **6 VMs**.

---

## D) Algorithms / Pseudocode

### Q7) Simplified trap handling flow
```text
execute_guest_instruction(instr):
  if instr is privileged_or_sensitive:
    trap_to_hypervisor(instr)
  else:
    run_natively(instr)

trap_to_hypervisor(instr):
  emulate(instr)
  return_to_guest()
```

---

### Q8) High-level I/O path in emulation
```text
guest_driver_write(dev, data):
  write_to_mmio_port(dev, data)  # triggers VM exit

hypervisor_on_vmexit():
  decode_io_request()
  emulate_device_or_forward()
  resume_guest()
```

---

## E) Security — Solved

### Q9) Why is the hypervisor part of the TCB?
**Answer:** Because it enforces isolation; compromise of hypervisor can compromise all VMs. Therefore it must be small, hardened, and patched.

---

## F) Bonus Exam Pack — Fully Solved

### Q10) Full virtualization vs paravirtualization
**Answer:**
- **Full virtualization:** guest OS runs unmodified; hypervisor traps/emulates privileged operations (often with hardware assist). Easier compatibility.
- **Paravirtualization:** guest OS is modified to use hypercalls/paravirtual drivers to cooperate with hypervisor. Often higher performance, but requires guest changes.

---

### Q11) Algorithm: high-level live migration (pre-copy) steps
```text
precopy_migrate(VM, src, dst):
  while dirty_rate is high:
    copy all memory pages from src to dst
    track dirty pages
    copy dirty pages again (iteration)
  stop VM briefly (downtime)
  copy remaining dirty pages + CPU/device state
  resume VM on dst
```

**Exam note:** goal is minimal downtime by copying most pages while VM runs.

---

### Q12) Numerical: nested paging extra memory accesses (intuition)
Assume a TLB miss. Without nested paging, a page-table walk costs **4** memory accesses.
With nested paging, each guest page-table access itself needs translation, doubling the walk to **8** memory accesses.

If memory access time is **100 ns**, estimate page-walk time in both cases (ignore caching).

**Solution:**
- Without nested paging: $4 \times 100\,ns = 400\,ns$.
- With nested paging: $8 \times 100\,ns = 800\,ns$.

**Final:** **400 ns vs 800 ns** (nested paging roughly doubles walk cost on TLB miss in this simplified model).
