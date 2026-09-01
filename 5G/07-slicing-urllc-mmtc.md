# 07 — Network Slicing, URLLC & mMTC

This chapter is where the three pillars from Chapter 1 (eMBB, URLLC, mMTC) and the architectural pieces from Chapters 2-6 all click together into 5G's actual headline capabilities.

## 7.1 Network Slicing — the feature that makes "one network, many use cases" real

**The core idea:** a **network slice** is a logically isolated, end-to-end "virtual network" running on top of the same shared physical infrastructure — its own configured set of 5GC functions (Chapter 2), its own QoS policies, potentially even dedicated or prioritized radio resources — customized for a specific purpose.

This is directly enabled by 5GC's Service-Based Architecture (Chapter 2.2): because network functions are software services rather than fixed hardware, an operator can spin up a differently-configured *combination* of AMF/SMF/UPF/PCF instances for each slice, rather than needing to build entirely separate physical networks (which is essentially what large enterprises/governments had to do before 5G if they wanted a dedicated, isolated network).

### How a device gets onto the right slice
- Each slice is identified by an **S-NSSAI** (Single Network Slice Selection Assistance Information).
- During Registration (Chapter 6.1), the UE can indicate which slice(s) it wants/is permitted to use.
- The **NSSF** (Chapter 2.4) is the function specifically responsible for figuring out which slice(s) a given device/session should be routed to, and can direct the AMF/SMF selection accordingly.

### Example slice configurations
| Slice example | Tuned for |
|---|---|
| Public mobile broadband slice | Regular phone traffic — general eMBB, best-effort priority |
| Factory automation slice | URLLC — guaranteed low latency, high reliability, possibly a dedicated local UPF right at the factory |
| Smart-meter slice | mMTC — huge device count, minimal per-device data, extreme power efficiency, deprioritized latency |

A single physical radio site and shared spectrum can serve devices on all three simultaneously, each getting fundamentally different treatment — this is precisely the flexibility problem identified in Chapter 1.6 that no single fixed configuration could solve.

## 7.2 URLLC — Ultra-Reliable Low-Latency Communication

Recall the target from Chapter 1.6: **1ms user-plane latency**. Achieving this isn't one single feature — it's the combined effect of several things already covered:
- **Mini-slots** (Chapter 4.2) — scheduling doesn't wait for full slot boundaries
- **High numerology / short slots** (Chapter 3.3, 4.1) — shorter fundamental timing units
- **Fast/shortened HARQ, or HARQ-less transmission** with heavy coding redundancy instead (Chapter 5.5)
- **Edge-deployed UPF** (Chapter 2.7) — minimizes physical distance data has to travel
- **Preemptive scheduling** — the network can interrupt a lower-priority eMBB transmission already in progress to immediately insert a URLLC packet, rather than waiting for the eMBB transmission to naturally finish

### Reliability, not just speed
The "UR" in URLLC is doing real work — URLLC targets extremely high reliability (commonly cited target: 99.999% packet delivery success within the latency budget), not just fast average-case latency. This is why PDCP packet duplication (Chapter 5.3) exists — sending the same critical packet over two independent radio paths dramatically reduces the odds that *both* fail simultaneously, trading some efficiency for much stronger reliability guarantees.

## 7.3 mMTC — Massive Machine-Type Communication

Recall the target from Chapter 1.6: **1,000,000 devices per km²** — roughly two orders of magnitude beyond what LTE was ever designed to comfortably handle in one area. Key techniques:

- **Extremely infrequent, tiny data transmissions** — most mMTC devices (a soil moisture sensor, a parking sensor) might send a few bytes once an hour or once a day, not a continuous stream.
- **Grant-free / contention-based access** — rather than every device going through a full scheduling request/grant handshake (efficient for a phone doing sustained data, wasteful overhead for a device sending 20 bytes), devices can transmit directly using shared, contention-based resources, accepting some collision risk in exchange for far less signaling overhead per transmission.
- **Extended sleep cycles** — building on DRX (LTE notes Ch. 6.2), NR devices in mMTC scenarios can sleep for extremely long stretches (hours), waking only briefly.
- **RedCap devices** (Reduced Capability — 3GPP Release 17, Chapter 1.5) — a category of simplified, cheaper 5G devices with reduced bandwidth/antenna requirements, aimed squarely at mid-tier IoT use cases that don't need full eMBB-grade radio capability but still want to sit on the same unified 5G network rather than a totally separate IoT-only technology.

### Related but distinct: LTE-M and NB-IoT
Worth noting these actually originated as **LTE extensions**, not NR features (LTE-Advanced Pro, LTE notes Ch. 7.4) — but they remain part of the broader "5G era" IoT strategy and continue to be supported/evolved alongside NR-based mMTC, since many low-end IoT deployments don't need or want a full NR radio.

## 7.4 Deep dive: the tension slicing has to manage

It's worth being explicit about the tradeoff that makes slicing hard in practice: eMBB wants to maximize total throughput (often by densely packing scheduling decisions and letting transmissions run efficiently to completion), while URLLC wants to interrupt whatever's happening the instant a latency-critical packet needs to go — and mMTC wants to minimize per-device signaling overhead above almost everything else. A scheduler serving all three slices on the same shared spectrum has to constantly arbitrate between these genuinely competing goals in real time, subframe by subframe (or slot/mini-slot by slot/mini-slot) — this scheduling complexity is a substantial part of why "true" multi-slice, all-pillars-at-once 5G deployments took years longer to mature than the initial eMBB-only, NSA-mode rollouts most people experienced first.

---

## Quick recap
- Network Slicing = logically isolated virtual networks on shared infrastructure, enabled by 5GC's service-based architecture (Ch. 2); NSSF routes devices to the right slice via S-NSSAI
- URLLC's 1ms/99.999% targets come from combining mini-slots, high numerology, fast/skipped HARQ, edge UPF placement, preemptive scheduling, and packet duplication — not any single feature
- mMTC handles massive device density via tiny/rare transmissions, grant-free access, extended sleep, and simplified RedCap devices — with LTE-M/NB-IoT as related, LTE-origin siblings
- The three pillars have genuinely competing scheduling needs, which is why real multi-slice 5G took longer to mature than initial eMBB-focused rollouts

**Next →** [08 - Key Formulas & Parameters](08-key-formulas-parameters.md)
