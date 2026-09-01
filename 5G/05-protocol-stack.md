# 05 — Protocol Stack

## 5.1 The layer stack — one new layer added

NR's stack is LTE's stack (LTE notes Ch. 5.1) plus exactly one new layer inserted at the top:

```
   ┌─────────────────────────┐
   │   IP / Applications      │
   ├─────────────────────────┤
   │   SDAP  ← NEW               │   Service Data Adaptation Protocol
   ├─────────────────────────┤
   │   PDCP                    │   Same role as LTE
   ├─────────────────────────┤
   │   RLC                     │   Same role as LTE
   ├─────────────────────────┤
   │   MAC                     │   Same role as LTE, faster/more flexible scheduling
   ├─────────────────────────┤
   │   PHY                     │   Chapters 3-4's scalable numerology, mmWave/MIMO
   └─────────────────────────┘
```

Plus **RRC** alongside, same control-plane-only role as in LTE.

## 5.2 SDAP (Service Data Adaptation Protocol) — the new layer

**Why it exists:** recall from the LTE notes (Ch. 2.6) that LTE bearers are tied to a QCI (QoS Class Identifier) — the QoS treatment is baked into the bearer itself. 5G introduces a related-but-different concept called a **QoS Flow** (Chapter 6 covers this terminology in detail), and SDAP's job is to **map QoS Flows onto the correct underlying radio bearer**, marking each packet with the right QoS Flow Identifier so the network can apply appropriate handling. This extra mapping layer exists specifically because 5G decouples "how the core network thinks about QoS" (QoS Flows, managed by SMF/PCF — Chapter 2) from "how the radio actually carries it" (radio bearers) more explicitly than LTE did — giving the network more flexibility to remap traffic to different radio bearers without disrupting the core network's view of the session.

## 5.3 PDCP — mostly unchanged, one new trick

Same core responsibilities as LTE (header compression, ciphering, integrity protection, in-order delivery — LTE notes Ch. 5.2), with one addition relevant to **Dual Connectivity** (Chapter 6): PDCP in NR can perform **packet duplication** — sending the same packet over two different radio links simultaneously (e.g., via two different cells) specifically to boost reliability for URLLC traffic, where losing even one packet matters far more than the wasted redundant capacity.

## 5.4 RLC — one deliberate simplification

LTE's RLC (LTE notes Ch. 5.3) handled both segmentation *and* concatenation (combining multiple small packets into one larger transmission) to make efficient use of scheduled resources. NR's RLC **drops concatenation** — segmentation only — and pushes that job down into the MAC layer instead. This was a deliberate simplification to reduce processing latency (each removed processing step matters when you're chasing NR's 1ms latency target), taking advantage of the fact that MAC-layer multiplexing can handle it just as well without RLC needing to.

The three modes (TM/UM/AM) from LTE carry over conceptually unchanged.

## 5.5 MAC — same job, much tighter timing

Functionally identical role to LTE (scheduling + HARQ + multiplexing — LTE notes Ch. 5.4), but operating against NR's much more flexible timing:
- Scheduling can happen per-slot *or* per-mini-slot (Chapter 4.2), not just per fixed 1ms subframe
- **HARQ round-trip times shrink dramatically** at higher numerologies, since a HARQ round-trip is fundamentally tied to slot duration, and NR slots can be as short as 0.125ms (μ=3) vs LTE's fixed 0.5ms slot
- NR also supports **HARQ feedback disabling** for select traffic types where waiting even for a fast HARQ round-trip is unacceptable for the latency budget — relying instead on RLC's higher-layer reliability mechanisms or simply on very robust initial transmission (heavy coding redundancy) to avoid needing a retry at all

## 5.6 PHY — Chapters 3 & 4, formalized

Everything already covered: scalable numerology, flexible frame structure, mini-slots, Massive MIMO, beamforming, extended modulation up to 1024-QAM, Bandwidth Parts. NR also uses a different, more modern channel coding scheme than LTE's Turbo Codes (LTE notes Ch. 5.5):
- **LDPC (Low-Density Parity-Check) codes** for user data — chosen for better performance at the very high data rates NR targets, and for being more parallelizable in hardware (helps meet low-latency processing targets)
- **Polar codes** for control channel information — chosen for excellent performance specifically at the very short block lengths typical of control signaling

## 5.7 Deep dive: QoS Flow vs Bearer — the terminology shift, precisely

This is worth being precise about since it's a common point of confusion:

- In **LTE**, the bearer *is* the unit of QoS — one bearer, one QCI, and the radio/core network machinery is built directly around bearers end-to-end.
- In **NR/5GC**, the **QoS Flow** (identified by a **5QI**, the evolved version of LTE's QCI concept) is the *core network's* unit of QoS — defined between the UE and the UPF. The **radio bearer** is a separate, lower-level concept that SDAP maps QoS Flows onto. A single radio bearer *can* carry multiple QoS Flows if the network decides that's efficient, which LTE's tighter bearer-to-QCI coupling didn't allow.

This extra indirection (QoS Flow → SDAP → Radio Bearer) is what lets 5GC and NR evolve somewhat independently of each other — the core network's view of "what QoS treatment does this traffic need" doesn't have to be rigidly locked to exactly how the radio layer currently happens to be carrying it.

---

## Quick recap
- NR's stack adds SDAP (new) above PDCP, mapping QoS Flows to radio bearers
- PDCP gains packet duplication for reliability (URLLC/Dual Connectivity); RLC drops concatenation (pushed to MAC) to cut latency
- MAC's scheduling/HARQ now operates at slot *and* mini-slot granularity, with dramatically shorter HARQ round-trips at high numerology
- PHY moves to LDPC (data) and Polar (control) coding, replacing LTE's Turbo Codes
- QoS Flow (core network concept, 5QI) vs Radio Bearer (radio concept) are now distinct, unlike LTE's tightly-coupled bearer/QCI — SDAP bridges them

**Next →** [06 - Call & Session Procedures](06-call-procedures.md)
