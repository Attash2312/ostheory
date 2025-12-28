# Chapter 19 — Fully Solved Questions (Networks & Distributed Systems)

All questions include complete answers.

---

## A) Core Theory — Solved

### Q1) Differentiate latency and bandwidth.
**Answer:** Latency is delay before data starts arriving; bandwidth is the data rate once transfer is underway.

### Q2) TCP vs UDP (3–5 key points).
**Answer:** TCP is connection-oriented, reliable, ordered, congestion-controlled. UDP is connectionless, best-effort, unordered, low overhead.

---

## B) Sockets — Solved

### Q3) Explain `bind`, `listen`, `accept` for a TCP server.
**Answer:**
- `bind`: attach server socket to local IP/port.
- `listen`: mark it as passive to accept connections.
- `accept`: create a new connected socket per client.

---

## C) RPC — Solved

### Q4) Why can RPC calls be executed twice? How to handle?
**Answer:** Retries after timeout can cause duplicates if server executed the request but reply was lost. Handle via request IDs + duplicate suppression, or ensure operation is idempotent.

---

## D) Numericals — Fully Solved

### Q5) Transmission time
Send **10 MiB** over a **20 Mbps** link (ignore latency).

**Solution:**
- 10 MiB = $10\times 2^{20}$ bytes = 10,485,760 bytes.
- Bits = $10,485,760\times 8 = 83,886,080$ bits.
- Time = bits / rate = $83,886,080 / 20,000,000 \approx 4.194$ s.

**Final:** about **4.19 seconds**.

---

## E) Algorithms / Pseudocode — Complete

### Q6) Simple TCP echo server loop (concept)
```text
server():
  s = socket(TCP)
  bind(s, port)
  listen(s)
  while true:
    c = accept(s)
    spawn_thread(handle_client, c)

handle_client(c):
  while data = recv(c):
    send(c, data)
  close(c)
```

### Q7) RPC request with duplicate suppression
```text
client_call(args):
  id = new_request_id()
  send(id, args)
  wait for reply with timeout
  if timeout: resend(id, args)

server_on_request(id, args):
  if id in reply_cache:
    resend cached reply
  else:
    result = procedure(args)
    cache(id, result)
    send reply
```

---

## F) DFS — Solved

### Q8) Why does caching complicate consistency in DFS?
**Answer:** Multiple clients may cache stale copies; updates must invalidate/refresh caches, requiring protocols (callbacks, leases, versioning) to keep consistency.

---

## G) Bonus Exam Pack — Fully Solved

### Q9) Explain “at-least-once” vs “at-most-once” RPC semantics.
**Answer:**
- **At-least-once:** client retries on timeout; server may execute the request multiple times (duplicates possible).
- **At-most-once:** system ensures request is executed 0 or 1 times using request IDs + duplicate suppression; more state needed.

**Exam tip:** If an operation is **idempotent** (same effect if repeated), at-least-once is often acceptable.

---

### Q10) Lamport logical clocks: rules (short)
**Answer:**
1) Increment local clock before each event.
2) On send message: include timestamp.
3) On receive message with timestamp $t$: set local clock to $\max(local, t) + 1$.

---

### Q11) Solved example: Lamport timestamps with one message
Process P1 events: a, b (send to P2), c
Process P2 events: d (receive from P1), e

Assume both clocks start at 0.

**Solution:**
- P1.a: increment → 1
- P1.b (send): increment → 2, message carries 2
- P1.c: increment → 3

- P2.d (receive msg=2): local=0 ⇒ set to max(0,2)+1=3
- P2.e: increment → 4

**Final timestamps:**
- P1: a=1, b(send)=2, c=3
- P2: d(recv)=3, e=4

---

### Q12) Numerical: time for request/response (simple)
Client sends a **4 KiB** request and receives an **8 KiB** response.
Link bandwidth = **10 Mbps**, RTT (propagation + switching) = **40 ms**.
Assume transmission time adds on top of RTT, and ignore processing time.

**Solution:**
- Total data = 12 KiB = $12\times 1024 = 12{,}288$ bytes = $98{,}304$ bits.
- Transmission time = $98{,}304 / 10{,}000{,}000 \approx 0.0098304$ s ≈ 9.83 ms.
- Total ≈ RTT + Tx = 40 ms + 9.83 ms = 49.83 ms.

**Final:** about **49.8 ms**.
